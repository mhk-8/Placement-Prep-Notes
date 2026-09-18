# Consistency, CAP and Distributed Agreement

> The most misquoted area in system design, and therefore the one where precision pays most. This file aims to let you say exactly what you mean.

---

## 1. What "consistency" means — three different things

The word is overloaded, and conflating the senses is the most common imprecision:

1. **The C in ACID** — a transaction moves the database from one valid state to another, respecting declared constraints. This is really about **integrity**, and it is the application's and the schema's responsibility.
2. **The C in CAP** — **linearizability**: the system behaves as if there were a single copy of the data and every operation took effect atomically at some instant between its start and its completion.
3. **Consistency models generally** — the spectrum of guarantees about what a read may return, from linearizable down to eventual.

When an interviewer asks "what consistency do you need?", they mean sense 2 or 3. Answer in terms of **what a user may observe**, not in terms of ACID.

---

## 2. The consistency spectrum

From strongest to weakest:

**Linearizable (strong / atomic).** Every read returns the most recent completed write, and the ordering respects real time. If write W completes at 10:00:00.000, every read starting after that instant sees it — on any node. This is the most intuitive model and the most expensive: it requires coordination, which costs at least one round trip between replicas and forfeits availability during a partition.

**Sequential consistency.** All nodes see operations in the *same* order, but that order need not match real time. Weaker than linearizable and rarely the model a system advertises.

**Causal consistency.** Operations that are causally related are seen in the same order by everyone; concurrent operations may be seen in different orders. If A posts and B replies, nobody sees the reply before the post — but two unrelated posts may appear in different orders to different users. **This is often exactly what an application needs**, and it is achievable without global coordination (using version vectors or Lamport timestamps).

**Read-your-writes.** A user always sees their own writes. Others may not yet. Implemented by routing a user's reads to the leader for a window after their write, or by passing a token carrying the write's log position and waiting for a replica to catch up.

**Monotonic reads.** A user never sees time run backwards. Implemented by pinning a user to one replica.

**Eventual consistency.** If writes stop, replicas converge. Says nothing about *when*, and nothing about what you see meanwhile. It is the weakest useful guarantee — and it is the default in many NoSQL stores, so if you choose one, say what staleness the product tolerates.

**The practical point:** the middle models (causal, read-your-writes, monotonic reads) are where most real systems should sit. "Eventually consistent, except the user's own writes are read-your-writes" is a much better answer than either extreme.

---

## 3. CAP, stated correctly

**During a network partition, a distributed system must choose between consistency and availability.**

Three clarifications that matter:

1. **Partition tolerance is not optional.** Networks partition — cables are cut, switches fail, packets are dropped. A single-node system can be CA; a distributed one cannot refuse P. So the real choice is **CP or AP**.
2. **CAP says nothing about the non-partitioned case.** Most of the time there is no partition, and a system can be both consistent and available. Quoting CAP to justify weak consistency during normal operation is a misuse.
3. **It is a binary idealisation.** Real systems tune per-operation (Cassandra's consistency levels) and the choice is rarely global.

**CP systems** (choose consistency): HBase, ZooKeeper, etcd, Spanner, and a leader-based relational database during failover. When partitioned, the minority side refuses to serve rather than risk stale or conflicting data. **Correct for:** balances, inventory, locks, configuration, leader election.

**AP systems** (choose availability): Cassandra, DynamoDB (in its eventually-consistent mode), Riak, DNS. When partitioned, every side keeps serving and reconciles later. **Correct for:** feeds, view counts, shopping carts, product catalogues, caches.

### PACELC — the fuller statement

> If there is a **P**artition, choose **A** or **C**; **E**lse (normal operation), choose **L**atency or **C**onsistency.

This is the more useful framing, because the else-branch is where systems actually spend their time. Synchronous cross-region replication costs 100+ ms of latency on every write; asynchronous costs consistency. **That trade-off exists with no partition at all**, and CAP alone does not capture it.

| System | PACELC |
|---|---|
| Cassandra, Dynamo | PA/EL — available when partitioned, low latency otherwise |
| Postgres (single leader) | PC/EC — consistent throughout |
| Spanner | PC/EC — pays latency for global consistency |
| MongoDB (default) | PA/EC |

---

## 4. Consensus

Consensus is agreement on a single value among nodes that may fail. It is the primitive underneath leader election, configuration management, distributed locks, and atomic commit.

**FLP impossibility:** in a fully asynchronous system with even one faulty process, no deterministic algorithm guarantees consensus. Real systems work around it with **timeouts** — i.e. by assuming partial synchrony. This is why every consensus system has a timeout knob and why tuning it badly causes spurious elections.

### Raft, in enough detail to describe

Three roles: **leader**, **follower**, **candidate**.

**Leader election.** Every follower runs a randomised election timeout. On expiry it becomes a candidate, increments the term, votes for itself, and requests votes. A candidate winning a **majority** becomes leader. The randomised timeouts make simultaneous candidacies rare, and a split vote simply triggers another round.

**Log replication.** Clients send commands to the leader, which appends to its log and replicates to followers. Once a majority have acknowledged, the entry is **committed** and applied to the state machine.

**Safety.** A candidate cannot win unless its log is at least as up to date as the majority's, which guarantees a committed entry is never lost.

**Why a majority quorum:** any two majorities of an odd-sized cluster intersect in at least one node, so a newly elected leader necessarily knows about every committed entry. It also makes split brain impossible — two disjoint majorities cannot exist.

**Cluster sizes are odd** (3, 5, 7) because an even size adds a failure to tolerate without adding fault tolerance: 4 nodes tolerate 1 failure, exactly as 3 do, while being more expensive and more likely to have *some* node down.

**Paxos** solves the same problem and came first; Raft was designed to be understandable and is what most modern systems implement. **ZooKeeper** uses ZAB, a close relative.

**What you actually use consensus for** — and the honest answer is: you use a system that implements it (etcd, ZooKeeper, Consul) rather than implementing it yourself. Leader election, service discovery, distributed locks, and the shard map all live there.

---

## 5. Distributed transactions

### Two-phase commit (2PC)

**Phase 1 (prepare).** The coordinator asks every participant to prepare; each does the work, makes it durable, and answers yes or no — a promise it can still commit.
**Phase 2 (commit/abort).** If all said yes, the coordinator tells everyone to commit; otherwise abort.

**The fatal flaw:** it is **blocking**. If the coordinator fails after participants have promised but before they are told the outcome, participants hold their locks and cannot unilaterally decide — they are stuck until the coordinator returns. In a microservice architecture this is a genuine outage mode, which is why 2PC is rare across service boundaries and common only inside a single database.

### Sagas

Model a long transaction as a sequence of **local** transactions, each with a **compensating action** that semantically undoes it.

```
Order placed  →  Payment charged  →  Inventory reserved  →  Shipment created
                                            │ fails
                                            ▼
              Refund payment   ←   Cancel order   ←   (compensations run backwards)
```

Two flavours: **choreography** (each service emits events others react to — decentralised, but the overall flow is hard to see) and **orchestration** (a coordinator drives the steps — explicit and debuggable, at the cost of a central component).

**What you give up:** atomicity and isolation. Intermediate states are visible — a customer may briefly see a charge before the order is confirmed. That has to be acceptable to the product, and designing the compensations is the hard part (you cannot "un-send" an email; you send an apology).

### The outbox pattern

The recurring practical problem: you must update the database **and** publish an event, atomically. Doing both directly is a distributed transaction.

**The solution:** write the event into an `outbox` table **in the same local transaction** as the business change. A separate process reads the outbox and publishes. Atomicity is local, publication is at-least-once, and consumers must be idempotent. This is the standard answer and worth knowing by name.

### Idempotency

Because retries are inevitable, every mutating operation should be safe to apply twice. The usual mechanism is an **idempotency key**: the client generates a unique key per logical operation, the server records it with the result, and a repeat returns the stored result instead of re-executing.

**"Exactly-once delivery" is not achievable** over an unreliable network. What is achievable is **at-least-once delivery plus idempotent processing**, which is observationally equivalent — and phrasing it that way is a precise, credible answer.

---

## 6. Choosing, in practice

| Data | Model needed | Why |
|---|---|---|
| Account balance | linearizable | double-spend must be impossible |
| Inventory at checkout | linearizable on decrement | overselling has a real cost |
| Seat booking | linearizable | two people, one seat |
| Social feed | eventual + read-your-writes | staleness is invisible; your own post must appear |
| View counter | eventual | nobody can tell |
| Username uniqueness | linearizable | a uniqueness constraint is a consensus problem |
| Shopping cart | eventual, merge on conflict | Amazon's canonical AP choice |
| Configuration / feature flags | linearizable | consistency matters more than availability |
| DNS | eventual | availability matters more than freshness |

**Ask the business question, not the technical one.** "If two users book the last seat at the same instant, what should happen?" produces a clearer answer than "do we need strong consistency?".

---

## 7. Recall questions

1. Give the three distinct meanings of "consistency" and say which one CAP uses.
2. Define linearizability precisely.
3. What is causal consistency, and why is it often the right target?
4. How is read-your-writes implemented?
5. State CAP correctly, including the two things it does *not* claim.
6. Why is partition tolerance not a real choice?
7. State PACELC and explain why the "else" branch matters more in practice.
8. What is the FLP result, and how do real systems get around it?
9. Describe Raft's leader election and log replication.
10. Why does a majority quorum guarantee no split brain and no lost committed entries?
11. Why are consensus clusters an odd size?
12. Describe 2PC and its fatal flaw.
13. Describe a saga, and say what guarantee it gives up.
14. What problem does the outbox pattern solve, and how?
15. Why is exactly-once delivery not achievable, and what is the correct phrasing instead?
16. For each of: balance, feed, cart, username uniqueness — state the consistency model and why.
