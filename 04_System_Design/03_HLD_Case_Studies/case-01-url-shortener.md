# HLD — URL Shortener (TinyURL / bit.ly)

> The best first case study: small enough to finish in 45 minutes, and it still exercises estimation, key generation, caching, sharding and analytics.

---

## 1. Requirements

**Functional**
- Given a long URL, return a short one.
- Visiting the short URL redirects to the long one.
- Optional **custom alias**.
- Optional **expiry**.
- Basic click analytics.

**Non-functional**
- **Read-heavy**: ~100:1 reads to writes.
- Redirects must be fast — **p99 under 100 ms**, because a redirect sits in front of a page load.
- High availability: a dead shortener breaks every link ever shared.
- Short codes must not be guessable in bulk (to limit enumeration of private links).

**Out of scope:** user accounts beyond an API key, link previews, malware scanning.

---

## 2. Estimation

```
WRITES   100M new URLs/month ÷ 2.5×10^6 s  = 40/s      peak ×3 = 120/s
READS    40 × 100                           = 4,000/s   peak   = 12,000/s

STORAGE  record ≈ 500 B (code, long URL, owner, timestamps, expiry)
         100M × 500 B = 50 GB/month → 3 TB over 5 years → ~9 TB with 3× replication

BANDWIDTH  reads 12,000 × 500 B ≈ 6 MB/s   — trivial

CACHE    hot 20% of a month's links: 20M × 500 B ≈ 10 GB  → fits one Redis node
```

**What the numbers decide:** 12,000 reads/second against 120 writes/second means a cache in front of the store is the single highest-leverage component. 9 TB means sharding is needed eventually but not on day one. No single number here demands anything exotic.

---

## 3. API

```
POST /v1/urls
  { "longUrl": "...", "alias": "optional", "expiresAt": "optional" }
  → 201 { "shortUrl": "https://sho.rt/aB3xK9", "code": "aB3xK9" }

GET  /{code}
  → 302 Found, Location: <longUrl>     (or 301, see below)

GET  /v1/urls/{code}/stats
  → 200 { "clicks": 1523, "createdAt": "...", "topReferrers": [...] }

DELETE /v1/urls/{code}
```

**302 vs 301 is a real decision.** A **301 (permanent)** lets the browser cache the redirect, which removes load from you — and also removes your analytics, because subsequent visits never reach your server. A **302 (temporary)** means every click hits you: more load, complete analytics, and the ability to change or revoke the target. **Choose 302 when analytics or revocation matter**, which for a link shortener they usually do. Saying this trade-off aloud is the point.

Writes need authentication (an API key) and rate limiting; reads are public.

---

## 4. Short code generation

Length: with a base62 alphabet (`a-zA-Z0-9`), **7 characters give 62⁷ ≈ 3.5 × 10¹²** codes. At 100M/month that is ~2,900 years of supply. Six characters gives 56 billion, which is enough for a decade — 7 is the safe choice.

Three approaches:

**(a) Hash and truncate.** `base62(md5(longUrl))[:7]`. Deterministic, so the same URL yields the same code — free deduplication. But collisions must be handled: on insert, if the code exists with a *different* URL, append a salt and retry. Collision probability by the birthday bound is small but non-zero at scale.

**(b) Counter + base62 encode.** A globally increasing counter, encoded to base62. No collisions ever, and codes are dense (short). **But codes are sequential and therefore enumerable** — a competitor can crawl every link. Fix by encrypting the counter with a format-preserving cipher or a Feistel network before encoding, which keeps uniqueness and destroys the ordering.

**(c) Pre-generated key pool.** A **Key Generation Service** pre-computes random unique codes into a table, and application servers take blocks of them. Generation is offline, so the write path never collides or blocks. Each server holds a block in memory and requests another when it runs low.

**The recommendation:** (c) for a system at this scale, or (b) with encryption. State the reason: it removes collision handling from the hot path entirely, and makes the write path a single insert.

**The KGS is itself a single point of failure**, so it needs replication and each server must hold a buffer of unused keys to survive a KGS outage. Keys handed out and then lost (server crash) are simply wasted — with 3.5 × 10¹² codes, that is acceptable.

---

## 5. Data model

```sql
CREATE TABLE urls (
    code        VARCHAR(8)  PRIMARY KEY,     -- the shard key
    long_url    TEXT        NOT NULL,
    owner_id    BIGINT,
    created_at  TIMESTAMP   NOT NULL,
    expires_at  TIMESTAMP,
    click_count BIGINT      DEFAULT 0        -- denormalised, updated async
);
CREATE INDEX idx_owner ON urls(owner_id);
```

**Access pattern:** every read is a point lookup by `code`. No joins, no ranges, no ad-hoc queries. **That derives a key-value store** — DynamoDB or Cassandra, partitioned on `code` — though a sharded relational database is equally defensible at this size, and simpler to operate.

**`click_count` is deliberately denormalised** and updated asynchronously. Incrementing a row on every redirect would put 12,000 writes/second on the hottest rows, which is exactly the wrong load for the store.

---

## 6. Architecture

```
                         ┌──────────┐
   Client ──── DNS ─────►│   CDN    │  (only for the static landing pages)
                         └────┬─────┘
                              ▼
                     ┌────────────────┐
                     │ Load Balancer  │
                     └───────┬────────┘
                             ▼
              ┌──────────────────────────────┐
              │  API servers (stateless)     │
              └───┬───────────┬──────────┬───┘
                  │           │          │
        ┌─────────▼──┐  ┌─────▼──────┐  ┌▼──────────────────┐
        │   Redis    │  │  Key Gen   │  │  Kafka (clicks)   │
        │  (cache)   │  │  Service   │  └─────────┬─────────┘
        └─────┬──────┘  └─────┬──────┘            │
              │ miss          │ blocks of codes   ▼
        ┌─────▼──────────────▼────────┐   ┌──────────────────┐
        │   URL store (sharded KV)    │   │ Analytics workers│
        └─────────────────────────────┘   └────────┬─────────┘
                                                   ▼
                                          ┌──────────────────┐
                                          │ Analytics store  │
                                          └──────────────────┘
```

**Read path:** `GET /{code}` → check Redis → on hit, 302 immediately; on miss, read the store, populate the cache, 302. Emit a click event to Kafka **asynchronously** — the redirect never waits for it.

**Write path:** validate the URL → take a code from the in-memory block (or use the custom alias, checking uniqueness) → insert → return.

---

## 7. Deep dives

### Caching
Cache-aside with Redis, TTL of an hour, **LRU eviction**. Expected hit ratio well above 90%, because link popularity is heavily skewed — a handful of viral links dominate traffic.

**Cache stampede** on a viral link expiring is the risk: thousands of concurrent requests miss at once. Mitigate with request coalescing (the first miss fetches, the rest wait) and jittered TTLs. Because entries are immutable once written (the long URL does not change), you can also use a very long TTL and invalidate only on delete.

### Sharding
Shard by **`code`**, hashed. It is the only access key, so every read hits exactly one shard — no scatter-gather. Distribution is uniform because codes are random. Use consistent hashing or a fixed partition count so adding capacity does not remap everything.

Custom aliases go through the same path; the uniqueness check is a conditional insert on the owning shard.

### Analytics without hurting the redirect
Click events go to Kafka, keyed by `code`. Workers aggregate per code per hour and write rollups. Exact per-click storage for 400M clicks/day is expensive and rarely needed; **pre-aggregated counters plus HyperLogLog for unique visitors** keeps this in kilobytes per link per day.

**This is where estimation paid off:** 12,000 synchronous counter increments per second would have been the system's bottleneck; the queue removes it entirely.

### Expiry and cleanup
Store `expires_at`, check it on read (returning 410 Gone), and let a background job delete expired rows. **Checking on read means correctness does not depend on the cleanup job**, which is the same pattern as the booking-hold expiry in `02_LLD_OOD`.

---

## 8. Bottlenecks and failure modes

| Risk | Impact | Mitigation |
|---|---|---|
| Cache cluster failure | 12,000 reads/s hit the store cold | Multi-node Redis with replicas; the store is sized to survive it, degraded |
| Viral link (hot key) | One shard and one cache key saturate | The cache absorbs it; add a small in-process cache on each API server for the top keys |
| KGS unavailable | Writes fail | Replicate; each server buffers a block of unused codes |
| Enumeration attack | Private links discovered | Random codes (not sequential); rate limit reads per IP |
| Malicious URLs | Reputational and legal risk | Async scanning against a blocklist; a warning interstitial for flagged links |
| Analytics queue backlog | Stale counts | Acceptable — it does not affect redirects. Alert on consumer lag |

**What breaks first as traffic grows 10×:** nothing structural. The cache scales horizontally, the store shards by code, and writes are trivial. This design is genuinely comfortable to 120,000 reads/second, which is a good thing to be able to say.

---

## 9. Trade-offs to state out loud

| Choice | Alternative | Why this one |
|---|---|---|
| Pre-generated key pool | Hash-and-truncate | Removes collision handling from the write path |
| 302 redirect | 301 | Preserves analytics and the ability to revoke |
| Key-value store | Relational | The only access pattern is a point lookup by code |
| Async click counting | Synchronous increment | 12,000 writes/s on hot rows would dominate the design |
| Cache-aside | Write-through | Records are immutable after creation, so lazy population is sufficient |

---

## 10. Practice

- [ ] Design from scratch in 45 minutes, speaking aloud
- [ ] Add per-user dashboards with top links (what changes in the data model?)
- [ ] Support link previews requiring a fetch of the target page
- [ ] Make the system multi-region — where does the KGS live?
- [ ] Work out the birthday-bound collision probability for hash-and-truncate at 10¹¹ links
