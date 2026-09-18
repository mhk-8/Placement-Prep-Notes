# System Design Fundamentals — Flashcards

## Questions

**Scaling and load balancing**
1. What is the precondition for horizontal scaling, and where does displaced state go?
2. Give three things an L7 load balancer can do that an L4 cannot.
3. How can a health check turn a degradation into an outage, and what is the two-endpoint fix?
4. Why is jitter essential in a retry policy?
5. Describe the three circuit-breaker states.
6. What is a bulkhead, and how does it differ from a circuit breaker?

**Caching**
7. Which two properties of real workloads make caching work?
8. Give the cost ratio between a memory reference, an SSD read and a database round trip.
9. Write the cache-aside read and write paths, and say why writes delete rather than update.
10. Distinguish cache stampede, penetration and avalanche, with one mitigation each.
11. Why do versioned keys sidestep invalidation entirely?
12. Why does a CDN cache key containing a per-user cookie perform badly?

**Databases**
13. What are the seven questions to answer before choosing a database?
14. Contrast B+ tree and LSM tree on write path and amplification.
15. Why do SSTables carry Bloom filters?
16. Give the five steps of scaling a relational database, in order.
17. What is the pre-signed URL pattern, and why does it matter?

**Replication and partitioning**
18. State precisely what replication scales and what partitioning scales.
19. Name the three replication-lag anomalies and the fix for each.
20. What is split brain, and how does a majority quorum prevent it?
21. Give the three properties a good partition key must have.
22. Why must you never partition on a mutable attribute?
23. Contrast local and global secondary indexes on a partitioned store.
24. What is salting, and what does it cost on reads?

**Consistency**
25. Give the three distinct meanings of "consistency".
26. Define linearizability, and say why causal consistency is often the better target.
27. State CAP correctly, plus the two things it does not claim.
28. State PACELC and why the else-branch matters more.
29. State the quorum condition and why it works.
30. Describe 2PC's fatal flaw and the saga alternative.
31. What problem does the outbox pattern solve, and how?

**Messaging**
32. Distinguish a queue, pub/sub and an event log on retention and replay.
33. Why is exactly-once delivery impossible, and what is the correct phrasing?
34. What ordering does Kafka guarantee, and what is the design consequence?
35. Why is consumer lag the key metric?
36. What is a poison pill, and what prevents it blocking a partition?

**Rate limiting and hashing**
37. Describe the fixed-window boundary burst with numbers.
38. Why does token bucket suit real API clients?
39. Why must a distributed rate-limit update be atomic, and how is that done?
40. Why does `hash mod N` fail, and how many keys move with consistent hashing instead?
41. Give the two problems that virtual nodes solve.

**Probabilistic structures**
42. What does a Bloom filter guarantee and not guarantee? Bits per element at 1%?
43. What does HyperLogLog estimate, in how much space, at what error?
44. Why does a count-min sketch take the minimum across rows?

**Microservices and observability**
45. What problem do microservices actually solve, and what do they cost?
46. Compute the availability of four 99.9% services in series.
47. Why is cursor pagination preferred to offset?
48. State the JWT trade-off precisely.
49. Give the four golden signals and say which is the leading indicator.
50. Explain tail latency amplification with the 100-subrequest calculation.
51. What is an error budget, and how does it change the reliability conversation?

---

## Answers

1. Statelessness — any instance must be able to serve any request. State moves to a shared cache, object storage, a queue or a signed token.
2. Route by path, header or cookie; terminate TLS; inspect and modify requests (and therefore do request-level rather than connection-level balancing).
3. If it checks a briefly-slow database, every instance reports unhealthy at once and the balancer removes the whole pool. Split into a **liveness** check (is the process alive) and a **readiness** check (should it receive traffic).
4. Without it, all clients that failed together retry together, producing a synchronised wave that re-breaks the recovering service.
5. **Closed** (calls pass through), **Open** (fail fast without calling), **Half-open** (one trial request after a cooldown; success closes, failure reopens).
6. A bulkhead isolates resources — a separate thread or connection pool per dependency — so one saturated dependency cannot starve the others. A circuit breaker stops calling a failing dependency altogether.
7. Temporal locality and access skew (a small fraction of items receives most requests).
8. Memory ~100 ns, SSD ~150 µs, a database round trip ~1–10 ms — roughly 1 : 1,500 : 10,000+.
9. Read: check cache, on miss read the database and populate. Write: write the database, then **delete** the key. Updating creates a race where two writers set the database and cache in opposite orders, leaving them permanently disagreed.
10. **Stampede**: a hot key expires, concurrent requests all miss → request coalescing. **Penetration**: requests for nonexistent keys always miss → cache the negative result or use a Bloom filter. **Avalanche**: mass simultaneous expiry or a cold restart → jittered TTLs and cache warming.
11. The key contains a version or content hash, so a change produces a *new* key; the old entry is never invalidated, it simply stops being requested and ages out.
12. The cache key then differs for every user, so essentially nothing is ever shared and the hit ratio approaches zero.
13. Read queries; write pattern; read:write ratio; relationships needing joins; required consistency; data volume and growth; schema stability.
14. B+ tree: in-place random writes, fewer page reads per read, lower write amplification. LSM: sequential appends plus background compaction, higher write throughput, higher read and write amplification.
15. So a read can skip an SSTable that definitely does not contain the key, avoiding a disk seek — and a false positive costs only one wasted read, never correctness.
16. Indexes and query tuning; a bigger machine; read replicas; caching; functional partitioning; sharding.
17. The client uploads directly to object storage using a time-limited signed URL, so file bytes never pass through your application servers.
18. Replication copies the same data (scaling reads, availability, durability). Partitioning splits different data (scaling writes and capacity).
19. Read-your-writes violation → route the user's reads to the leader briefly. Monotonic reads violation → pin the user to one replica. Consistent prefix violation → keep causally related writes in one partition.
20. Two nodes both believing they are leader and both accepting writes. A majority quorum makes it impossible, since two disjoint majorities cannot exist; fencing tokens stop a revived old leader writing.
21. High cardinality, even distribution, and presence in the common query.
22. Because a change to that attribute would change the row's shard, requiring a cross-shard move — a distributed transaction you did not want.
23. **Local**: each shard indexes its own data — cheap writes, but reads scatter-gather across all shards. **Global**: the index is partitioned by term — reads hit one shard, but writes touch two and are usually asynchronous and slightly stale.
24. Appending a random suffix to a hot key to spread its writes across several partitions. Reads must then query all the salted variants and merge.
25. The C in ACID (integrity/constraints); the C in CAP (linearizability); and consistency models generally (the spectrum of read guarantees).
26. Linearizable: every read returns the most recent completed write, respecting real-time order. Causal consistency preserves cause-and-effect ordering without global coordination, which is usually all an application needs.
27. During a **partition**, choose consistency or availability. It does not describe the non-partitioned case, and it is a binary idealisation while real systems tune per operation.
28. If **P**artition then **A** or **C**; **E**lse **L**atency or **C**onsistency. Systems spend almost all their time unpartitioned, where the latency/consistency trade-off is the live one.
29. **R + W > N** — the read set and write set must overlap, so at least one responding replica holds the latest write.
30. 2PC blocks: if the coordinator dies after participants promise, they hold locks and cannot decide. A saga replaces atomicity with local transactions plus compensating actions, which never blocks but exposes intermediate states.
31. Updating the database and publishing an event atomically. Write the event to an outbox table in the **same local transaction**, and have a relay publish it — local atomicity, at-least-once publication, idempotent consumers.
32. A queue deletes on consumption with no replay. Pub/sub broadcasts and does not retain. A log **retains** messages after consumption, consumers hold their own offsets, and history can be **replayed**.
33. The consumer cannot distinguish a lost ack from a lost message, so redelivery is unavoidable. The correct phrasing is at-least-once delivery plus idempotent processing.
34. Ordering only **within a partition**. So messages that must be ordered relative to each other must share a partition key — and the partition count caps consumer parallelism.
35. It measures the backlog directly and grows before anything user-visible breaks, making it the earliest available warning.
36. A message that always fails processing, blocking its partition forever. A dead letter queue after N attempts moves it aside — and must itself be monitored.
37. With a limit of 100/minute, a client can send 100 at 10:00:59 and 100 more at 10:01:00 — 200 requests in one second without ever violating the stated limit.
38. Real clients are idle then bursty. Token bucket permits a burst up to the bucket capacity while bounding the long-run average, and costs only two numbers per client.
39. Otherwise two concurrent requests can both read 99 and both proceed. Use a Lua script (or equivalent) so refill, check and decrement happen in one atomic round trip.
40. Changing N remaps nearly every key (~80% going 4→5). Consistent hashing moves only ~K/N — about 20%.
41. Uneven arc lengths from random placement, and a failed node dumping its entire range onto one successor. Vnodes also let you weight heterogeneous hardware.
42. No false negatives; false positives possible. About **9.6 bits per element** for 1%, 14.4 for 0.1%.
43. Distinct-element cardinality, in about **12 KB** at roughly **1%** standard error — and it is mergeable, so per-server counts combine.
44. Collisions can only inflate a counter, so every row overestimates; the minimum picks the least-polluted estimate. It therefore never underestimates.
45. An organisational problem — many teams blocked on one deployment. The cost is network calls, distributed transactions, distributed tracing, and a much larger operational surface.
46. 0.999⁴ ≈ **99.6%**, about 35 hours of downtime a year purely from the topology.
47. `OFFSET n` forces the database to scan and discard n rows, so deep pages get slower; and concurrent inserts shift items across page boundaries, so users see duplicates or miss rows.
48. It removes the session lookup, which suits distributed systems — but a JWT is valid until it expires, so revocation requires either short expiry with refresh tokens or a revocation list that reintroduces the lookup.
49. Latency, traffic, errors, **saturation** — and saturation is the leading indicator of the other three.
50. A page making 100 sub-requests, each with a 1% chance of exceeding p99, has a 1 − 0.99¹⁰⁰ ≈ **63%** chance of at least one slow call — so a component's p99 becomes the composed page's typical experience.
51. The allowed unreliability implied by an SLO (99.9% permits ~43 minutes a month). It reframes reliability from an argument into arithmetic: risky deploys **spend** the budget, and when it is exhausted, feature work stops.
