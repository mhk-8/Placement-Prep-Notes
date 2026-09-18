# System Design Fundamentals — Solved Questions

> 20 questions, in the style of both OA MCQs and interview follow-ups. System design rarely appears as a pure MCQ section, but these concepts are asked constantly as *follow-ups* in DSA and core-CS rounds — "you used a cache; what happens when it expires?"
> Every option is explained. Cover the answers.

---

**Q1.** A service is at 99.9% availability. Three such services are chained in series. What is the overall availability?
(a) 99.9%  (b) 99.7%  (c) 99.99%  (d) 33.3%

<details><summary>Answer</summary>

**(b) ≈99.7%.**

Components in series **multiply**: 0.999³ = 0.997. That is 8.77 hours × 3 ≈ **26 hours of downtime a year**, caused purely by the topology.

**The design consequence:** every synchronous hop you add to a request path reduces availability. It is the strongest quantitative argument for shallow call chains and for moving non-essential dependencies off the critical path.

Contrast with **parallel** redundancy, where availability *adds* nines: two independent 99% components give 1 − 0.01² = 99.99%.
</details>

---

**Q2.** The single most important question to ask in the first five minutes of a design round is
(a) Which database should we use?
(b) How many servers do we need?
(c) What are the functional and non-functional requirements, especially scale and consistency?
(d) What programming language?

<details><summary>Answer</summary>

**(c).**

Everything downstream is derived from scale, read/write ratio, latency target and consistency requirement. Choosing a database before knowing the access patterns is the failure the framework exists to prevent.

The single highest-value question inside (c) is the **consistency** one — "if two users book the last seat simultaneously, what should happen?" — because it alone determines the storage strategy.
</details>

---

**Q3.** `hash(key) mod N` is used to map keys to N cache servers. One server is added. Approximately what fraction of keys map to a different server?
(a) 1/N  (b) ~80% for N going 4→5  (c) 0  (d) 50% always

<details><summary>Answer</summary>

**(b) about 80%.**

Only keys where `h mod 4 == h mod 5` stay put — roughly 1 in 5. So ~80% move.

**Why this is catastrophic for a cache:** 80% of entries become misses instantly, so the origin receives a near-total load spike at the exact moment you were adding capacity.

**The fix is consistent hashing**, where only ~K/N keys move — about 20% in this case.
</details>

---

**Q4.** Virtual nodes exist in consistent hashing primarily to
(a) increase the number of physical servers
(b) smooth load distribution and spread a failed node's range across many successors
(c) reduce the hash function's cost
(d) support range queries

<details><summary>Answer</summary>

**(b).**

Two problems with one point per node: random placement gives uneven arcs (one node might own 60% of the ring), and when a node dies its **entire** range falls on one successor, potentially cascading.

With 100–200 vnodes per node, the law of large numbers smooths the arcs, and a failure is absorbed by many different successors. Vnodes also let you weight heterogeneous hardware by assigning more vnodes to bigger machines.

(d) is wrong and worth noting: consistent hashing *destroys* ordering, so range queries are lost.
</details>

---

**Q5.** A popular cache key expires and thousands of concurrent requests hit the database simultaneously. This is
(a) cache penetration  (b) **cache stampede**  (c) cache avalanche  (d) a cache miss

<details><summary>Answer</summary>

**(b) cache stampede** (thundering herd).

Distinguish the three precisely:
- **Stampede:** one hot key expires; concurrent requests all miss.
- **Penetration:** requests for keys that **never existed** always miss and always reach the database.
- **Avalanche:** a large fraction of the cache expires at once, or the cache restarts cold.

**Stampede mitigations:** request coalescing (the first miss computes, the rest wait), probabilistic early refresh, a lock on regeneration, and jittered TTLs.
</details>

---

**Q6.** In cache-aside, why delete the cache entry on write rather than update it?
(a) Deleting is faster
(b) Updating creates a race where two writers set the cache in the opposite order from the database
(c) The cache does not support updates
(d) It saves memory

<details><summary>Answer</summary>

**(b).**

Two concurrent writers can set the database in one order and the cache in the other, leaving them **permanently disagreed** — the cache never self-corrects.

Deleting is idempotent, and the next read repopulates from the authoritative source. The remaining race (a slow reader writing a stale value after a writer's delete) is bounded by the TTL, and further reduced by a delayed second delete.
</details>

---

**Q7.** CAP states that during a network partition a system must choose between
(a) consistency and availability
(b) consistency and partition tolerance
(c) availability and partition tolerance
(d) latency and durability

<details><summary>Answer</summary>

**(a).**

Partition tolerance is not optional in a distributed system — networks fail — so the real choice is CP or AP.

**Two things CAP does not say**, and both are commonly misquoted: it says nothing about the non-partitioned case, and it is a binary idealisation while real systems tune per operation.

**PACELC** is the fuller statement: if **P**artition, choose **A** or **C**; **E**lse, choose **L**atency or **C**onsistency. The else-branch is where systems spend almost all their time.
</details>

---

**Q8.** Which consistency model means "a user always sees their own writes, even if others do not yet"?
(a) Linearizable  (b) **Read-your-writes**  (c) Monotonic reads  (d) Eventual

<details><summary>Answer</summary>

**(b) read-your-writes.**

It is the guarantee that fixes the classic replication-lag symptom: a user posts a comment, their next read hits a lagging follower, and the comment appears to have vanished.

Implemented by routing that user's reads to the **leader** for a window after their write, or by passing a token carrying the write's log position and waiting for a replica to catch up.

**Monotonic reads** (c) is the different guarantee that a user never sees time run backwards; it is fixed by pinning a user to one replica.
</details>

---

**Q9.** With N=3 replicas, which quorum configuration guarantees a read sees the latest write?
(a) W=1, R=1  (b) W=2, R=2  (c) W=1, R=2  (d) W=3, R=0

<details><summary>Answer</summary>

**(b) W=2, R=2.**

The condition is **R + W > N**. With 2 + 2 > 3, the read set and write set must share at least one replica, so at least one responder holds the latest value.

(a) 1 + 1 = 2, not greater than 3 — eventually consistent. (c) 1 + 2 = 3, not greater than 3 — still no guaranteed overlap. (d) R=0 reads nothing.

`W=3, R=1` also satisfies the condition and gives fast reads, but writes then fail if **any** replica is down.
</details>

---

**Q10.** Two-phase commit's fundamental weakness is that it
(a) is slow  (b) **blocks if the coordinator fails after the prepare phase**  (c) cannot handle more than two participants  (d) loses data

<details><summary>Answer</summary>

**(b).**

Once participants have promised to commit, they hold locks and **cannot unilaterally decide**. If the coordinator dies before announcing the outcome, they are stuck until it returns.

In a microservice architecture that is a genuine outage mode, which is why 2PC is rare across service boundaries and common only inside a single database.

**The alternative is a saga** — local transactions with compensating actions — which gives up atomicity and isolation in exchange for never blocking.
</details>

---

**Q11.** Which partition key is the worst choice for a messaging system's message table?
(a) conversation_id  (b) user_id  (c) **timestamp**  (d) message_id

<details><summary>Answer</summary>

**(c) timestamp.**

Every new message has a timestamp near "now", so **all current writes land on the newest partition** while every other partition sits idle. This is the classic hot-partition mistake.

A good partition key needs high cardinality, even distribution, and presence in the common query. `conversation_id` satisfies all three and additionally keeps a conversation's messages together for range reads.

**The related rule:** never partition on a **mutable** attribute (like `status`), because a change would require moving the row between shards.
</details>

---

**Q12.** In a partitioned database, a **local** secondary index means
(a) reads by the indexed attribute must scatter-gather across all shards
(b) writes touch two shards
(c) the index is always stale
(d) range queries are impossible

<details><summary>Answer</summary>

**(a).**

A local (document-partitioned) index has each shard indexing only its own data. Writes are cheap — one shard — but a query by the secondary attribute must ask **every** shard and merge, so tail latency becomes the slowest shard's.

(b) describes a **global** (term-partitioned) index: the index is partitioned by the indexed term, so reads touch one shard but writes touch two and are no longer atomic — which is why global indexes are usually asynchronous and slightly stale.

There is no free option; DynamoDB's LSI and GSI are exactly this distinction.
</details>

---

**Q13.** Exactly-once message delivery across a network is
(a) achieved by Kafka  (b) achieved with acknowledgements  (c) **not achievable; the practical equivalent is at-least-once plus idempotent processing**  (d) achieved by TCP

<details><summary>Answer</summary>

**(c).**

The consumer processes a message and sends an ack; if the ack is lost, the broker redelivers. The consumer cannot distinguish "my ack was lost" from "I never got it". No protocol removes that ambiguity — you can only move where it lives.

Kafka's "exactly-once semantics" is transactional read-process-write **within Kafka**, which is genuinely useful and is not exactly-once delivery to an external system.

**The phrasing to use:** "at-least-once delivery plus idempotent consumers, which is observationally exactly-once."
</details>

---

**Q14.** The best autoscaling signal for a pool of queue consumers is
(a) CPU utilisation  (b) memory  (c) **queue depth / consumer lag**  (d) request count

<details><summary>Answer</summary>

**(c).**

Queue depth measures the backlog **directly**; CPU is only a proxy, and a consumer blocked on I/O shows low CPU while falling badly behind.

**Consumer lag is also the single best early-warning metric** for a queue-backed system: it grows before anything user-visible breaks, which is exactly what you want from an alert.
</details>

---

**Q15.** Jitter in a retry policy exists to
(a) make retries faster
(b) **prevent synchronised retry waves from many clients**
(c) improve security
(d) reduce memory

<details><summary>Answer</summary>

**(b).**

Without randomisation, every client that failed at the same instant retries at the same instant — and the recovering service is hit by a synchronised wave that knocks it over again, indefinitely.

This is not a minor refinement. Exponential backoff **without** jitter can turn a brief outage into a self-sustaining one, and naming jitter specifically is a signal that you have operated something.
</details>

---

**Q16.** A Bloom filter can produce
(a) false negatives only  (b) **false positives only**  (c) both  (d) neither

<details><summary>Answer</summary>

**(b) false positives only.**

If any of the k bits is 0, the element is **definitely** absent. If all are 1, they may have been set by other elements.

**Why the asymmetry is exactly right for its main use:** in an LSM-tree database, a false positive costs one unnecessary disk read, while a false negative would cost *correctness* by skipping a file that does contain the key. The error is on the affordable side.

**The number to know:** ~9.6 bits per element for a 1% false positive rate.
</details>

---

**Q17.** A client experiences a timeout on a payment request and retries. Without which mechanism might they be charged twice?
(a) Rate limiting  (b) **An idempotency key**  (c) A circuit breaker  (d) A load balancer

<details><summary>Answer</summary>

**(b) an idempotency key.**

The first request may have succeeded with the *response* lost, so the client genuinely cannot tell. The client supplies a unique key per logical operation; the server stores the key with its result and returns the stored result on a repeat instead of re-executing.

This is not an edge case — client timeouts are routine — so **every creating or charging endpoint should accept one**.
</details>

---

**Q18.** Cursor-based pagination is preferred over offset-based because
(a) it is simpler to implement
(b) **`OFFSET n` is O(n) in the database, and concurrent inserts shift items across page boundaries**
(c) it supports jumping to an arbitrary page
(d) it uses less bandwidth

<details><summary>Answer</summary>

**(b) — both halves matter.**

The database must **scan and discard** the skipped rows, so deep pagination gets progressively slower. And when rows are inserted while a user is paging, everything shifts, so they see duplicates or miss items entirely.

(c) is actually offset's one genuine **advantage** — cursor pagination cannot jump to page 47. That is usually an acceptable loss, and saying so demonstrates you understand the trade-off rather than reciting a rule.
</details>

---

**Q19.** Why should you not alert on average latency?
(a) Averages are hard to compute
(b) **An average hides the tail: 50 ms average is consistent with 5% of users at 850 ms**
(c) Averages change too fast
(d) You should — averages are the correct signal

<details><summary>Answer</summary>

**(b).**

Alert on **percentiles** — p99 is where most SLOs are written.

The deeper point is **tail latency amplification**: a page assembling 100 sub-requests, each with a 1% chance of exceeding p99, has a `1 − 0.99¹⁰⁰ ≈ 63%` chance of at least one slow call. **The p99 of a component becomes the typical experience of a composed page.**

A related trap: you cannot average per-server p99s to get a global p99 — you need a mergeable structure such as t-digest.
</details>

---

**Q20.** A service calls a dependency that has become very slow. Without a circuit breaker, the most likely outcome is
(a) the dependency recovers faster
(b) **the caller's threads block waiting, the caller becomes slow, and the failure cascades upstream**
(c) requests are automatically rerouted
(d) nothing — the caller just returns errors

<details><summary>Answer</summary>

**(b) cascading failure.**

Each blocked thread is a resource the caller cannot use for anything else. When all are blocked, the caller is effectively down — and its callers then block in turn.

The circuit breaker cuts this chain in both directions: the caller **fails fast** instead of consuming threads, and the struggling dependency gets breathing room to recover instead of being hammered.

**Bulkheads** attack the same problem differently, by isolating thread pools per dependency so one saturated dependency cannot starve the others. **Timeouts** are the prerequisite for both — without a timeout, nothing ever fails fast.
</details>

---

## Scoring

| Score /20 | Reading |
|---|---|
| 17+ | Fundamentals are solid; move to the case studies |
| 12–16 | Re-read the files behind the missed questions |
| 7–11 | Work through `01`–`06` properly before attempting a design round |
| < 7 | Start at `framework.md` and read in order |

**Diagnostic:** misses on Q5, Q6, Q11, Q12 mean the *failure modes* have not landed — which is exactly what deep dives probe. Those four files (`02`, `04`) are the highest-yield re-read.
