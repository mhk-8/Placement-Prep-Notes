# Messaging, Queues and Event Streaming

> Queues are how you decouple components in time. Almost every "make it scale" answer eventually involves moving work off the request path, and this file is the mechanism behind that move.

---

## 1. Why asynchronous processing

A synchronous request path is only as fast and as available as its slowest, least reliable dependency. If placing an order must synchronously charge a card, decrement inventory, send an email, update analytics and notify a warehouse, then the email provider being slow makes checkout slow, and the email provider being down makes checkout **fail**.

Moving non-essential work behind a queue gives four things:

1. **Latency** — the user waits only for what they need. Checkout returns as soon as the order is durable.
2. **Decoupling** — the producer does not know or care who consumes, or whether they are up right now.
3. **Load smoothing** — a spike is absorbed by the queue and drained at the consumers' pace, instead of overwhelming them. This is the single most valuable property under bursty load.
4. **Resilience** — a consumer can be down for ten minutes and lose nothing; it catches up.

**The costs, which must be stated:** eventual consistency (the work is not done when the response returns), operational complexity (another system to run and monitor), harder debugging (the causal chain spans processes), and the need for **idempotent consumers**, because delivery is at-least-once.

---

## 2. Queue vs pub/sub vs log

Three genuinely different shapes, and using the wrong word is a tell.

| | Message queue | Pub/sub | Event log |
|---|---|---|---|
| Delivery | to **one** consumer | to **every** subscriber | to every consumer group |
| After consumption | message is deleted | message is gone | **retained** for a period |
| Replay | no | no | **yes** |
| Ordering | usually none | none | **within a partition** |
| Consumer tracks | nothing | nothing | its own **offset** |
| Examples | SQS, RabbitMQ (work queue) | SNS, Redis pub/sub | **Kafka**, Pulsar, Kinesis |

**Queue** — work distribution. Ten workers pull from one queue; each task is done once. Adding workers increases throughput.

**Pub/sub** — event broadcast. One "order placed" event reaches billing, shipping, analytics and email independently.

**Log** — an ordered, durable, replayable sequence. Because messages are retained and consumers hold their own offset, a new consumer can read history from the beginning, and a buggy consumer can be fixed and **replayed**. This replayability is what makes Kafka qualitatively different, and it is the property to name when you choose it.

**Interview phrasing:** "I need durable, ordered, replayable delivery so a new downstream service can rebuild its state from history — that's Kafka. If I only needed work distribution with no ordering, SQS would be simpler and cheaper."

---

## 3. Delivery semantics

| Semantics | Mechanism | Risk |
|---|---|---|
| **At-most-once** | ack before processing | message lost if the consumer crashes mid-work |
| **At-least-once** | ack after processing | **duplicates** if the ack is lost after the work is done |
| **Exactly-once** | not achievable end to end over an unreliable network | — |

**At-least-once is the practical default**, and the duplicate problem is pushed to the consumer, which must be **idempotent**.

**Why exactly-once is not real:** the consumer processes the message and then sends an ack; if the ack is lost, the broker redelivers. The consumer cannot distinguish "my ack was lost" from "I never received it". No amount of protocol removes this — you can only move where the ambiguity lives.

**What systems actually offer** — Kafka's "exactly-once semantics" — is transactional writes *within Kafka* (read-process-write atomically across topics), which is genuinely useful but is not exactly-once delivery to an external system. Saying "at-least-once delivery plus idempotent processing, which is observationally exactly-once" is the precise and credible phrasing.

**Making a consumer idempotent:**
- A natural idempotent operation (`SET status = 'paid'` rather than `balance = balance - 10`).
- A processed-message table keyed by message id, checked in the same transaction as the work.
- An idempotency key supplied by the producer.
- A conditional write (compare-and-set on a version).

---

## 4. Ordering

**Global ordering across a distributed queue is expensive and usually unnecessary.** What systems provide is ordering **within a partition** (Kafka) or within a message group (SQS FIFO).

The design consequence: **choose the partition key so that messages which must be ordered share a key.** Partition by `user_id`, and that user's events are ordered relative to each other; different users interleave freely, which is fine.

**The trade-off:** ordering constrains parallelism. One partition is consumed by one consumer in a group, so the number of partitions caps your consumer parallelism. Needing strict global ordering means one partition means one consumer means no horizontal scaling — which is why you should push back on "we need global ordering" and ask what actually must be ordered.

---

## 5. Kafka, in enough detail

**Structure.** A **topic** is split into **partitions**. Each partition is an ordered, append-only log on disk, replicated across brokers. A message's position in a partition is its **offset**.

```
Topic: orders
  Partition 0:  [0][1][2][3][4][5]  ← append only
  Partition 1:  [0][1][2][3]
  Partition 2:  [0][1][2][3][4][5][6]
                              ▲
                     consumer group A offset
```

**Producers** choose a partition by key (`hash(key) mod partitions`) or round robin if no key. **Consumers** belong to a **consumer group**; each partition is assigned to exactly one consumer in the group, so the group collectively sees each message once, and adding consumers beyond the partition count adds nothing.

**Retention** is by time or size, not by consumption — messages persist after being read, which is what enables replay.

**Why Kafka is fast:** sequential disk writes (an append is faster than a random write, and sequential disk can outrun random memory access), the OS page cache rather than a userspace cache, zero-copy transfer from disk to network, and batching plus compression.

**Replication.** Each partition has a leader and followers. Producers write to the leader; `acks=all` waits for all in-sync replicas, `acks=1` waits only for the leader, `acks=0` does not wait. This is the same durability-versus-latency dial as database replication.

**Consumer lag** — the gap between the latest offset and the consumer's offset — is **the metric to monitor**. Growing lag means consumers cannot keep up, and it is the early warning before a backlog becomes an incident.

---

## 6. Operational patterns

### Dead letter queue
After N failed processing attempts, move the message to a **dead letter queue** rather than retrying forever. Without one, a single malformed message blocks a partition indefinitely — the "poison pill" problem. The DLQ must be monitored and drained, or it becomes a silent data-loss mechanism.

### Retry with backoff
Retry transient failures with exponential backoff **and jitter**. A common structure is tiered retry topics (`retry-5s`, `retry-1m`, `retry-10m`) so a failing message does not block fresher ones behind it.

### Backpressure
When consumers fall behind, the queue grows. Options: autoscale consumers on **queue depth** (a much better signal than CPU), shed low-priority messages, or push back on producers. Doing nothing means the queue grows until storage or retention limits are hit and messages are silently dropped.

### Priority
Most brokers do not support true priority. The practical approach is **separate queues per priority** with consumers weighted towards the high-priority one, so low-priority work cannot starve the important path.

### Fan-out
One event, many consumers. In Kafka this is several consumer groups on one topic; with SNS+SQS it is one topic fanning into several queues, which gives each consumer its own retry and DLQ behaviour — often worth the extra component.

### The outbox pattern
Publishing an event **and** updating the database atomically is a distributed transaction. Instead, write the event to an `outbox` table in the same local transaction, and have a relay publish it. Local atomicity, at-least-once publication, idempotent consumers. (Also in `05`; it is the single most reused pattern in this area.)

---

## 7. Choosing a broker

| Need | Choice | Why |
|---|---|---|
| Simple work queue, managed, no ordering | **SQS** | cheapest to operate, scales automatically |
| Ordering, replay, high throughput, multiple consumers | **Kafka** | the log model |
| Complex routing, per-message priority, request/reply | **RabbitMQ** | rich exchange/routing semantics |
| Fan-out to many subscribers | **SNS**, or Kafka consumer groups | broadcast |
| Task scheduling with delays and retries | **Celery**, Sidekiq, a managed scheduler | application-level ergonomics |
| In-process, tolerant of loss | an in-memory queue | no operational cost |

**Do not reach for Kafka by default.** It is a substantial operational commitment (brokers, ZooKeeper or KRaft, partitions, consumer groups, rebalancing). If the requirement is "send an email after signup", a database-backed job table with a worker polling it is a legitimate, much simpler answer — and saying so demonstrates judgement.

---

## 8. Where queues fit in a design

Typical placements, worth having ready:

- **Checkout** → order written synchronously; email, analytics, warehouse notification, recommendation update all asynchronous.
- **Uploads** → store the blob, enqueue thumbnailing and transcoding.
- **Feed** → a post enqueues fan-out to followers' timelines.
- **Search** → database writes emit change events that update the search index asynchronously.
- **Notifications** → all delivery channels behind a queue, with per-channel retry.
- **Rate-limited third parties** → a queue plus a token-bucket consumer paces calls to an external API that would otherwise reject you.

---

## 9. Recall questions

1. Give the four benefits of asynchronous processing and the four costs.
2. Distinguish a queue, pub/sub and an event log on delivery, retention and replay.
3. What single property makes Kafka qualitatively different from SQS?
4. Why is exactly-once delivery not achievable, and what is the correct phrasing?
5. Give four ways to make a consumer idempotent.
6. What ordering does Kafka actually guarantee, and what is the design consequence?
7. Why does needing global ordering destroy horizontal scaling?
8. Explain partitions, consumer groups, and why adding consumers beyond the partition count is useless.
9. Give four reasons Kafka is fast.
10. What is consumer lag and why is it the key metric?
11. What is a poison pill, and what prevents it from blocking a partition?
12. Why is queue depth a better autoscaling signal than CPU?
13. How do you implement priority when the broker does not support it?
14. State the outbox pattern and the problem it solves.
15. Give a case where a database-backed job table is the right answer instead of Kafka.
