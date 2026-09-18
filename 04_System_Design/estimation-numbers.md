# Back-of-Envelope Estimation

> The numbers, the arithmetic shortcuts, and five fully worked estimations. Estimation is not about precision — it is about arriving at the right **order of magnitude** fast enough that it informs the design rather than delaying it.

---

## 1. Latency numbers every engineer should know

| Operation | Time | Relative |
|---|---|---|
| L1 cache reference | 0.5 ns | 1× |
| Branch mispredict | 5 ns | 10× |
| L2 cache reference | 7 ns | 14× |
| Mutex lock/unlock | 25 ns | 50× |
| Main memory reference | **100 ns** | 200× |
| Compress 1 KB (Snappy) | 2 µs | |
| Send 1 KB over 1 Gbps network | 10 µs | |
| **SSD random read** | **150 µs** | |
| Read 1 MB sequentially from memory | 250 µs | |
| Round trip within a datacentre | **500 µs** | |
| Read 1 MB sequentially from SSD | 1 ms | |
| **Disk seek (HDD)** | **10 ms** | |
| Read 1 MB sequentially from HDD | 20 ms | |
| **Round trip California → Netherlands** | **150 ms** | |

**The three ratios that matter:**
- Memory is ~100× faster than SSD; SSD is ~100× faster than HDD seek.
- A datacentre round trip (0.5 ms) is ~5,000× a memory reference — **the network is the expensive part**, which is why batching and caching dominate real optimisation.
- A cross-continent round trip is 150 ms, which is why CDNs exist: you cannot beat the speed of light, you can only move the data closer.

**Rules of thumb these produce:**
- Memory is fast, disk is slow, **the network is slower than both** for anything cross-region.
- Sequential access beats random access by an order of magnitude on both SSD and HDD.
- Avoid cross-region round trips in a user-facing path. One is tolerable; three is a broken experience.

---

## 2. Availability and its cost

| Availability | Downtime per year | Per month | Per day |
|---|---|---|---|
| 99% ("two nines") | 3.65 days | 7.2 hours | 14.4 min |
| 99.9% | **8.77 hours** | 43.8 min | 1.44 min |
| 99.99% | **52.6 min** | 4.38 min | 8.6 s |
| 99.999% ("five nines") | 5.26 min | 26 s | 864 ms |

**Components in series multiply.** A request passing through a load balancer, an app server and a database, each at 99.9%, gives 0.999³ ≈ **99.7%** — a full day of downtime a year. This is the argument for redundancy at every tier, and it is worth stating explicitly in an interview.

**Components in parallel add nines.** Two independent 99% components in an either-works configuration give 1 − 0.01² = **99.99%**.

---

## 3. Powers of two and data sizes

| Power | Approx value | Name |
|---|---|---|
| 2¹⁰ | 1 thousand | 1 KB |
| 2²⁰ | 1 million | 1 MB |
| 2³⁰ | 1 billion | 1 GB |
| 2⁴⁰ | 1 trillion | 1 TB |
| 2⁵⁰ | 1 quadrillion | 1 PB |

| Data | Typical size |
|---|---|
| A single character (ASCII) | 1 byte |
| A UUID | 16 bytes (36 as a string) |
| A tweet-length text post | ~300 bytes |
| A typical database row | 100 B – 1 KB |
| A compressed thumbnail | 10–50 KB |
| A web page (HTML only) | ~100 KB |
| A photo | 200 KB – 2 MB |
| A minute of 1080p video | ~50 MB |

---

## 4. The arithmetic shortcuts

```
Seconds in a day    = 86,400 ≈ 10^5        ← round to this, always
Seconds in a month  ≈ 2.5 × 10^6
Seconds in a year   ≈ 3 × 10^7

1 million per day   ≈ 12 per second
1 billion per day   ≈ 12,000 per second

1 KB × 1 million    = 1 GB
1 KB × 1 billion    = 1 TB
1 MB × 1 million    = 1 TB
```

**Peak traffic** is usually 2–3× the average for a steady consumer service, and can be **10×** for event-driven ones (a ticket sale, a sports final, a flash sale). Say which assumption you are using.

**The 80/20 rule for caching:** roughly 20% of the data serves 80% of requests, so a cache holding 20% of the working set typically achieves an 80% hit rate. That single assumption converts a storage estimate into a memory estimate.

---

## 5. What one machine can do

Useful for answering "do we even need to distribute this?"

| Resource | A single commodity server |
|---|---|
| RAM | 64–512 GB |
| SSD | 1–10 TB |
| Network | 10 Gbps ≈ 1.25 GB/s |
| Simple HTTP QPS | 10,000–50,000 |
| Postgres/MySQL writes/s | 5,000–20,000 (with SSD, simple rows) |
| Postgres/MySQL reads/s | 50,000+ with a warm cache |
| Redis operations/s | 100,000+ |
| Kafka messages/s per broker | 100,000+ |

**Modern hardware is far more capable than most designs assume.** If your estimate lands at 500 QPS and 200 GB, saying so — "this fits on one machine with a replica; I'll design it to shard later but I would not shard now" — is a *stronger* answer than reflexively drawing a distributed system. Premature distribution is a real design failure, and interviewers notice when you avoid it.

---

## 6. Worked estimation 1 — URL shortener

**Given:** 100 million new URLs per month, read:write ratio 100:1, 5-year retention.

```
WRITES
  100M / month ÷ (2.5 × 10^6 s) = 40 writes/second
  Peak (×3)                     = 120 writes/second

READS
  40 × 100                      = 4,000 reads/second
  Peak                          = 12,000 reads/second

STORAGE (per record ~500 bytes: short code, long URL, user, timestamps)
  100M × 500 B                  = 50 GB / month
  × 12 × 5 years                = 3 TB
  × 3 (replication)             = 9 TB

BANDWIDTH
  Write: 120 × 500 B            ≈ 60 KB/s        (negligible)
  Read:  12,000 × 500 B         ≈ 6 MB/s         (comfortable)

CACHE (80/20 on daily reads)
  Daily reads = 4,000 × 10^5    = 400M
  20% of ~3.3M daily new URLs is tiny; cache the hot set
  Say 20% of a month's URLs: 20M × 500 B ≈ 10 GB   → fits in one Redis node
```

**What this tells the design:** overwhelmingly read-heavy, tiny records, no joins, key lookups only. → a key-value store with a cache in front, and 9 TB means sharding is needed but not urgent.

---

## 7. Worked estimation 2 — Social feed

**Given:** 300 million DAU, each posting twice a day and reading their feed 10 times a day.

```
WRITES (posts)
  300M × 2 = 600M / day ÷ 10^5  = 6,000 posts/second
  Peak (×3)                     = 18,000/second

READS (feed loads)
  300M × 10 = 3B / day ÷ 10^5   = 30,000 feed requests/second
  Peak                          = 90,000/second

Read:write ratio                = 5:1 on user actions, but each feed read
                                  assembles ~100 posts, so the amplification
                                  at the storage layer is far higher

STORAGE
  Post metadata 1 KB × 600M     = 600 GB / day  ≈ 220 TB / year
  Media: assume 10% include a 200 KB image
        60M × 200 KB            = 12 TB / day   ← media dominates by 20×

FAN-OUT
  Average 200 followers
  6,000 posts/s × 200           = 1.2M timeline writes/second  ← the real problem
```

**What this tells the design:** media goes to object storage plus a CDN, not the database. And the fan-out number — 1.2M writes/second — is what forces the hybrid push/pull decision. **The estimate discovered the hard problem**, which is exactly what estimation is for.

---

## 8. Worked estimation 3 — Chat system

**Given:** 50 million DAU, 40 messages sent per user per day, messages retained forever.

```
MESSAGES
  50M × 40 = 2B / day ÷ 10^5    = 20,000 messages/second
  Peak                          = 60,000/second

CONNECTIONS
  Assume 10% concurrently online = 5M concurrent WebSocket connections
  At ~10 KB memory per connection = 50 GB of connection state
  A server holding 50,000 connections → 100 servers just for connections

STORAGE
  Message ~200 bytes (id, sender, receiver, text, timestamp)
  2B × 200 B                    = 400 GB / day
  × 365                         = 146 TB / year
```

**What this tells the design:** connection management is a first-class problem needing its own tier and a service-discovery mechanism to find which server holds a given user. Storage grows to hundreds of terabytes, so the message store must be partitioned from day one and old messages tiered to cheaper storage.

---

## 9. Worked estimation 4 — Video streaming

**Given:** 10 million DAU, each watching 30 minutes of 1080p a day.

```
BANDWIDTH
  1080p ≈ 5 Mbps
  Assume 5% watching simultaneously at peak = 500,000 concurrent streams
  500,000 × 5 Mbps              = 2.5 Tbps     ← this is enormous

STORAGE (uploads)
  Say 500,000 minutes uploaded/day at ~50 MB/min raw
                                = 25 TB / day raw
  × 5 transcoded renditions     = 125 TB / day
```

**What this tells the design:** 2.5 Tbps cannot come from an origin — **the CDN is not an optimisation here, it is the architecture.** And transcoding at 25 TB/day of input is a large asynchronous pipeline, not a request-path activity.

---

## 10. Worked estimation 5 — Does this even need to be distributed?

**Given:** an internal tool for 5,000 employees, each making 100 requests a day.

```
QPS = 5,000 × 100 / 10^5 = 5 requests/second.  Peak maybe 50.
Storage: 500,000 rows/day × 1 KB = 500 MB/day = 180 GB/year.
```

**The answer is a single application server, one managed Postgres instance with a replica, and daily backups.** No cache, no queue, no sharding.

Recognising this case is a genuine signal of judgement. A candidate who designs a nine-component distributed system for 5 QPS has demonstrated that they pattern-match rather than reason.

---

## 11. Estimation checklist

- [ ] State assumptions explicitly, and say they are assumptions
- [ ] Round to powers of ten and say you are rounding
- [ ] Compute QPS **and** peak QPS
- [ ] Compute storage **with** replication and retention
- [ ] Check the read:write ratio — it drives caching and replication strategy
- [ ] Ask whether a single machine would do
- [ ] **Connect every number to a design decision** — an unused estimate is wasted time
- [ ] Sanity-check against reality: if you compute 50 Tbps for a small startup, you made an error
