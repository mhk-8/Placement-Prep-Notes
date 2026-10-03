
# System Design Numbers and Estimation — One Pager

> **Use:** any HLD round, and the back-of-envelope moment in every system-design interview.
> Knowing these numbers is what separates a plausible design from a hand-wave. ⭐

---

## 1. Latency numbers every engineer should know ⭐⭐⭐

| Operation | Latency | Relative |
|---|---|---|
| L1 cache reference | 0.5 ns | 1× |
| Branch mispredict | 5 ns | |
| L2 cache reference | 7 ns | 14× L1 |
| Mutex lock/unlock | 25 ns | |
| **Main memory (DRAM) reference** | **100 ns** | 200× L1 ⭐ |
| Compress 1 KB with Snappy | 3 µs | |
| Send 1 KB over 1 Gbps network | 10 µs | |
| **Read 4 KB randomly from SSD** | **150 µs** | 1,500× DRAM ⭐ |
| Read 1 MB sequentially from memory | 250 µs | |
| Round trip within the same datacentre | **500 µs** | |
| Read 1 MB sequentially from SSD | 1 ms | 4× memory |
| **Disk seek (HDD)** | **10 ms** | 100,000× DRAM ⭐ |
| Read 1 MB sequentially from HDD | 20 ms | |
| Send packet CA → Netherlands → CA | **150 ms** | |

**The three ratios to remember:** memory is ~100× faster than SSD; SSD is ~100× faster than
disk seek; same-DC round trip (0.5 ms) is ~300× an intercontinental one is ~150 ms. ⭐

---

## 2. Powers of two and data sizes

| Power | Approx | Name |
|---|---|---|
| 2¹⁰ | 1 thousand | 1 KB |
| 2²⁰ | 1 million | 1 MB |
| 2³⁰ | 1 billion | 1 GB |
| 2⁴⁰ | 1 trillion | 1 TB |
| 2⁵⁰ | 1 quadrillion | 1 PB |

```
char/byte 1 B · int/float 4 B · long/double 8 B · pointer 8 B
UUID 16 B (36 chars as text) · timestamp 8 B · IPv4 4 B
Short URL record ≈ 100 B · tweet ≈ 300 B · typical JSON API row ≈ 1 KB
Thumbnail ≈ 20 KB · web photo ≈ 300 KB · 1 min of 1080p video ≈ 50 MB ⭐
```

---

## 3. Time and QPS arithmetic ⭐⭐

```
1 day            = 86,400 s  ≈ 10⁵ s   ⭐ the single most useful approximation
1 month          ≈ 2.5 × 10⁶ s
1 year           ≈ 3.15 × 10⁷ s  ≈ π × 10⁷ s

1 M requests/day   → ~12 QPS average
100 M requests/day → ~1,160 QPS average
1 B requests/day   → ~11,600 QPS average

PEAK = 2× to 10× average. Use 3× unless told otherwise, and SAY you are using 3×. ⭐
```

### 📐 Worked example — URL shortener
```
Assumption : 100 M new URLs/day, read:write = 100:1
Write QPS  = 100 M / 10⁵ s = 1,000 QPS;  peak ≈ 3,000 QPS
Read QPS   = 100 × 1,000 = 100,000 QPS;  peak ≈ 300,000 QPS
Storage    = 100 M/day × 100 B = 10 GB/day → 3.65 TB/year → ~18 TB for 5 years with replication
Key space  = base62, 7 chars → 62⁷ ≈ 3.5 × 10¹² — enough for ~100 years at this rate ⭐
Cache      = 20% of reads are 80% of traffic → cache 20% of a day's URLs = 2 GB ⭐ fits in RAM
Bandwidth  = 100 K reads/s × 500 B = 50 MB/s out
Servers    = 300 K peak QPS / 5 K QPS per server ≈ 60 app servers + headroom
```

> ⭐ **The method matters more than the number.** State the assumption, round aggressively,
> carry one significant figure, and sanity-check the magnitude at the end.

---

## 4. Single-machine capacity rules of thumb ⭐

| Resource | Rough capacity |
|---|---|
| App server (simple requests) | 1,000 – 10,000 QPS |
| MySQL/Postgres, simple indexed reads | 5,000 – 20,000 QPS |
| MySQL/Postgres writes | 1,000 – 5,000 QPS (fsync-bound) |
| Redis (single instance) | 100,000+ ops/s ⭐ |
| Kafka (per broker) | ~100 MB/s, millions of msgs/s cluster-wide |
| Nginx / load balancer | 50,000+ connections |
| 1 Gbps NIC | ~125 MB/s |
| 10 Gbps NIC | ~1.25 GB/s |
| Commodity server RAM | 64 – 512 GB |
| SSD throughput | 500 MB/s (SATA), 3-7 GB/s (NVMe) ⭐ |
| HDD throughput | 100 – 200 MB/s sequential, ~100 IOPS random |
| SSD IOPS | 10,000 – 1,000,000 |

**Availability:** 99% = 3.65 days/yr down · 99.9% = 8.8 h · 99.99% = 52 min · 99.999% = 5 min.
⭐ Note that a chain of four 99.9% services gives ~99.6%, not 99.9%.

---

## 5. The estimation checklist ⭐

```
□ DAU / MAU, and requests per user per day
□ Read:write ratio  ⭐ it decides the whole architecture
□ QPS average, then peak (state the multiplier)
□ Payload size per request → bandwidth in and out
□ Storage per record × records/day × retention × replication factor
□ Cache size: Pareto — 20% of keys serve 80% of reads
□ Number of servers = peak QPS / per-server QPS, with 30-50% headroom
□ Sanity check: does this fit on one machine? If yes, say so and stop. ⚠️ Not everything
  needs to be distributed, and saying this is a maturity signal
```

---

## 6. Design building blocks — the one-line justification for each

| Component | Why you add it |
|---|---|
| **Load balancer** | Distribute traffic, health checks, TLS termination. L4 (fast) vs L7 (content-aware) |
| **Cache** | Cut read latency and DB load. Cache-aside · write-through · write-behind. TTL + eviction (LRU/LFU) ⭐ |
| **CDN** | Serve static/media from the edge; cuts latency and egress cost |
| **Read replica** | Scale reads; ⚠️ replication lag → stale reads |
| **Sharding** | Scale writes/storage beyond one machine. Hash · range · directory ⚠️ hot keys, resharding |
| **Message queue** | Decouple, absorb bursts, retry. Kafka (log, replay) vs SQS/RabbitMQ (task queue) ⭐ |
| **Rate limiter** | Protect downstream. Token bucket · leaky bucket · fixed/sliding window ⭐ |
| **Reverse proxy / API gateway** | Auth, routing, rate limiting, request aggregation |
| **Search index** | Elasticsearch for text; a DB `LIKE` does not scale |
| **Blob store** | S3 for large objects; keep metadata in the DB, not the blob ⭐ |
| **Consistent hashing** | Adding a node moves only 1/n of keys; virtual nodes for balance |
| **Bloom filter** | "Definitely not present" in O(1) and ~10 bits/key; **no false negatives**, tunable false positives ⭐ |
| **Zookeeper / etcd** | Leader election, config, service discovery |
| **Circuit breaker** | Stop hammering a failing dependency; fail fast |
| **Idempotency key** | Safe retries under at-least-once delivery ⭐ |

---

## 7. Trade-offs to state explicitly ⭐⭐

```
CAP           : under a partition, choose Consistency or Availability. P is not optional ⚠️
PACELC        : else, choose Latency or Consistency
Strong vs eventual consistency: read-your-writes, monotonic reads, causal consistency
SQL vs NoSQL  : transactions + joins + schema vs horizontal scale + flexible schema
Normalise vs denormalise: write integrity vs read speed
Sync vs async : simplicity/latency vs throughput/resilience
Push vs pull  : fan-out on write (fast reads, expensive for celebrities) vs on read ⭐
Monolith vs microservices: operational simplicity vs independent scaling + deploy
Latency vs throughput: not the same thing — optimise the one the SLA names ⭐
Cost          : RAM ≫ SSD ≫ HDD ≫ object storage per GB. Egress bandwidth is expensive
```

### Quorum maths
📐 `R + W > N` gives strong consistency. With N = 3: W = 2, R = 2 is the usual choice.
W = 1 is fast writes with stale reads; R = 1, W = N is fast reads with slow writes.

### Bloom filter sizing
📐 For false-positive rate `p` and `n` items: `m = −n·ln p / (ln 2)²` bits,
`k = (m/n)·ln 2` hash functions. For p = 1%, that is **~9.6 bits per item, 7 hashes**. ⭐

---

## 8. GPU and parallel numbers ⭐ (your differentiator — NVIDIA, Qualcomm)

| Quantity | Value |
|---|---|
| GPU global memory bandwidth (modern datacentre) | 1 – 3 TB/s |
| CPU DRAM bandwidth | ~50 – 200 GB/s |
| PCIe 4.0 ×16 | ~32 GB/s ⚠️ the usual bottleneck in host↔device transfer |
| NVLink | 300 – 900 GB/s |
| Global memory latency (GPU) | 300 – 600 cycles ⭐ hidden by occupancy, not avoided |
| Shared memory / L1 latency | ~20 – 30 cycles |
| Warp size | 32 threads (NVIDIA) |
| Coalesced transaction | 32 B / 64 B / 128 B segments ⭐ |

```
ARITHMETIC INTENSITY = FLOPs / bytes moved. Compare against the machine balance
(peak FLOP/s ÷ peak bytes/s) to decide whether you are compute- or memory-bound.
ROOFLINE: attainable = min(peak FLOP/s, arithmetic intensity × bandwidth) ⭐
→ Most graph and sparse workloads are memory-bound. Say this; it is the right instinct and
  it is exactly what your SSSP and points-to projects demonstrate.
```

📐 **Amdahl:** speedup = `1 / (s + p/N)` — a serial fraction `s` caps you at `1/s`.
📐 **Gustafson:** scaled speedup = `s + p·N` — the problem grows with the machine.
> ⭐ State both. Amdahl is the pessimistic fixed-problem view; Gustafson is why large-scale
> parallelism is still worth it in practice.

---

## 9. The interview script ⭐

```
 1. REQUIREMENTS   (5 min)  functional, non-functional (scale, latency, consistency), out of scope
 2. ESTIMATION     (5 min)  DAU → QPS → storage → bandwidth. State every assumption
 3. API            (3 min)  3-5 endpoints with signatures
 4. DATA MODEL     (5 min)  entities, keys, which store and WHY
 5. HIGH LEVEL     (10 min) boxes and arrows; client → LB → service → cache → DB → queue
 6. DEEP DIVE      (10 min) let them choose, or offer the interesting part
 7. SCALE & FAIL   (7 min)  bottleneck → shard/cache/replicate; single points of failure;
                            hot keys; monitoring and alerting
 8. TRADE-OFFS     (3 min)  what you would do differently with 10× scale or a different SLA
```

> ⭐ **Your strongest deep-dive directions**, given your background: anything involving
> throughput vs latency, memory access patterns, batch pipelines, feature stores, or
> model-serving infrastructure. Steer the deep dive there.

---

## Recall questions
1. DRAM vs SSD random read vs HDD seek — the three numbers and their ratios.
2. 1 B requests/day is how many QPS? What peak do you assume?
3. How many bits per item does a 1% Bloom filter need?
4. `R + W > N` — what does it guarantee, and what is the usual choice for N = 3?
5. Four chained services each at 99.9% — what is the end-to-end availability?
6. What is arithmetic intensity, and what does the roofline model tell you?
7. State Amdahl's law and the one thing it does not account for.
