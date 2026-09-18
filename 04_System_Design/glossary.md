# System Design Glossary

> Use these terms precisely. Vague usage of "consistency", "sharding" or "scalable" is one of the clearest negative signals in a design round.

---

## Scaling and traffic

**Vertical scaling (scale up)** — a bigger machine. Simple, no code changes, but bounded by the largest machine available and leaves a single point of failure.

**Horizontal scaling (scale out)** — more machines. Effectively unbounded, but requires statelessness, load balancing, and a strategy for data that cannot be duplicated.

**Stateless service** — one where any instance can serve any request, because no request-specific state is held in the instance's memory. The precondition for horizontal scaling.

**Load balancer** — distributes requests across instances. **L4** operates on TCP (IP and port, fast, protocol-agnostic); **L7** understands HTTP (can route by path, header or cookie, can terminate TLS).

**QPS / RPS** — queries or requests per second. Always distinguish **average** from **peak**; peak is typically 2–3× average and can be 10× for event-driven traffic.

**Throughput** — work completed per unit time. **Latency** — time for one operation. They are not the same, and batching usually improves throughput at the cost of latency.

**Tail latency (p99, p999)** — the latency experienced by the slowest 1% or 0.1% of requests. The number that matters for user experience, because a page assembling 100 sub-requests will hit the p99 on most loads.

**Backpressure** — a downstream component signalling upstream to slow down, rather than silently queueing or dropping.

**Thundering herd** — many clients hitting an origin simultaneously, typically when a popular cache entry expires or a service restarts cold.

---

## Data storage

**OLTP** — online transaction processing: many small reads and writes, low latency, normalised schema. **OLAP** — analytical processing: few large scans and aggregations, denormalised or columnar.

**Replication** — copying the same data to multiple nodes. Improves read throughput and availability. **Leader–follower** (one writable node), **multi-leader** (several, needing conflict resolution), **leaderless** (quorum-based, as in Dynamo).

**Replication lag** — the delay before a follower reflects a leader's write. The direct cause of "I posted it but I can't see it".

**Sharding / partitioning** — splitting *different* data across nodes. Improves write throughput and storage capacity. Note the distinction from replication: replication copies, sharding divides.

**Shard key / partition key** — the attribute deciding which shard a record lives on. The most consequential choice in a distributed data design; a poor one creates **hot partitions**.

**Hot partition / hotspot** — a shard receiving disproportionate traffic, usually from a skewed key (a celebrity user, a popular product, a monotonically increasing timestamp).

**Consistent hashing** — a partitioning scheme where adding or removing a node relocates only ~K/N keys instead of remapping everything. Uses a hash ring with virtual nodes.

**Denormalisation** — deliberately duplicating data to avoid joins at read time. Trades write complexity and redundancy risk for read speed.

**Write-ahead log (WAL)** — changes are appended to a durable log before being applied. Supports atomicity (undo) and durability (redo).

**LSM tree** — log-structured merge tree: writes go to an in-memory table and are flushed to immutable sorted files, compacted in the background. Optimised for writes (Cassandra, RocksDB, LevelDB). Contrast with the **B+ tree**, which is optimised for reads and range scans (Postgres, MySQL).

---

## Consistency

**Strong consistency** — every read returns the most recent write. Also called linearizability when it also respects real-time ordering.

**Eventual consistency** — replicas converge if writes stop. A read may return stale data in the meantime.

**Read-your-writes consistency** — a user always sees their own writes, even if others do not yet. Usually implemented by routing that user's reads to the leader, or by a session token.

**Monotonic reads** — a user never sees time go backwards (reading a newer value, then an older one).

**CAP theorem** — during a network **partition**, a system must choose between consistency and availability. It says nothing about the partition-free case, which is where it is most often misquoted.

**PACELC** — the fuller statement: if **P**artition, choose **A** or **C**; **E**lse, choose **L**atency or **C**onsistency.

**ACID** — atomicity, consistency, isolation, durability. The transactional guarantees of a classical database.

**BASE** — basically available, soft state, eventual consistency. The deliberately weaker counterpart adopted by many distributed stores.

**Quorum** — requiring R read replicas and W write replicas to respond, out of N. When **R + W > N**, a read set and a write set must overlap, giving strong consistency.

**Two-phase commit (2PC)** — a blocking distributed-transaction protocol with a prepare phase and a commit phase. Correct but fragile: a coordinator failure blocks participants.

**Saga** — a long-running transaction expressed as a sequence of local transactions, each with a compensating action to undo it. The usual practical alternative to 2PC across microservices.

**Idempotency** — an operation that can be applied repeatedly with the same result. Essential wherever retries exist, which is everywhere in a distributed system.

**Exactly-once delivery** — generally impossible over an unreliable network. What systems achieve is **at-least-once delivery plus idempotent processing**, which is observationally equivalent and is the phrasing to use.

---

## Caching

**Cache hit ratio** — the fraction of requests served from cache. The single metric that determines whether the cache is worth its complexity.

**Cache-aside (lazy loading)** — the application checks the cache, and on a miss reads the database and populates the cache. The most common pattern.

**Write-through** — writes go to the cache and the database synchronously. Consistent, higher write latency.

**Write-behind (write-back)** — writes go to the cache and are flushed to the database asynchronously. Fast, with a durability risk.

**TTL** — time to live, after which an entry expires. The simplest invalidation strategy and usually the right one.

**Cache stampede** — the thundering herd applied to a cache: an expiring hot key sends every concurrent request to the origin at once. Mitigated by request coalescing, a probabilistic early refresh, or a lock on regeneration.

**CDN** — a geographically distributed cache for static or cacheable content, placed close to users. Solves the speed-of-light problem.

**Eviction policies** — LRU (least recently used, the default), LFU (least frequently used), FIFO, random.

---

## Messaging

**Message queue** — point-to-point delivery; each message is consumed by exactly one consumer. Used for work distribution (SQS, RabbitMQ).

**Pub/sub** — one message is delivered to every subscriber. Used for event broadcasting.

**Event log** — an append-only, ordered, **replayable** sequence, retained after consumption (Kafka). Different from a queue: consumers track their own offset, and new consumers can replay history.

**Consumer group** — a set of consumers dividing partitions among themselves, so each message reaches the group once.

**Dead letter queue (DLQ)** — where messages go after repeated processing failures, so one poisonous message cannot block the pipeline.

**Ordering guarantee** — usually only *within a partition*. Global ordering across partitions is expensive and rarely necessary; say which you need.

---

## Architecture

**Monolith** — one deployable unit. Simple to develop, test and deploy; scales as a whole; the right starting point for most systems.

**Microservices** — independently deployable services with their own data. Independent scaling and team autonomy, at the cost of network calls, distributed transactions, and much heavier operational overhead.

**API gateway** — a single entry point handling routing, authentication, rate limiting, and request aggregation.

**Service mesh** — a sidecar-based infrastructure layer handling service-to-service concerns: retries, circuit breaking, mTLS, observability.

**Circuit breaker** — after repeated failures calling a dependency, stop calling it for a period and fail fast. Prevents a slow dependency from exhausting the caller's threads and cascading the failure.

**Bulkhead** — isolating resources (thread pools, connection pools) per dependency, so one saturated dependency cannot starve the others.

**Sidecar** — a helper process deployed alongside a service instance, handling cross-cutting concerns.

---

## Reliability and operations

**SLI / SLO / SLA** — a service **level indicator** is the measurement (p99 latency); the **objective** is the internal target (p99 < 200 ms); the **agreement** is the contractual promise with consequences.

**Error budget** — the allowed unreliability implied by an SLO. A 99.9% target permits 0.1% failures; spending the budget on risky deploys is a deliberate choice.

**Blast radius** — how much of the system a given failure affects. Cells, shards and bulkheads exist to contain it.

**Graceful degradation** — shedding non-essential functionality to keep the core working (serving a stale feed rather than an error page).

**Retry with exponential backoff and jitter** — retry after 1 s, 2 s, 4 s, with randomisation. **The jitter is essential**: without it, synchronised retries create their own thundering herd.

**Blue-green deployment** — two identical environments; switch traffic, roll back by switching back.

**Canary deployment** — route a small percentage of traffic to the new version first, and watch the error rate.

**Feature flag** — decouples deploying code from releasing behaviour; the basis of trunk-based development and instant rollback.

---

## Probabilistic structures

**Bloom filter** — a space-efficient set membership test with **no false negatives and possible false positives**. Used to avoid expensive lookups for keys that definitely do not exist.

**HyperLogLog** — approximate cardinality (count of distinct items) in kilobytes rather than gigabytes, with ~2% error.

**Count-min sketch** — approximate frequency counts in sublinear space; used for heavy-hitter detection.
