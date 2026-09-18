# Caching

> Caching is the highest-leverage optimisation in most systems, because most systems are read-heavy and most reads are for a small fraction of the data. It is also where the subtlest bugs live, because a cache is a second copy of the truth.

---

## 1. Why caching works

Two empirical properties of real workloads:

- **Temporal locality** — recently accessed data is likely to be accessed again.
- **Skew (the 80/20 rule)** — a small fraction of items receives most of the traffic. Real distributions are often more extreme than 80/20; a Zipfian access pattern means the top 1% of items can account for 30–50% of requests.

Together these mean a cache holding a *small* fraction of the data can serve a *large* fraction of requests.

**The economics:** a memory reference is ~100 ns, an SSD read ~150 µs, a database query over the network ~1–10 ms. A cache hit is therefore 10,000× to 100,000× cheaper than a database round trip. Even a 50% hit ratio halves database load.

**The metric that matters is hit ratio.** A cache at 20% hit ratio is adding a network hop and a consistency problem in exchange for very little. Measure it; if it is low, either the working set does not fit or the access pattern has no locality, and the cache is the wrong tool.

---

## 2. Where caches live

Caching happens at every layer, and naming the layer is part of answering well.

| Layer | Example | Scope | Invalidation difficulty |
|---|---|---|---|
| **Client** | browser cache, mobile app store | one user | hardest — you cannot reach it |
| **CDN / edge** | CloudFront, Cloudflare | one region | purge API, or versioned URLs |
| **Reverse proxy** | Nginx, Varnish | one datacentre | easy |
| **Application** | in-process map, Caffeine | one instance | easy, but **not shared** |
| **Distributed cache** | Redis, Memcached | whole cluster | easy and shared |
| **Database** | buffer pool, query cache | one database | automatic |

**In-process vs distributed** is a real trade-off worth naming. An in-process cache has zero network latency but each instance has its own copy — so the effective hit ratio drops as you add instances, memory is duplicated, and invalidation must be broadcast. A distributed cache costs a network round trip (~0.5 ms) but is shared, consistent across instances, and survives an instance restart.

Many systems use both: a small in-process cache for very hot keys in front of a shared Redis, which is in front of the database. Each layer absorbs what the one behind it would have served.

---

## 3. Caching patterns

### Cache-aside (lazy loading) — the default

```
read(key):
    value = cache.get(key)
    if value is not None:
        return value                  # HIT
    value = db.get(key)               # MISS
    cache.set(key, value, ttl)
    return value

write(key, value):
    db.write(key, value)
    cache.delete(key)                 # INVALIDATE, do not update
```

**Advantages.** Only requested data is cached, so memory is spent on what is actually used. The cache can fail without breaking correctness — reads fall through to the database. It is simple and it is what most systems do.

**Disadvantages.** Every cache miss costs an extra round trip. The first request after a write is always a miss. And there is a subtle write race, below.

**Why `delete` rather than `set` on write:** deleting is idempotent and lets the next read repopulate from the authoritative source. Updating the cache directly opens a race where two concurrent writers set the cache in the opposite order from the database, leaving them permanently disagreed.

### Read-through
The cache itself knows how to load from the database; the application only talks to the cache. Cleaner application code, and the loader can coalesce concurrent misses for the same key. Requires cache support (a library or a managed cache).

### Write-through
Write to the cache and the database synchronously, in that order or as one operation.

**Advantage.** The cache is never stale. **Disadvantage.** Every write pays both latencies, and you cache data that may never be read — wasteful when the write:read ratio is high for a given key.

### Write-behind (write-back)
Write to the cache, acknowledge, and flush to the database asynchronously in batches.

**Advantage.** Very fast writes, and batching reduces database load dramatically (a view counter incremented a thousand times becomes one write of +1000).

**Disadvantage.** **A cache failure loses acknowledged writes.** Only acceptable where the data tolerates loss — view counts, analytics, non-critical counters — or where the cache itself is durable and replicated.

### Refresh-ahead
Proactively refresh entries that are about to expire and are predicted to be requested again. Avoids the latency spike of a miss on a hot key. Adds complexity and wasted refreshes for entries that are not re-requested.

---

## 4. Invalidation

> "There are only two hard things in computer science: cache invalidation and naming things."

Three approaches, in increasing order of precision and cost:

**TTL (time to live).** Every entry expires after a fixed period. Simple, self-healing, no coordination. The staleness window is bounded and explicit. **This is the right default**, and choosing the TTL is a product decision: how stale may this be? A user's display name, five minutes. A stock price, one second. A blog post, an hour.

**Explicit invalidation.** On write, delete the affected keys. Precise, but you must know every key affected — which becomes genuinely hard when caches hold derived or aggregated values. A change to one user's name may invalidate their profile, every post preview showing it, and a cached leaderboard.

**Versioned keys.** Include a version or a content hash in the key: `user:123:v7` or `/static/app.a3f9c2.js`. The old entry is never invalidated, it simply stops being requested and ages out. **This sidesteps invalidation entirely** and is why static assets are served with content-hashed filenames and effectively infinite TTLs.

A common combination: versioned keys for immutable content, TTL for everything else, explicit invalidation only for the small set of cases where staleness is unacceptable.

---

## 5. Eviction policies

When the cache is full, something must go.

| Policy | Rule | Fits |
|---|---|---|
| **LRU** | evict the least recently used | general purpose — the default |
| **LFU** | evict the least frequently used | stable popularity distributions |
| **FIFO** | evict the oldest inserted | rarely the right choice |
| **Random** | evict an arbitrary entry | surprisingly competitive, very cheap |
| **TTL-based** | evict expired entries first | time-sensitive data |

**LRU's weakness** is a scan: one large sequential pass over cold data evicts the entire hot set. Segmented LRU and adaptive policies (ARC, W-TinyLFU) exist to resist this.

**LFU's weakness** is that an item popular long ago can never be evicted. Windowed or decayed LFU fixes it.

Redis offers `allkeys-lru`, `allkeys-lfu`, `volatile-lru` (only keys with a TTL), `volatile-ttl` and `noeviction`. Choosing `noeviction` means writes fail when memory is full — occasionally what you want for a cache holding data that cannot be recomputed.

---

## 6. The failure modes

These three are the substance of a caching deep dive, and knowing them by name is a strong signal.

### Cache stampede (thundering herd)
A popular key expires. Every concurrent request for it misses simultaneously and hits the database at once. The database, sized for the cached load, collapses — and because it is now slow, the cache is never repopulated, so the stampede persists.

**Mitigations:**
- **Request coalescing / single-flight.** Let the first miss compute; the rest wait for its result. The single most effective fix.
- **Probabilistic early expiration.** Each request refreshes the entry with a probability that rises as the TTL approaches, so one request refreshes it before it expires while the rest still hit.
- **Lock on regeneration.** One holder recomputes; others serve the stale value briefly.
- **Jittered TTLs.** Never set the same TTL on many keys populated together, or they expire together.

### Cache penetration
Requests for keys that **do not exist** always miss and always reach the database. If attacker-controlled, this bypasses the cache entirely.

**Mitigations:** cache the negative result (store a tombstone with a short TTL), or front the cache with a **Bloom filter** (see `09`) that can say "definitely not present" without touching the database.

### Cache avalanche
A large fraction of the cache expires at once, or the cache cluster restarts cold. Everything falls through simultaneously.

**Mitigations:** jittered TTLs so expiries spread out; warming the cache before admitting traffic after a restart; a small in-process cache as a second line of defence; and rate limiting at the database to protect it from the flood.

---

## 7. Consistency between cache and database

The cache is a second copy, so the two can disagree. Two races are worth understanding precisely.

**Race 1 — the write/read interleaving on cache-aside.**
```
T1 (read):  cache miss, reads DB → gets value V1
T2 (write): writes V2 to DB, deletes cache key
T1 (read):  writes V1 into the cache      ← now permanently stale
```
The window is small but real. Mitigations: a short TTL bounds the damage; **delete-after-write plus a second delayed delete** ("delayed double delete") closes most of it; or serialise via a change-data-capture stream so invalidation always follows the database write.

**Race 2 — update-the-cache-on-write.**
Two writers set the database in one order and the cache in the other, leaving them disagreed indefinitely. **This is the reason to delete rather than update.**

**The honest framing for an interview:** you cannot have a cache and strict consistency without paying for it (distributed locks, or a transactional outbox plus CDC). What you can do is **bound the staleness** with a TTL and choose a window the product tolerates. Saying that explicitly — "we accept up to 60 seconds of staleness on profile names; for balances we bypass the cache entirely" — is exactly the trade-off reasoning being graded.

---

## 8. CDN specifics

A CDN is a cache with geography as its purpose: it removes the 100–200 ms cross-continent round trip by serving from a nearby edge.

- **Static assets** (JS, CSS, images, video segments) — cache for a long time with **content-hashed filenames**, so a deploy changes the URL and invalidation never arises.
- **Dynamic content** — cacheable for seconds if slightly stale is acceptable; use `Cache-Control: s-maxage` to let the CDN cache longer than the browser.
- **Cache key** — by default the URL, but it can include headers (`Vary`). A cache key that includes a per-user cookie has a hit ratio near zero; this is a common misconfiguration.
- **Purging** is slow and rate-limited on most CDNs. Prefer versioned URLs.
- **Origin shield** — an intermediate cache layer so that many edges do not all miss to the origin simultaneously.

---

## 9. Choosing what to cache

Good candidates: expensive to compute, frequently read, rarely written, tolerant of staleness.

| Candidate | Cache it? |
|---|---|
| A user's profile | yes — read constantly, written rarely |
| A rendered feed page | yes, briefly — expensive, tolerates seconds of staleness |
| A product catalogue | yes — read-heavy, changes rarely |
| An account balance | **no** — correctness matters, and it is cheap to read |
| A one-off analytics query | no — not re-read |
| A session token | yes — that is what a session store is |

**Do not cache what you cannot afford to be stale, and do not cache what is not re-read.**

---

## 10. Recall questions

1. Why does caching work at all — which two properties of real workloads?
2. Give the approximate cost ratio between a memory reference, an SSD read and a database round trip.
3. What is the single metric that determines whether a cache is worth it?
4. Compare in-process and distributed caches; why does the hit ratio of an in-process cache fall as you add instances?
5. Write the cache-aside read and write paths.
6. Why delete the cache entry on write rather than update it?
7. When would you accept write-behind, and what is the risk?
8. Name the three invalidation strategies and when each is right.
9. Why do versioned keys sidestep invalidation?
10. What is LRU's weakness, and what is LFU's?
11. Describe cache stampede and three mitigations.
12. Describe cache penetration and its two mitigations.
13. Describe the stale-write race in cache-aside and how to bound it.
14. Why does a CDN cache key that includes a user cookie perform badly?
15. Give two things you should never cache, and say why.
