# Rate Limiting and Throttling

> Rate limiting protects a system from being consumed faster than it can serve — by abusive clients, by buggy ones, and by success. It is also a complete small design problem in its own right, which is why it appears as both a component and a standalone interview question.

---

## 1. Why rate limit

1. **Protect capacity.** Keep one heavy client from exhausting resources everyone shares.
2. **Prevent abuse.** Credential stuffing, scraping, denial of service.
3. **Control cost.** Downstream calls (a paid API, an LLM, an SMS gateway) cost money per call.
4. **Enforce a business model.** Free tier 100 requests/hour, paid tier 10,000.
5. **Fairness.** Multi-tenant systems must stop one tenant degrading another.

**What to limit by** — and this is a genuine design choice:

| Key | Fits | Weakness |
|---|---|---|
| API key / user id | authenticated APIs | requires authentication first |
| IP address | unauthenticated endpoints | NAT puts thousands behind one IP; attackers rotate IPs |
| IP + endpoint | login, signup | finer grained |
| Tenant / organisation | B2B | one user can still consume the tenant's quota |
| Global | protecting a fragile dependency | no per-client fairness |

Real systems layer several: a global limit protecting the service, a per-tenant limit for fairness, and a per-endpoint limit for expensive operations.

---

## 2. The algorithms

### Fixed window counter

Count requests per fixed interval; reset at each boundary.

```
key = f"{user}:{minute}"
count = INCR(key)
if count == 1: EXPIRE(key, 60)
allow = count <= limit
```

**Pros.** Trivial, one counter, minimal memory.
**Con — the boundary burst.** With a limit of 100/minute, a client can send 100 at 10:00:59 and 100 more at 10:01:00 — **200 requests in one second** while never violating the stated limit. This is the standard criticism and worth naming.

### Sliding window log

Store the timestamp of every request; on each request, drop entries older than the window and count what remains.

```
ZREMRANGEBYSCORE key 0 (now - window)
ZADD key now request_id
count = ZCARD key
allow = count <= limit
```

**Pros.** Exactly correct — no boundary artefact, precise to the millisecond.
**Cons.** Memory is O(requests in the window) **per client**. A limit of 10,000/hour means 10,000 stored timestamps per user. At scale this is too expensive.

### Sliding window counter — the usual choice

Approximate the sliding window by weighting the previous fixed window by how much of it still overlaps.

```
estimate = current_window_count
         + previous_window_count × (overlap fraction of the previous window)

e.g. 30 seconds into the current minute:
     estimate = 30 + 80 × 0.5 = 70
```

**Pros.** Near-constant memory (two counters), and the boundary burst is largely eliminated.
**Cons.** An approximation — it assumes requests were uniformly distributed in the previous window, so it can be slightly off in either direction.

**This is what most production rate limiters use**, and it is the right default answer.

### Token bucket — the best for bursts

A bucket holds up to `capacity` tokens and refills at `rate` tokens per second. Each request consumes a token; if the bucket is empty, the request is rejected (or queued).

```
refill = (now - last_refill) × rate
tokens = min(capacity, tokens + refill)
if tokens >= 1:
    tokens -= 1
    allow
else:
    reject
```

**Pros.** **Allows bursts up to the bucket capacity while bounding the long-run average rate** — which matches how real clients behave (idle, then a flurry). Memory is two numbers per client. It is also lazy: no background timer is needed, tokens are computed on access.

**Cons.** Two parameters to tune, and a burst can still momentarily overwhelm a fragile downstream.

**This is the algorithm to name when burst tolerance matters**, and it is what most API gateways implement.

### Leaky bucket

Requests enter a queue and are processed at a fixed rate; overflow is dropped.

**Pros.** Perfectly smooth output — the downstream sees a constant rate regardless of the input shape.
**Cons.** No burst tolerance at all, and queuing adds latency.

**Fits:** shaping traffic towards a fragile or rate-limited downstream — paying an external API at exactly its allowed rate, for example.

### Comparison

| Algorithm | Memory/client | Burst allowed | Accuracy | Use |
|---|---|---|---|---|
| Fixed window | O(1) | boundary burst (2× limit) | poor at edges | quick and dirty |
| Sliding log | O(n) | none | exact | low-volume, high-value |
| Sliding counter | O(1) | minimal | approximate | **general default** |
| Token bucket | O(1) | **up to capacity** | exact average | **APIs, burst-tolerant** |
| Leaky bucket | O(queue) | none | exact output rate | traffic shaping |

---

## 3. Distributed rate limiting

With many application servers, a per-instance limit is wrong: ten servers each allowing 100/minute permits 1,000/minute.

**Option 1 — centralised counter (Redis).** All instances increment a shared counter.
*Pros:* accurate. *Cons:* a network round trip on every request (~0.5 ms), and Redis becomes a dependency of every request and a single point of failure.

Make the check atomic with a **Lua script**, so read-modify-write cannot interleave:
```lua
local current = redis.call('INCR', KEYS[1])
if current == 1 then redis.call('EXPIRE', KEYS[1], ARGV[1]) end
return current
```
Without atomicity, two concurrent requests can both read 99 and both proceed.

**Option 2 — local buckets with a shared budget.** Each instance holds a local allowance and periodically syncs with a central store. Far fewer round trips; slightly over-permissive at the boundaries. This is what high-throughput gateways do.

**Option 3 — sticky routing.** Route a client consistently to one instance (consistent hashing on the client key) so its counter is local. No round trip, but rebalancing loses state, and the routing constraint is a real cost.

**What to do when the store is unreachable** is a genuine design question. **Fail open** (allow the request) keeps the service available but removes protection exactly when things are already going wrong. **Fail closed** (reject) protects the backend but turns a Redis blip into a full outage. Most systems fail open for ordinary APIs and fail closed for security-critical paths like login. Volunteering this choice is a good signal.

---

## 4. Responses and client contract

Return **HTTP 429 Too Many Requests**, with headers so a well-behaved client can adapt:

```
HTTP/1.1 429 Too Many Requests
Retry-After: 30
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1758200400
```

Returning `Remaining` on **every** response — not only on rejections — lets clients self-throttle before hitting the wall, which reduces rejected traffic overall.

**Do not silently drop.** A client that gets no response retries immediately and makes things worse; one that gets a 429 with `Retry-After` backs off.

---

## 5. Placement

| Layer | What it catches | Note |
|---|---|---|
| CDN / edge | volumetric floods | cheapest place to reject; never reaches your origin |
| API gateway | per-client API quotas | **the usual home** |
| Service | per-endpoint expensive operations | finer grained, knows the cost |
| Database / dependency | last-resort protection | connection pool limits, admission control |

**Reject as early as possible.** A request rejected at the edge costs nothing; one rejected after authentication, deserialisation and three internal calls has already consumed most of its cost.

---

## 6. Beyond simple limits

**Adaptive / concurrency limiting.** Instead of a fixed rate, bound **concurrent in-flight requests** and adjust the bound based on observed latency (an additive-increase/multiplicative-decrease loop, like TCP congestion control). This adapts automatically when the system is having a bad day, where a static limit does not.

**Cost-based limiting.** Not all requests are equal. Charge tokens by expense — a simple read costs 1, a complex aggregation costs 50. This is how cloud provider APIs and GraphQL rate limits work, and it is a sophisticated answer to "how do you stop one expensive query from consuming the budget".

**Tiered limits.** Different quotas per plan, usually with burst allowance scaling too.

**Quotas vs rate limits.** A rate limit is per second or minute (protecting capacity); a quota is per month (enforcing billing). Both exist and they are different mechanisms.

---

## 7. A complete design sketch

Requirements: 10 million users, per-user 1,000 requests/hour with bursts up to 100, rate-limit check must add under 5 ms.

```
Client → CDN (volumetric protection)
       → API Gateway
             │ extract API key
             ▼
       Rate limiter (token bucket, Redis-backed, Lua-atomic)
             │ allow → forward
             │ deny  → 429 + Retry-After
             ▼
       Service
```

- **Algorithm:** token bucket — burst 100, refill 1,000/3,600 ≈ 0.28 tokens/second.
- **Storage:** Redis, key `rl:{api_key}`, value `{tokens, last_refill}`, TTL a few hours to reclaim idle keys.
- **Memory:** ~50 bytes per active key; 1 million active keys ≈ 50 MB — trivially one node, though shard by key for availability.
- **Atomicity:** a Lua script performing refill, check and decrement in one round trip.
- **Availability:** Redis with a replica; **fail open** on unavailability, with an alert, because the API is not security-critical.
- **Observability:** rate of 429s per tenant, p99 of the limiter check, Redis latency.

---

## 8. Recall questions

1. Give five reasons to rate limit and three possible limiting keys with their weaknesses.
2. Describe the fixed-window boundary burst with numbers.
3. Why is the sliding window log impractical at scale?
4. Give the sliding-window-counter estimate formula and its assumption.
5. Why does token bucket suit real API clients better than a strict rate?
6. When is leaky bucket the right choice?
7. Why must the distributed counter update be atomic, and how?
8. Compare centralised counters, local buckets with sync, and sticky routing.
9. When would you fail open versus fail closed, and why?
10. What headers should a 429 carry, and why return them on success too?
11. Why reject as early in the path as possible?
12. What is concurrency-based adaptive limiting, and what does it fix?
13. What is cost-based limiting, and where is it used?
14. Distinguish a rate limit from a quota.
