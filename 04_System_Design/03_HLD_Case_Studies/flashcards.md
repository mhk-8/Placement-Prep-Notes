# HLD Case Studies — Flashcards

> The reusable lessons, extracted from the eleven case studies. If you can answer these, you can reassemble any of the designs.

## Questions

**Framework and estimation**
1. Give the six stages of a design round with their rough timings.
2. What is the single most valuable clarifying question, and why?
3. What must you do with every estimate you compute?
4. Give the four numbers to compute in the estimation stage.
5. What are the shortcuts for seconds/day, 1M/day and 1 KB × 1 billion?

**URL shortener**
6. Why 7 base62 characters, and how many codes is that?
7. Compare the three code-generation approaches and say which you would choose.
8. Why 302 rather than 301, and what does that cost?
9. Why is the click counter not incremented synchronously?

**Rate limiter**
10. Why not check Redis on every request at 1M QPS, and what replaces it?
11. What does the local-allowance design give up, and is that acceptable?
12. When do you fail open and when do you fail closed?

**News feed**
13. What number does the estimation uncover, and how is it computed?
14. Explain fan-out on write versus on read, and why each fails alone.
15. State the hybrid rule and why the follower distribution makes it work.
16. How is read-your-writes achieved despite asynchronous fan-out?
17. Why are two follow tables stored?

**Chat**
18. What forces a separate connection tier, and what is the discovery problem?
19. Why are client timestamps unusable for ordering, and what replaces them?
20. How is at-least-once delivery made observationally exactly-once?
21. Why is presence computed on demand rather than broadcast?

**Video**
22. Which single number forces the CDN, and what is its value?
23. Why is the video split into segments before transcoding?
24. Why does the client, not the server, choose the bitrate?

**Ride hailing**
25. Why does distance-to-all-drivers fail, and what replaces it?
26. Why must a geohash query include the 8 neighbouring cells?
27. Why does location live in Redis rather than a durable store?
28. What prevents a driver being assigned two trips?

**Typeahead**
29. What does the latency budget rule out, and what is the consequence?
30. What is cached at every trie node, and what does it cost?
31. Why shard by prefix rather than by hash?
32. Why does personalisation hurt, and what is the compromise?

**File storage**
33. What four things does chunking buy?
34. Why are metadata and blobs stored separately?
35. How are concurrent offline edits detected, and how are they resolved?
36. What is the privacy cost of global deduplication?

**Notifications**
37. Why separate topics per priority rather than one topic with a priority field?
38. What are the two things an idempotency key prevents here?
39. Why one worker per channel?

**Crawler**
40. Why a Bloom filter, and how much does it save at 10B URLs?
41. Describe the two-stage frontier and what each stage solves.
42. Why is the politeness delay proportional to response time?
43. Why partition the frontier by host?

**Payments**
44. What does the estimation reveal about this system, and why does it matter?
45. Why double-entry rather than a balance column? Give three benefits.
46. Give the four steps of the idempotency-key mechanism and the role of `request_hash`.
47. Why a saga rather than 2PC, and what does it give up?
48. Why is reconciliation not optional?

---

## Answers

1. Requirements (0–5), estimation (5–10), API and data model (10–15), high-level design (15–25), deep dive (25–40), bottlenecks and wrap-up (40–45).
2. The consistency question — "if two users book the last seat simultaneously, what should happen?" — because the answer determines the entire storage strategy.
3. Connect it to a design decision. An estimate you do not use is theatre.
4. QPS (and peak QPS); storage (with replication and retention); bandwidth; and the cache working set.
5. 86,400 ≈ 10⁵ s/day; 1M/day ≈ 12/s; 1 KB × 1 billion = 1 TB.
6. 62⁷ ≈ 3.5 × 10¹² codes — roughly 2,900 years of supply at 100M/month, where 6 characters gives only about a decade.
7. Hash-and-truncate (deduplicates for free, needs collision handling); counter + base62 (no collisions, but enumerable unless encrypted); pre-generated key pool (no collisions on the hot path). Choose the key pool, or the counter with a format-preserving cipher.
8. 302 keeps every click coming to you, preserving analytics and the ability to revoke. The cost is that the browser never caches the redirect, so you carry all the read load.
9. 12,000 redirects/second would mean 12,000 writes/second on the hottest rows. Emit an event to Kafka and aggregate asynchronously.
10. It costs a ~0.5 ms round trip per request and requires a Redis cluster sized for 1M ops/second purely for limiting. Local token buckets per gateway with periodic sync replace it.
11. Bounded over-permissiveness between syncs — roughly the sum of unspent local allowances. For capacity protection that is acceptable; for login attempts it is not.
12. Fail open for ordinary APIs, so the limiter cannot cause the outage it exists to prevent. Fail closed for security-critical paths like login and payments.
13. 1.2 million timeline writes per second: 6,000 posts/second × 200 average followers.
14. Push writes the post into every follower's timeline (cheap reads, catastrophic for celebrities). Pull assembles at read time (cheap writes, catastrophic at 90,000 reads/second for users following many accounts).
15. Push for ordinary users, pull for accounts above a follower threshold. It works because the distribution is extremely skewed: most users have few followers, and each user follows only a handful of celebrities, so the read-time merge stays small.
16. Insert the post into the author's own timeline **synchronously** on the write path, before returning.
17. One partitioned by follower (answering "whose posts do I merge") and one by followee (answering "who do I fan out to"). Both are hot queries, and a single table would force a scatter-gather on one of them.
18. 5 million concurrent WebSocket connections, which will not fit on a few servers. The discovery problem is finding **which connection server currently holds a given user**, solved with a Redis session map (with TTLs) or a per-user pub/sub channel.
19. Device clocks are wrong and adversarial. The server assigns a monotonic per-conversation sequence number, and clients sort by it and can detect gaps.
20. A client-generated message id carried end to end, with a conditional insert on the server and deduplication by id on the recipient.
21. Broadcasting every status change to every contact for 5 million churning users is enormous volume for very little value. Instead heartbeat into Redis with a TTL and compute presence only for contacts currently on screen.
22. 2.5 Tbps of peak egress (500,000 concurrent streams × 5 Mbps), which no origin can serve.
23. Segments are independent, so encoding parallelises massively across a worker fleet — turning one long job into thousands of short ones. It also enables adaptive bitrate switching at segment boundaries.
24. The client knows its own throughput and buffer level; and keeping the decision client-side means the server serves static, cacheable files, which is what makes the CDN work.
25. A full scan of 1M drivers per match request, with an expensive distance computation each. Geohash or H3 cells replace it, turning proximity into a prefix or cell lookup.
26. A driver 50 metres away can be just across a cell boundary and would otherwise be invisible. Omitting the neighbours is the classic bug in this design.
27. It is written 250,000 times a second, it is worthless after 30 seconds, and losing it costs one 4-second update cycle. A durable store would be the bottleneck for no benefit.
28. A conditional update — `UPDATE drivers SET status='ASSIGNED' WHERE driver_id=? AND status='AVAILABLE'` — with the affected row count as the verdict. The Redis read is advisory.
29. 200,000 QPS with a ~50 ms budget rules out computing anything at request time. So the answer must be precomputed and the read path must be a lookup.
30. The top k completions for that prefix. It costs substantial memory (10–20 GB for 100M queries) and makes updates expensive, since a frequency change must propagate to every ancestor — which is why updates are batched offline.
31. Prefix sharding routes a request to exactly one shard; hash sharding would require a scatter-gather across all shards for every keystroke.
32. It makes the answer per-user, so the shared cache stops working. The compromise is to serve a shared top-k and re-rank a small candidate set locally.
33. Resumable transfer, delta sync (only changed chunks re-uploaded), deduplication across all users, and parallel transfer. Cheap versioning is a fourth consequence.
34. 10 PB of immutable blobs and 2.5 TB of relational metadata are entirely different storage problems, with different access patterns, cost profiles and query needs.
35. Detected with version vectors — if neither version's vector dominates the other, the edits were concurrent. Resolved by keeping both as a conflicted copy, because automatic merge is impossible for binary files.
36. A user can infer whether a file already exists in the system by observing that their upload was skipped.
37. Most brokers have no true priority, so separate topics are the practical mechanism — and it means a 50-million-message campaign cannot delay an OTP.
38. A duplicate notification from a producer retry, and a duplicate OTP — both of which are user-visible failures.
39. Each channel has a different provider, different rate limits, different retry policy and different failure modes; separating them lets each scale and fail independently.
40. 10 billion URLs as an exact hash set is ~160 GB; a Bloom filter at 1% is ~12 GB — a 13× saving, and the error is harmless (a false positive skips a new URL, a false negative merely re-crawls).
41. Front queues hold URLs by priority and a biased selector draws from them; back queues each hold one host, so politeness is enforced structurally. A min-heap of next-allowed-fetch times tells workers which back queue is ready.
42. It makes the crawler automatically gentlest with the servers that are already struggling.
43. It makes politeness a purely **local** decision requiring no coordination between crawler nodes.
44. That 1,000 TPS is small and fits one Postgres instance — so the design problem is correctness, not scale. It matters because it prevents reflexively designing a distributed system where a transactional one is simpler and safer.
45. Balances become derived and verifiable; the zero-sum invariant is continuously checkable; entries are append-only so there is a complete audit trail **and no hot-row contention**.
46. The client sends a unique key; the server inserts it under a unique constraint in the same transaction as the work; on success it does the work and stores the response; on constraint violation it returns the stored response. `request_hash` detects a client reusing a key with a different payload.
47. 2PC across an external provider would block on coordinator failure. The saga gives up atomicity and isolation — intermediate states are visible — and designing the compensating actions is the hard part.
48. Because it is how you discover the discrepancies that idempotency and sagas did not prevent. A payment design without reconciliation is incomplete.
