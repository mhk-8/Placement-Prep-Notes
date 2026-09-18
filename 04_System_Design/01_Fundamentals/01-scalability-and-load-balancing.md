# Scalability and Load Balancing

---

## 1. The two axes of scaling

### Vertical scaling (scale up)
Put the workload on a bigger machine: more cores, more RAM, faster disks.

**Advantages.** No application changes. No distributed-systems problems — no consistency issues, no partition tolerance, no network partitions between your own components. Debugging stays tractable.

**Limits.** There is a largest available machine, and price scales super-linearly near the top. More seriously, **a single machine is a single point of failure**: no amount of vertical scaling gives you availability.

**When it is right:** early-stage systems, workloads with genuinely shared state (a single-writer database), and anything where the engineering cost of distribution exceeds the hardware cost. A modern server with 512 GB of RAM handles far more than most designs assume, and saying so is a signal of judgement rather than naivety.

### Horizontal scaling (scale out)
Add more machines and spread the work.

**Advantages.** Effectively unbounded capacity. Redundancy comes for free — losing one of fifty instances is a rounding error. Commodity hardware is cheaper per unit of work.

**Costs.** You now have a distributed system: load balancing, service discovery, data partitioning, consistency choices, partial failures, and a much larger operational surface.

**The precondition is statelessness.**

---

## 2. Statelessness — the enabling property

A **stateless** service holds no request-specific state between requests. Any instance can serve any request; instances are interchangeable; scaling is just changing a number.

State has to live somewhere, so it moves:

| State | Where it goes |
|---|---|
| Session / login | A shared cache (Redis) or a signed token (JWT) carried by the client |
| Uploaded files | Object storage (S3), never local disk |
| In-progress work | A queue or a database, so another instance can pick it up |
| Cached computations | A shared cache, not process memory |

**Sticky sessions** (routing a user always to the same instance) are the alternative, and they are a trap: that instance becomes a single point of failure for those users, deploys become disruptive, and load balancing becomes uneven. Use them only when there is genuinely no alternative, such as long-lived WebSocket connections — and even then, hold the *authoritative* state elsewhere.

**Interview phrasing:** "I'd keep the application tier stateless so I can scale it by adding instances and lose one without impact. Session state goes in Redis."

---

## 3. Load balancers

A load balancer distributes incoming requests across a pool of backends. It also provides health checking, TLS termination, and a stable address that hides the backend topology.

### Layer 4 vs Layer 7

| | L4 (transport) | L7 (application) |
|---|---|---|
| Operates on | IP address and TCP/UDP port | HTTP: path, headers, cookies, method |
| Can inspect content | no | yes |
| Can terminate TLS | no (passes through) | yes |
| Routing decisions | connection-level | request-level |
| Throughput | higher, lower latency | lower, more CPU |
| Typical use | raw TCP services, extreme throughput | web APIs, microservice routing |

**L7 is the default for HTTP services**, because path-based routing (`/api/users` → user service), header-based routing (canary by a header), and TLS termination at the edge are all things you want. L4 is for when you need raw throughput or are balancing a non-HTTP protocol.

### Algorithms

| Algorithm | How it works | When it fits |
|---|---|---|
| **Round robin** | rotate through the pool | uniform requests, uniform backends |
| **Weighted round robin** | proportional to capacity | heterogeneous machines |
| **Least connections** | send to the backend with fewest open connections | long-lived or variable-duration requests |
| **Least response time** | fewest connections *and* lowest latency | latency-sensitive services |
| **IP hash** | hash the client IP to a backend | crude stickiness without shared state |
| **Consistent hashing** | hash a request key onto a ring | cache-friendly routing (see `08`) |

**Least connections is usually the better default than round robin** for anything where request durations vary, because round robin can pile short requests onto a backend already handling a slow one.

### Health checks

- **Passive:** observe real traffic and eject a backend after repeated failures. Free, but slow to detect.
- **Active:** poll a `/health` endpoint on a fixed interval. Faster detection, small constant load.

**Design the health endpoint carefully.** If it checks the database and the database is briefly slow, every backend reports unhealthy simultaneously and the load balancer removes the entire pool — turning a degradation into an outage. A common resolution is two endpoints: a **liveness** check that only proves the process is alive, and a **readiness** check that reflects dependencies and controls traffic admission.

### Redundancy of the balancer itself
A single load balancer is a single point of failure. The standard arrangement is an active–passive pair sharing a floating IP, or several balancers behind DNS round robin, or a cloud provider's managed balancer which is internally redundant.

---

## 4. DNS and global distribution

DNS is the first routing layer. Beyond name resolution it provides:

- **Round-robin DNS** — multiple A records for one name; crude balancing with no health awareness and poor failover (clients cache).
- **GeoDNS** — return different addresses by the client's location, so users reach the nearest region.
- **Anycast** — the same IP announced from many locations; BGP routes each client to the topologically nearest one. What CDNs and DNS providers themselves use.

**DNS TTL is the failover knob.** A low TTL (30–60 s) allows fast failover but increases lookup volume; a high TTL caches well but means clients keep hitting a dead address after a failover. DNS is a poor *primary* failover mechanism for this reason — prefer a load balancer or anycast for fast failover, and use DNS for coarse geographic routing.

---

## 5. Application-tier scaling patterns

### Autoscaling
Add or remove instances based on a metric — CPU, request rate, or queue depth.

Two traps worth naming:
- **Scale-up lag.** Booting an instance takes 30 s to several minutes; a traffic spike arriving faster than that is not absorbed. Keep headroom, or pre-warm for predictable events.
- **Flapping.** Aggressive thresholds cause repeated scale-up/scale-down cycles. Use a cooldown period and asymmetric thresholds (scale up quickly, scale down slowly).

**Queue depth is often the better autoscaling signal than CPU** for worker pools, because it measures the backlog directly rather than a proxy for it.

### Connection pooling
Opening a database connection costs a TCP handshake plus authentication — milliseconds, and each connection consumes memory on the database. With 100 application instances each holding 50 connections, the database sees 5,000 connections and spends its time context-switching.

The fix is a **connection pooler** (PgBouncer, ProxySQL) multiplexing many application connections onto few database connections. This is a genuinely common production problem and a good detail to volunteer.

### Request coalescing
When many identical requests arrive concurrently for something expensive, let the first one compute and have the rest wait on its result. Prevents a cache miss on a hot key from becoming N identical database queries.

---

## 6. Failure handling

### Timeouts
**Every network call needs a timeout.** Without one, a slow dependency consumes the caller's threads until the caller itself becomes unresponsive — the classic cascading failure.

Set timeouts from the **latency budget**, not arbitrarily: if the user-facing p99 target is 200 ms and three internal calls are involved, no single call gets a 5-second timeout.

### Retries
Retries turn transient failures into successes — and turn an overloaded service into a dead one, if done naively.

Rules:
1. **Only retry idempotent operations**, or make the operation idempotent with a request key.
2. **Exponential backoff:** 100 ms, 200 ms, 400 ms, 800 ms.
3. **Add jitter.** Without randomisation, every client retries at the same instant and creates a synchronised wave. This is not a minor detail — it is the difference between recovery and a self-sustaining outage.
4. **Cap the attempts** — typically three.
5. **Do not retry at every layer.** Three layers each retrying three times is 27 requests.

### Circuit breaker
Track failures to a dependency. Past a threshold, **open** the circuit and fail fast without calling it. After a cooldown, allow a trial request (**half-open**); on success, **close** again.

```
   CLOSED ──failures exceed threshold──► OPEN
      ▲                                    │
      │                                after cooldown
   success                                 ▼
      └──────────────────────────────  HALF-OPEN
                                    (one trial request)
```

The purpose is twofold: the caller stops wasting threads on a doomed call, and the struggling dependency gets breathing room to recover instead of being hammered.

### Bulkheads
Isolate resources per dependency — separate thread pools or connection pools — so that saturating one cannot starve the others. Named after ship compartments: a breach floods one section, not the hull.

### Graceful degradation
Decide in advance what to drop under load. A feed service that cannot reach the recommendation engine should serve a chronological feed, not a 500. Explicitly listing the degraded modes is a strong thing to volunteer in a design round.

### Load shedding
Under extreme load, reject a fraction of requests immediately rather than accepting everything and serving all of it slowly. Rejecting 10% fast is better than failing 100% slowly — and prioritising which 10% (drop background refreshes, keep checkout) is where the design judgement lies.

---

## 7. Putting it together

A typical request path at moderate scale:

```
Client
  │  DNS (GeoDNS → nearest region)
  ▼
Anycast IP / cloud load balancer  ── TLS termination
  │  L7 routing by path
  ▼
API Gateway  ── authn, rate limiting, request validation
  │
  ▼
Stateless application instances (autoscaled, behind health checks)
  │  timeouts + retries with jitter + circuit breakers
  ├──► Cache (Redis)
  ├──► Database (primary + read replicas, via a connection pooler)
  └──► Queue ──► async workers
```

---

## 8. Recall questions

1. What is the precondition for horizontal scaling, and where does the displaced state go?
2. Why are sticky sessions usually a bad idea, and when are they unavoidable?
3. Give three things an L7 balancer can do that an L4 cannot.
4. When does least-connections beat round robin?
5. How can a health check turn a degradation into an outage, and what is the fix?
6. Why is DNS a poor primary failover mechanism?
7. Name the two classic autoscaling failure modes.
8. Why does a connection pooler exist?
9. Why is jitter essential in a retry policy?
10. Describe the three circuit-breaker states and the purpose of each transition.
11. What is the difference between a bulkhead and a circuit breaker?
12. Why is shedding 10% of load better than degrading 100% of it?
