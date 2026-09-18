# HLD — Distributed Rate Limiter

> A compact system with a genuinely interesting distributed problem: maintaining a shared counter that is accurate enough, fast enough, and does not become a single point of failure.
> *(The algorithms themselves are covered in depth in `01_Fundamentals/07-rate-limiting.md`; this is the system around them.)*

---

## 1. Requirements

**Functional**
- Decide allow/deny for each incoming request, per client.
- Different limits per **tier** (free, premium) and per **endpoint**.
- Return `429` with `Retry-After` and `X-RateLimit-*` headers.
- Limits configurable at runtime, without a deploy.

**Non-functional**
- **Latency budget: under 5 ms added** to every request — this sits on the hot path of everything.
- Accuracy: small over-permissiveness is acceptable; large over-permissiveness is not.
- Availability: the limiter must not be able to take the whole API down.
- Scale: 1 million requests/second across 10 million distinct clients.

---

## 2. Estimation

```
THROUGHPUT   1M checks/second at peak
ACTIVE KEYS  10M clients, but perhaps 1M active in any window
STATE        token bucket = (tokens, last_refill) ≈ 50 B per key
             1M × 50 B = 50 MB    ← trivially fits in memory
NETWORK      1M round trips/second to a shared store
```

**The decisive observation:** the state is tiny (tens of megabytes) but the **request rate against it is enormous**. So the design problem is not storage — it is avoiding a network round trip per request while keeping the count shared.

---

## 3. Architecture

```
                 ┌──────────────────────────────────┐
   Clients ─────►│        API Gateway tier          │
                 │  ┌────────────────────────────┐  │
                 │  │ local token buckets (L1)   │  │  in-process, no network
                 │  └─────────────┬──────────────┘  │
                 └────────────────┼─────────────────┘
                                  │ periodic sync (every 100 ms)
                                  ▼
                       ┌──────────────────────┐
                       │  Redis cluster (L2)  │  sharded by client key
                       │  authoritative count │
                       └──────────┬───────────┘
                                  │
                       ┌──────────▼───────────┐
                       │  Config service      │  limits per tier/endpoint
                       │  (watched, cached)   │
                       └──────────────────────┘
```

Three tiers, and the layering is the answer:

**L1 — local, in-process.** Each gateway instance holds a token bucket per active client, with a share of the global allowance. Zero network cost; this handles the overwhelming majority of checks.

**L2 — Redis, authoritative.** Gateways sync their consumption every 100 ms, receive a corrected allowance, and adjust. Redis is sharded by client key so no single node is hot.

**Config service.** Limits live as data, watched for changes and cached locally, so a limit change propagates in seconds without a deploy.

---

## 4. The central design question: shared counter vs local allowance

**Option A — every check hits Redis.**
```lua
-- atomic: refill, check, decrement in one round trip
local tokens = tonumber(redis.call('HGET', KEYS[1], 'tokens') or ARGV[1])
local last   = tonumber(redis.call('HGET', KEYS[1], 'ts') or ARGV[2])
local refill = (tonumber(ARGV[2]) - last) * tonumber(ARGV[3])
tokens = math.min(tonumber(ARGV[1]), tokens + refill)
if tokens >= 1 then
  redis.call('HMSET', KEYS[1], 'tokens', tokens - 1, 'ts', ARGV[2])
  redis.call('EXPIRE', KEYS[1], 3600)
  return 1
end
redis.call('HMSET', KEYS[1], 'tokens', tokens, 'ts', ARGV[2])
return 0
```
Exact, and **the Lua script is essential** — without atomicity, two concurrent checks both read the same token count and both proceed. But it costs ~0.5 ms and one Redis operation **per request**, which at 1M requests/second means a Redis cluster sized for 1M ops/second purely for rate limiting.

**Option B — local allowance with periodic sync.** Each of N gateways gets `limit/N` locally and reconciles every 100 ms. One Redis round trip per gateway per interval instead of per request — a reduction of several orders of magnitude.

**The cost is bounded over-permissiveness.** In the worst case, between syncs, clients can exceed the limit by roughly the sum of unspent local allowances. With 100 ms sync intervals this is a small percentage, and for capacity protection that is entirely acceptable.

**The recommendation:** Option B for high-volume, capacity-protection limits; Option A for low-volume, high-stakes limits (login attempts, payment retries) where exactness matters and the volume does not.

**Saying this split aloud** — "different limits deserve different mechanisms" — is a stronger answer than picking one and defending it universally.

---

## 5. Key design and sharding

The limiter key is composite:
```
rl:{tier}:{client_id}:{endpoint_group}:{window}
```

Sharding by `client_id` gives uniform distribution and keeps all of one client's endpoints on one Redis shard, so a multi-key check is one round trip.

**Hot key risk:** a single very high-volume client. The local-allowance design already absorbs this, since that client's checks are served in-process on whichever gateways it reaches.

**Memory reclamation:** every key gets a TTL slightly longer than its window, so idle clients evict themselves and the working set stays proportional to *active* clients, not total clients.

---

## 6. Failure modes

| Failure | Behaviour | Decision |
|---|---|---|
| Redis unavailable | Gateways keep using local allowances | **Fail open** for ordinary APIs; the local buckets still bound the damage |
| Redis unavailable, security-critical endpoint | — | **Fail closed** for login and payment paths |
| Gateway instance dies | Its local allowance is lost | Harmless — it simply is not spent |
| Gateways scale up | Each holds `limit/N`, N changed | Re-derive N from the service registry on each sync |
| Config service down | Limits are stale | Cache the last good config locally; stale limits are far better than no limits |
| Clock skew between gateways | Refill rates drift slightly | Use monotonic clocks locally and let the Redis sync correct the drift |

**The fail-open/fail-closed split is the most important answer in this whole design**, because it is the one place where the limiter can cause an outage rather than prevent one. Volunteer it.

---

## 7. Response contract

```
HTTP/1.1 429 Too Many Requests
Retry-After: 30
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1758200400
```

Return `X-RateLimit-Remaining` on **every** response, not only on rejections, so well-behaved clients self-throttle before hitting the wall. That measurably reduces total rejected traffic, which is better for both sides.

**Never silently drop.** A client with no response retries immediately and amplifies the problem; a client with a `Retry-After` backs off.

---

## 8. Placement in the stack

```
CDN / edge        → volumetric floods, rejected before reaching you at all
API gateway       → per-client quotas          ← the main limiter lives here
Service           → expensive-endpoint limits, cost-based
Database          → connection pool caps, admission control
```

**Reject as early as possible.** A request rejected at the edge costs nothing; one rejected after authentication, deserialisation and two internal calls has already consumed most of its cost.

---

## 9. Extensions worth mentioning

**Cost-based limiting.** Not all requests are equal — charge tokens by expense (a simple read costs 1, a complex aggregation costs 50). This is how cloud provider and GraphQL rate limits work, and it is the correct answer to "how do you stop one expensive query consuming the quota".

**Adaptive concurrency limiting.** Instead of a fixed rate, bound in-flight requests and adjust the bound from observed latency (additive-increase/multiplicative-decrease, like TCP). This adapts automatically when the system is having a bad day, where a static limit does not.

**Quotas versus rate limits.** A rate limit is per second (protecting capacity); a quota is per month (enforcing billing). They are different mechanisms with different storage — quotas are durable and reconciled, rate limits are ephemeral.

---

## 10. Trade-offs to state

| Choice | Alternative | Why |
|---|---|---|
| Local allowance + periodic sync | Redis on every check | 1M round trips/s is the dominant cost; bounded over-permission is acceptable |
| Token bucket | Sliding window counter | Real API clients are bursty; token bucket matches that shape |
| Fail open by default | Fail closed | The limiter must not be able to cause the outage it exists to prevent |
| Limits as watched config | Limits in code | Changing a limit during an incident must not require a deploy |
| TTL on every key | Explicit cleanup | Idle clients evict themselves; the working set tracks active clients |

---

## 11. Practice

- [ ] Design from scratch in 45 minutes
- [ ] Write the Lua script for a sliding-window-counter instead of a token bucket
- [ ] Work out the worst-case over-permission for the local-allowance design
- [ ] Extend to cost-based limiting — what changes in the key and the state?
- [ ] Design the rollout: how do you change a limit safely for 10M clients?
