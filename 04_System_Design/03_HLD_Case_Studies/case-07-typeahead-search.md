# HLD — Search Autocomplete (Typeahead)

> A deceptively small problem with a genuinely interesting data structure and an unusually tight latency budget. It is the best case study for showing that **precomputation beats computation** at read time.

---

## 1. Requirements

**Functional**
- As a user types a prefix, return the top 5–10 completions.
- Suggestions ranked by popularity (query frequency), optionally personalised.
- Suggestions update as query trends change — within hours, not seconds.

**Non-functional**
- **p99 under 100 ms**, and realistically closer to 50 ms — a suggestion that arrives after the user has typed the next character is useless.
- Very high read volume: a suggestion request **per keystroke**.
- Approximate freshness is fine; approximate ranking is fine.

---

## 2. Estimation — and why it forces precomputation

```
QUERIES        Say 5B searches/day ÷ 10^5 s = 50,000 searches/second
KEYSTROKES     average query ~20 characters, but debounced to ~4 requests
               50,000 × 4                    = 200,000 autocomplete QPS

CORPUS         ~100M distinct queries, average 20 bytes = 2 GB of raw strings
               a trie with top-k cached at each node ≈ 10–20 GB

WRITES         query logs: 50,000/s appended, aggregated in batch
```

**200,000 QPS with a 50 ms budget** rules out computing anything at request time. The answer must already exist and only need to be looked up. That single conclusion is the design.

---

## 3. The data structure

### A plain trie
Each node is a character; a path spells a prefix. Finding the node for a prefix is O(L) in the prefix length, independent of corpus size. Then you must find the top k completions in its subtree — which means **traversing the whole subtree**, and for a prefix like "a" that is most of the corpus.

### The trie with top-k cached at each node — the actual answer

**Store, at every node, the top k completions of that prefix.** A lookup becomes: walk L nodes, read the precomputed list, return. **O(L) total, with L ≤ 20 and no subtree traversal at all.**

```
        (root)
          │
          c ──► top5: ["cat", "car", "card", "care", "cart"]
          │
          a ──► top5: ["cat", "car", "card", "care", "cart"]
         ╱ ╲
        t   r ──► top5: ["car", "card", "care", "cart", "cargo"]
        │   │
     "cat"  d ──► top5: ["card", "cardio", "cards", ...]
```

**The cost is memory and update complexity.** Storing 5 strings at every node multiplies memory substantially — hence the 10–20 GB estimate. And a frequency change must propagate up the path from the leaf to the root, updating every ancestor's top-k list.

**That update cost is exactly why updates are batched**, which is the next decision.

### The alternative worth naming
For very large corpora, a **finite-state transducer** (as used by Lucene) compresses shared suffixes as well as prefixes, giving far smaller memory at some CPU cost. Mentioning it shows breadth; the cached-trie answer is what to build.

---

## 4. Architecture

```
  READ PATH  (must be trivial)
  ───────────────────────────
  Client ──debounced──► CDN/edge cache ──► Autocomplete servers
                                                  │
                                            in-memory trie
                                            (replicated, read-only)

  WRITE PATH  (batch, offline)
  ────────────────────────────
  Search queries ──► Kafka ──► Aggregation job (hourly)
                                    │  count queries, filter, rank
                                    ▼
                            ┌───────────────────┐
                            │  Trie builder     │  builds a new trie
                            └─────────┬─────────┘
                                      ▼
                            ┌───────────────────┐
                            │ Trie snapshot     │  versioned artefact
                            │ (object storage)  │
                            └─────────┬─────────┘
                                      ▼
                            servers load and swap atomically
```

**The read path and the write path are completely separated.** Read servers hold an immutable in-memory trie and serve lookups; they never write. The trie is rebuilt offline and shipped as a versioned artefact, which servers load and swap in atomically.

**This separation is the core design decision**, and it is worth stating as such: it makes reads trivially fast and trivially scalable (add read replicas, they are stateless apart from a read-only snapshot), and it confines all the complexity to an offline job where latency does not matter.

---

## 5. Deep dives

### Sharding the trie
20 GB does not fit comfortably in one process alongside everything else, and you want replication anyway.

**Shard by prefix.** Prefixes starting `a–f` on shard 1, `g–m` on shard 2, and so on. A request routes to exactly one shard by its first character or two — **no scatter-gather**, which is the property that keeps latency low.

**The distribution is uneven** — far more queries begin with "s" than "z" — so shard by *observed traffic* rather than alphabetically, splitting hot prefixes across more shards.

### Update frequency
Hourly rebuilds are typically enough: query popularity moves slowly. **Except for breaking news**, where a term goes from zero to dominant in minutes.

The practical answer is a **two-tier merge**: the batch trie (hourly, comprehensive) plus a small real-time layer of trending terms (streaming, minutes) merged at read time. The merge is cheap because the real-time layer is tiny.

### Caching
The prefix distribution is extremely skewed: short prefixes ("a", "th", "how") receive a large share of requests. **Cache prefix → results at the edge/CDN** with a short TTL. A high hit ratio here removes most traffic before it reaches the servers at all.

Because a suggestion list is not personalised in the base design, the cache key is just the prefix — which is what makes the hit ratio high. **Personalisation destroys cacheability**, which is the trade-off in the next section.

### Ranking
Base ranking is query frequency over a recent window, usually time-decayed so last month's trend fades. Additional signals: click-through rate on the suggestion (did the user actually pick it?), recency, and geography.

**Personalisation** — the user's own history, their location — means the answer differs per user and the shared cache stops working. The usual compromise: serve the shared top-k and **re-rank a small candidate set** on the client or at the edge using local history. That keeps the expensive part shared and the personal part cheap.

### Filtering
Offensive terms, spam and queries that are themselves sensitive must be excluded. This belongs in the **offline aggregation** step, not at read time — a blocklist applied while building the trie costs nothing at query time.

---

## 6. Bottlenecks and failure modes

| Risk | Impact | Mitigation |
|---|---|---|
| Hot prefix shard | One shard saturates | Shard by observed traffic; cache aggressively at the edge |
| Trie rebuild failure | Suggestions go stale | Versioned snapshots; servers keep serving the last good one |
| A bad snapshot deployed | Wrong or offensive suggestions everywhere | Validate the artefact before rollout; canary to a fraction of servers; keep the previous version for instant rollback |
| Memory pressure | Servers OOM as the corpus grows | Cap corpus size (drop long-tail queries), cap k, compress with an FST |
| Trending term missed | Breaking news not suggested | The real-time merge layer |
| Latency spike on swap | p99 breaches during snapshot load | Load into a second structure and swap the pointer atomically; never block requests |

**That last one is a good detail:** naively replacing the trie in place causes a stall on every server simultaneously. Build beside it, swap a reference, free the old one.

---

## 7. Trade-offs to state

| Choice | Alternative | Why |
|---|---|---|
| Top-k cached at every trie node | Traverse the subtree at query time | The 50 ms budget cannot afford a subtree walk for short prefixes |
| Offline batch rebuild | Incremental in-place updates | Updating ancestors on every query event is enormous write amplification for no user-visible benefit |
| Shard by prefix | Hash sharding | Prefix sharding keeps a request on one shard; hashing would scatter-gather |
| Shared (non-personalised) base results | Fully personalised | Shared results are cacheable; personalisation is added by re-ranking a small candidate set |
| Hourly freshness + real-time trending layer | Fully real-time | Popularity moves slowly; only breaking news needs minutes |
| Filtering offline | Filtering at read time | Free at query time, and easier to audit |

---

## 8. What this case study teaches generally

**Precomputation beats computation** whenever reads vastly outnumber writes and the read latency budget is tight. The same shape appears in the news feed (precomputed timelines), recommendations (precomputed candidate lists), and analytics dashboards (precomputed rollups).

The recognisable pattern: *move the work to a place where latency does not matter, and make the read path a lookup.*

---

## 9. Practice

- [ ] Design from scratch in 45 minutes
- [ ] Work out the memory for a 100M-query corpus with top-5 at each node, and reduce it
- [ ] Design the real-time trending layer and its merge
- [ ] Add personalisation without destroying the cache hit ratio
- [ ] Design the safe rollout of a new trie snapshot, including rollback
