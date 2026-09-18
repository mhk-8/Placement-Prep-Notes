# HLD — Web Crawler

> A system defined by its **politeness constraints** as much as its scale. It is also the clearest real use of a Bloom filter, and a good case study for frontier design and trap avoidance.

---

## 1. Requirements

**Functional**
- Crawl the web starting from a seed set.
- Extract links and enqueue new URLs.
- Store page content for downstream indexing.
- Re-crawl pages periodically, with frequency reflecting how often they change.

**Non-functional**
- **1 billion pages per month.**
- **Politeness**: respect `robots.txt`, and never overwhelm a single host.
- Avoid duplicate work: the same URL, and the same *content* under different URLs.
- Robust against traps: infinite calendars, session-id URLs, deliberate loops.
- Extensible: adding image or PDF crawling should not be a redesign.

---

## 2. Estimation

```
RATE       1B pages/month ÷ 2.5×10^6 s   = 400 pages/second
           peak / catch-up                = ~1,000/second

BANDWIDTH  average page ~500 KB (HTML + assets we fetch)
           400 × 500 KB                   = 200 MB/s = 1.6 Gbps

STORAGE    raw HTML compressed ~100 KB/page
           1B × 100 KB                    = 100 TB/month

URL FRONTIER  say 10B known URLs × 100 B  = 1 TB   ← too big for memory
SEEN SET      10B URLs
              exact hash set: 10B × 16 B  = 160 GB
              Bloom filter at 1%: 10B × 9.6 bits ≈ 12 GB   ← 13× smaller
```

**What the numbers decide:** 400 pages/second is modest, so throughput is not the problem — **coordination** is. Ten billion known URLs will not fit in one process's memory, so the frontier is a distributed, persistent structure. And the seen-set size is what makes a Bloom filter genuinely worthwhile rather than a party trick.

---

## 3. Architecture

```
   ┌─────────────────┐
   │  URL Frontier   │  prioritised, politeness-aware queues
   └────────┬────────┘
            │ next URL (respecting per-host delay)
            ▼
   ┌─────────────────┐    ┌──────────────────┐
   │  Fetcher pool   │───►│ DNS resolver     │ (cached — DNS is a bottleneck)
   └────────┬────────┘    └──────────────────┘
            │ raw HTML
            ▼
   ┌─────────────────┐    ┌──────────────────┐
   │ Content dedupe  │───►│ Content hash set │  (simhash / checksum)
   └────────┬────────┘    └──────────────────┘
            │ new content
            ▼
   ┌─────────────────┐    ┌──────────────────┐
   │ Parser / link   │───►│  Content store   │ (object storage)
   │   extractor     │    └──────────────────┘
   └────────┬────────┘
            │ extracted URLs
            ▼
   ┌─────────────────┐    ┌──────────────────┐
   │ URL filter +    │───►│ Seen-URL set     │  (Bloom filter + exact store)
   │ normaliser      │    └──────────────────┘
   └────────┬────────┘
            │ genuinely new URLs
            └────────────► back to the Frontier
```

It is a **loop**, and the interesting components are the frontier and the two dedup stages.

---

## 4. The URL frontier — the core of the design

The frontier must satisfy two conflicting requirements simultaneously:

- **Priority:** important pages (high PageRank, news sites, frequently-changing) should be crawled sooner.
- **Politeness:** no single host may be hit too frequently, regardless of how many of its URLs are queued.

**The standard two-stage design** (from the Mercator crawler):

```
  FRONT QUEUES (priority)          BACK QUEUES (politeness)
  ┌──────────┐                     ┌──────────────────────┐
  │ prio 1   │──┐                  │ queue A → host1.com  │◄── one host per queue
  ├──────────┤  │   selector       ├──────────────────────┤
  │ prio 2   │──┼──────────────►   │ queue B → host2.com  │
  ├──────────┤  │  (biased to      ├──────────────────────┤
  │ prio 3   │──┘   high priority) │ queue C → host3.com  │
  └──────────┘                     └──────────────────────┘
                                             │
                                   ┌─────────▼──────────┐
                                   │ heap of (next_ok_  │  when may this host
                                   │ time, queue_id)    │  be fetched again?
                                   └────────────────────┘
```

- **Front queues** hold URLs by priority. A biased selector pulls mostly from high-priority queues but not exclusively, so low-priority URLs are not starved.
- **Back queues** each hold URLs for exactly **one host**. A worker takes from a back queue, so a host is never fetched by two workers at once.
- A **min-heap keyed by "earliest next fetch time"** tells workers which back queue is ready. The delay per host is typically `10 × the last response time`, which automatically backs off from slow servers.

**That last detail is elegant and worth stating:** politeness delay proportional to response time means you are gentlest with the servers that are already struggling.

---

## 5. Deduplication — two different problems

### URL deduplication
Ten billion known URLs. Checking "have I seen this?" on every extracted link, at maybe 50 links per page and 400 pages/second, is 20,000 lookups/second against a 10-billion-entry set.

**Bloom filter in front of an exact store.** The filter says "definitely not seen" for most URLs — those go straight to the frontier. For the small fraction it says "possibly seen", consult the exact store.

**The asymmetry is exactly right:** a false positive means skipping a URL that was in fact new — a small loss of coverage. A false negative would mean re-crawling, which is merely wasteful. Neither is a correctness failure, which is what makes the approximation acceptable.

**URL normalisation must happen first**, or dedup fails: lowercase the host, strip the default port, remove fragments, sort query parameters, strip known session ids, resolve relative paths. `HTTP://Example.com:80/a/../b?z=1&a=2#top` and `http://example.com/b?a=2&z=1` are the same page.

### Content deduplication
Different URLs frequently serve identical or near-identical content — mirrors, print versions, session-id variants.

- **Exact duplicates:** hash the normalised content (MD5/SHA) and check a set.
- **Near-duplicates:** **simhash** or MinHash produces a fingerprint where similar documents have similar fingerprints, so near-duplicates are detectable by Hamming distance. This is what catches boilerplate-heavy near-identical pages.

Detecting content duplicates *after* fetching does not save bandwidth, but it saves storage and downstream indexing — which at 100 TB/month is significant.

---

## 6. Deep dives

### robots.txt
Fetch and cache per host (typically 24 hours). Honour `Disallow`, `Crawl-delay` and sitemap directives. A crawler that ignores robots.txt gets blocked, reported and sometimes sued — this is not optional politeness, it is the operating licence.

### DNS
DNS resolution is a **genuine bottleneck**: it is synchronous, and a naive crawler spends a large fraction of its time waiting on it. Run a caching resolver, pre-resolve hosts when URLs are enqueued, and honour TTLs. Mentioning DNS as a bottleneck is a detail that distinguishes a considered answer.

### Traps
- **Infinite spaces** — calendars generating `?date=2099-12-31` forever. Mitigate with a **depth limit** and a **per-host page cap**.
- **Session ids in URLs** — every visit generates a new URL for the same page. Caught by normalisation plus content dedup.
- **Spider traps** — deliberately generated infinite link graphs. Caught by depth and host caps.
- **Huge files** — cap the download size and time out.

### Re-crawl policy
Pages change at very different rates. Track observed change frequency per URL and schedule accordingly: a news homepage hourly, a static documentation page monthly. **Adaptive scheduling** — increase the interval when the page has not changed, decrease when it has — gets most of the benefit with simple logic.

### Distribution
Partition the frontier **by host hash**, so all URLs for one host live on one crawler node. That makes politeness a **local** decision requiring no coordination — which is the key property. Nodes exchange discovered URLs that belong to other partitions.

---

## 7. Bottlenecks and failure modes

| Risk | Impact | Mitigation |
|---|---|---|
| Politeness violation | Blocked, reported, or legal trouble | Per-host back queues; delay proportional to response time |
| Frontier size | 1 TB does not fit in memory | Persistent, partitioned by host; memory holds only the ready set |
| DNS latency | Fetchers idle | Caching resolver, pre-resolution |
| Spider traps | Infinite crawling of worthless pages | Depth limits, per-host caps, content dedup |
| Crawler node failure | Its hosts stop being crawled | Frontier is persistent; reassign the partition |
| Storage growth | 100 TB/month | Compress; retain only what indexing needs; tier aggressively |
| Bloom filter saturation | False positive rate degrades | Monitor fill ratio; use scalable (chained) Bloom filters |

---

## 8. Trade-offs to state

| Choice | Alternative | Why |
|---|---|---|
| Bloom filter + exact store | Exact store only | 12 GB versus 160 GB, and the error is on the harmless side |
| Two-stage frontier | One priority queue | A single queue cannot satisfy priority and politeness simultaneously |
| Partition by host | Partition by URL hash | Makes politeness a local decision with no coordination |
| Delay ∝ response time | Fixed delay | Automatically gentler with servers that are struggling |
| Simhash for near-duplicates | Exact hash only | Boilerplate-heavy near-duplicates are a large fraction of the web |
| Depth and host caps | Trusting the link graph | Traps are common and often deliberate |

---

## 9. Practice

- [ ] Design from scratch in 45 minutes
- [ ] Size the Bloom filter for 10B URLs at 0.1% and compare with 1%
- [ ] Design the URL normaliser: list every rule
- [ ] Design the adaptive re-crawl scheduler
- [ ] Explain how to add image crawling without redesigning the frontier
