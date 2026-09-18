# Observability and Reliability

> A system you cannot observe is a system you cannot operate. This area is rarely the *main* topic of a design round, and it is very frequently the wrap-up question — "how would you know this is working?" — where a concrete answer separates candidates.

---

## 1. The three pillars

### Metrics
Numeric time series: request rate, error rate, latency percentiles, queue depth, CPU. Cheap to store, cheap to query, ideal for dashboards and alerts. **Low cardinality is essential** — a metric labelled by user id becomes millions of series and destroys the metrics backend.

**The four golden signals** (from Google's SRE practice), and the ones to name:

| Signal | What it measures | Why |
|---|---|---|
| **Latency** | how long requests take | the user's experience; split success from failure latency |
| **Traffic** | demand (requests/second) | context for everything else |
| **Errors** | failure rate | the most direct health indicator |
| **Saturation** | how full the system is (CPU, memory, queue depth) | the leading indicator of the next three |

**RED** (Rate, Errors, Duration) is the request-centric variant; **USE** (Utilisation, Saturation, Errors) is the resource-centric one. Either is a fine framework to cite.

### Logs
Discrete timestamped events with context. Expensive at volume; the right tool when you need to know *what happened to this particular request*.

**Structured logging is the point.** `{"level":"error","request_id":"abc","user_id":42,"latency_ms":230,"msg":"payment failed"}` is queryable; `"Payment failed for user 42"` is not. Structure everything, and always include the **request id**.

Sample aggressively at high volume — full logging of 100,000 requests/second is unaffordable and almost never read. Keep all errors, sample successes.

### Traces
The path of a single request across services, with timing per hop. A **trace id** is generated at the edge and propagated through every call, so the whole request can be reassembled.

```
trace: abc123
  [gateway        ────────────────────────────────── 240ms]
    [auth-service  ──── 15ms]
    [order-service     ─────────────────────── 200ms]
       [db query         ── 8ms]
       [inventory-svc      ──────────────── 170ms]  ← the problem
       [payment-svc          ── 12ms]
```

This is the replacement for the stack trace you gave up by distributing the system. Without it, "the API is slow" is unanswerable.

**How they complement each other:** metrics tell you *something is wrong* and when; traces tell you *where*; logs tell you *why*.

---

## 2. Percentiles, and why averages lie

**Never alert on an average.** An average latency of 50 ms is consistent with 95% of requests at 10 ms and 5% at 850 ms — and those 5% are real users having a bad time.

| Percentile | Meaning |
|---|---|
| p50 (median) | the typical experience |
| p95 | the slow tail most users occasionally hit |
| **p99** | the number most SLOs are written against |
| p999 | the extreme tail; often your largest customers, whose data is biggest |

**Tail latency amplification.** A page assembling 100 independent sub-requests, each with a 1% chance of exceeding p99, has a `1 − 0.99¹⁰⁰ ≈ 63%` chance of at least one slow call. **So the p99 of a component becomes the typical experience of a composed page.** This is the argument for caring about tails, and it is a genuinely strong point to make in a design round.

**Percentiles do not average.** You cannot take the mean of per-server p99s to get the global p99 — you need a mergeable structure like **t-digest** or HDR histograms.

---

## 3. SLI, SLO, SLA and error budgets

**SLI** — the measurement. "The fraction of requests served in under 200 ms."
**SLO** — the internal target. "99.9% of requests under 200 ms over a rolling 30 days."
**SLA** — the contractual promise, with financial consequences. Always set **looser** than the SLO, so you breach the internal target long before the contract.

**The error budget** is the inverse of the SLO: a 99.9% target permits 0.1% failures — about 43 minutes a month. That budget is a **resource to spend**: risky deploys, migrations and experiments consume it. When it is exhausted, the team stops shipping features and works on reliability.

This reframing is the useful part: it converts "how reliable should we be?" from an argument into an arithmetic question, and it makes the trade-off between velocity and stability explicit.

**Do not target 100%.** Each additional nine costs roughly an order of magnitude more, and beyond a point the user's own network is less reliable than your service, so the improvement is invisible.

---

## 4. Alerting

**Alert on symptoms, not causes.** "The error rate exceeds 1%" is actionable and user-relevant. "CPU is at 80%" may be completely fine — and a system can be entirely broken with normal CPU.

**Every alert must be actionable.** If the response is "acknowledge and ignore", delete it. Alert fatigue is a genuine failure mode: a team that receives fifty alerts a night stops reading them, and misses the one that mattered.

**Page vs ticket.** Page a human at 3 a.m. only for user-visible, urgent problems. Everything else is a ticket for working hours.

**Multi-window burn-rate alerting** is the modern practice: alert when the error budget is being consumed fast enough to exhaust it early, combining a short window (catches sudden spikes) and a long one (catches slow bleeds). It produces far fewer false pages than a simple threshold.

**Every alert should link to a runbook** — what this means, how to diagnose, what to try, whom to escalate to. Writing the runbook when you create the alert is the discipline; writing it during the incident is not.

---

## 5. Failure handling patterns

Most of these appear in `01`; collected here as the reliability toolkit.

| Pattern | Problem it solves |
|---|---|
| **Timeout** | a slow dependency consuming the caller's threads |
| **Retry with backoff + jitter** | transient failures; jitter prevents synchronised retry storms |
| **Circuit breaker** | stop calling a dependency that is failing; let it recover |
| **Bulkhead** | isolate resources per dependency so one cannot starve the others |
| **Fallback** | serve a cached or degraded response instead of an error |
| **Load shedding** | reject a fraction fast rather than failing everything slowly |
| **Rate limiting** | bound the load a client can impose |
| **Idempotency** | make retries safe |
| **Graceful degradation** | shed features to preserve the core |

**Cascading failure** is the pattern these exist to prevent: service D slows; C's threads block waiting; C becomes slow; B's threads block; the whole system is down because of one dependency. Timeouts, circuit breakers and bulkheads each cut the chain at a different point.

---

## 6. Redundancy and failure domains

**Eliminate single points of failure** — every tier needs at least two of everything, including the load balancer and the database.

**Failure domains:**
- **Instance** — one process dies. Handled by redundancy behind a health check.
- **Rack / host** — spread instances across hosts.
- **Availability zone** — an independent datacentre within a region. Multi-AZ is the standard baseline.
- **Region** — a whole geography. Multi-region is expensive and complex, and justified by disaster recovery or latency, not by fashion.

**Active-active vs active-passive.** Active-active serves from all regions (better utilisation and latency; needs multi-leader replication or careful partitioning by geography). Active-passive keeps a standby (simpler, cheaper, with failover risk and idle capacity).

**RPO and RTO** are the two numbers to quote. **Recovery Point Objective** — how much data may be lost (the age of the last usable backup). **Recovery Time Objective** — how long recovery may take. Stating them turns a vague "we'd have backups" into an engineering requirement.

**Backups must be tested.** An untested backup is a hypothesis, not a backup. Restore drills are the only way to know.

---

## 7. Deployment safety

| Strategy | Mechanism | Rollback |
|---|---|---|
| **Rolling** | replace instances gradually | redeploy the old version |
| **Blue-green** | two environments, switch traffic | **instant** — switch back |
| **Canary** | small traffic percentage first, watch metrics | stop the rollout |
| **Feature flag** | ship dark, enable separately | flip the flag — no deploy |

**Canary gives the best risk control** because you see real errors on a small slice before full exposure. **Blue-green gives the fastest rollback.** **Feature flags decouple deploy from release**, which is what makes trunk-based development workable — at the cost of flag cleanup, which teams routinely forget.

**Always have a rollback plan, and decide the rollback triggers in advance.** "Error rate above 2% for five minutes, or p99 above 500 ms" decided beforehand beats a judgement call under pressure at 2 a.m.

---

## 8. Incident response

1. **Detect** — an alert, or a customer report (the latter means detection failed).
2. **Triage** — assess severity and user impact.
3. **Mitigate first, diagnose second.** Roll back, fail over, shed load. Understanding the root cause can wait; stopping the bleeding cannot.
4. **Communicate** — a status page and internal updates on a fixed cadence, even when the update is "still investigating".
5. **Resolve.**
6. **Postmortem** — blameless, within a few days, with concrete action items and owners.

**Blameless is not a nicety, it is a mechanism.** People who fear blame hide information, and the postmortem then fails at its only job. The premise is that a system which allows one person's mistake to cause an outage has a systems problem, not a person problem.

**Good postmortem structure:** what happened, a timeline, user impact, root cause (with "five whys"), what went well, what went badly, and action items with owners and dates.

---

## 9. What to say in the wrap-up

When asked "how would you monitor this?", a concrete answer beats a list:

> "Four golden signals per service: request rate, error rate, p50/p99 latency, and saturation — for the queue-backed parts that's consumer lag, which is the earliest warning we'd get.
>
> SLO: 99.9% of reads under 200 ms over 30 days, with burn-rate alerting on the error budget rather than a raw threshold.
>
> I'd page on user-visible symptoms — error rate above 1%, p99 above 500 ms, consumer lag growing for 10 minutes — and ticket everything else.
>
> Distributed tracing with a trace id from the gateway, because otherwise a slow request across these six services is undebuggable.
>
> The thing that would worry me most is the cache: if the hit ratio drops the database sees 10× its designed load, so I'd alert on hit ratio directly rather than waiting for the latency symptom."

That last sentence is the one that lands: it shows you know which failure this specific design is vulnerable to.

---

## 10. Recall questions

1. Name the three pillars and what each is good and bad at.
2. Give the four golden signals and say which is the leading indicator.
3. Why is high metric cardinality a problem?
4. Why is structured logging the point, and what field must always be present?
5. What does a trace give you that metrics and logs cannot?
6. Why should you never alert on an average?
7. Explain tail latency amplification with the 100-subrequest calculation.
8. Why can't you average per-server p99s, and what structure solves it?
9. Distinguish SLI, SLO and SLA, and say why the SLA is looser.
10. What is an error budget and how does it change the reliability conversation?
11. Why not target 100% availability?
12. Why alert on symptoms rather than causes?
13. What is alert fatigue and what is the rule that prevents it?
14. Describe cascading failure and name three patterns that cut the chain.
15. Define RPO and RTO.
16. Compare rolling, blue-green, canary and feature flags on risk and rollback.
17. Why mitigate before diagnosing?
18. Why is a blameless postmortem a mechanism rather than a courtesy?
