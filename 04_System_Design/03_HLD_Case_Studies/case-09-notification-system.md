# HLD — Notification System

> A system that looks simple and is not. It is the clearest case study for **fan-out to heterogeneous channels, third-party dependency management, and delivery guarantees** — and it is a component of almost every other system, so it is worth knowing well.

---

## 1. Requirements

**Functional**
- Send notifications over **push** (iOS/Android), **SMS**, **email** and **in-app**.
- Triggered by other services ("order shipped", "someone mentioned you").
- Respect user preferences: which channels, which categories, quiet hours.
- Support templates with variables and localisation.
- Support scheduled and recurring notifications.
- Track delivery status and let senders query it.

**Non-functional**
- 100M users, ~10 notifications per user per day.
- **Critical notifications** (OTP, security alert) must be fast — under 5 seconds — and near-certain to arrive.
- Marketing notifications may be delayed and even dropped under pressure.
- Must not be able to spam a user.
- Third-party providers fail regularly; the system must survive that.

---

## 2. Estimation

```
VOLUME        100M × 10 = 1B/day ÷ 10^5 s  = 10,000/s     peak ×5 = 50,000/s
              (peak is spiky: a campaign blast is far above steady state)

BY CHANNEL    push  70%  = 7,000/s
              email 20%  = 2,000/s
              SMS    5%  =   500/s      ← most expensive per message
              in-app 5%  =   500/s

STORAGE       delivery records 1B/day × 200 B = 200 GB/day → tier after 30 days
DEVICE TOKENS 100M users × 2 devices × 100 B  = 20 GB
```

**What the numbers decide:** the spikiness. A marketing campaign to 50 million users arrives as a single burst, not a steady 10,000/second. **The queue is not an optimisation here, it is the mechanism that makes the burst survivable** — and it is why priority separation matters, because an OTP must not queue behind a campaign.

---

## 3. Architecture

```
  Producers (order svc, social svc, marketing) 
        │  POST /v1/notifications
        ▼
  ┌──────────────────────┐
  │ Notification API     │  validate, deduplicate, resolve user
  └──────────┬───────────┘
             ▼
  ┌──────────────────────┐      ┌────────────────────┐
  │ Preference Service   │◄────►│ User prefs store   │
  │ (filter + quiet hrs) │      └────────────────────┘
  └──────────┬───────────┘
             ▼
  ┌──────────────────────────────────────────────────┐
  │  Kafka   topic per PRIORITY, partitioned by user │
  │  ┌───────────┐ ┌───────────┐ ┌────────────────┐  │
  │  │ critical  │ │ standard  │ │   marketing    │  │
  │  └───────────┘ └───────────┘ └────────────────┘  │
  └──────┬──────────────┬──────────────┬─────────────┘
         ▼              ▼              ▼
  ┌────────────┐ ┌────────────┐ ┌────────────┐ ┌────────────┐
  │ Push worker│ │ SMS worker │ │Email worker│ │In-app work.│
  └──────┬─────┘ └──────┬─────┘ └──────┬─────┘ └──────┬─────┘
         ▼              ▼              ▼              ▼
   APNs / FCM      Twilio etc.     SES etc.     WebSocket / DB
         │              │              │
         └──────────────┴──────────────┴──► delivery callbacks
                                              ▼
                                   ┌────────────────────┐
                                   │ Delivery tracking  │
                                   └────────────────────┘
```

**The three design decisions visible in this diagram:**

1. **Separate topics per priority.** A campaign blast fills the marketing topic; the critical topic is untouched, so OTPs still arrive in seconds. Sharing one topic would make every OTP queue behind 50 million marketing messages.
2. **A worker per channel.** Each channel has a different provider, different rate limits, different failure modes and different retry policy. One worker type per channel keeps those concerns separate and lets each scale independently.
3. **Preference filtering happens before the queue.** There is no point enqueueing a message the user has opted out of.

---

## 4. The channel abstraction

Every channel does the same thing differently, which is a Strategy:

```python
class NotificationChannel(ABC):
    @abstractmethod
    def send(self, notification: Notification) -> DeliveryResult: ...

    @abstractmethod
    def rate_limit(self) -> Rule: ...

class PushChannel(NotificationChannel): ...      # APNs / FCM, device tokens
class SmsChannel(NotificationChannel): ...       # Twilio, costs money per message
class EmailChannel(NotificationChannel): ...     # SES, bounce handling
class InAppChannel(NotificationChannel): ...     # WebSocket or a database row
```

Adding WhatsApp or Slack is a new class and a new worker deployment. Nothing existing changes — the extensibility point worth naming.

**Channel-specific realities to mention:**
- **Push** — tokens expire and devices are uninstalled; the provider returns "invalid token", and you must **delete it** or you accumulate dead tokens forever.
- **SMS** — costs real money per message, has per-country regulations and sender-id rules, and is the channel to rate limit hardest.
- **Email** — bounces and spam complaints damage sender reputation, so hard bounces must suppress the address permanently.
- **In-app** — the only channel you fully control; delivery is a database write plus an optional WebSocket push.

---

## 5. Deep dives

### Deduplication and idempotency
Producers retry. Without protection, a retried "order shipped" notifies the user twice.

Require an **idempotency key** from the producer (`{event_id}:{user_id}:{channel}`), store it with a TTL, and reject duplicates. This is the same mechanism as the payment idempotency key, and it is non-negotiable in a system where every producer is a distributed service that retries.

### Rate limiting the user, not just the API
A user must not receive 200 notifications because a batch job misbehaved. Enforce **per-user caps per category per window** (e.g. at most 5 social notifications an hour), applied in the preference service before enqueueing.

**Also collapse related notifications:** "3 people liked your post" rather than three separate pushes. This is a product requirement that shapes the architecture — it needs a short aggregation window per user per category, which means a small stateful step before dispatch.

### Quiet hours and time zones
"Do not notify between 22:00 and 08:00 **local time**." That means storing the user's time zone and, for non-critical notifications, either dropping or **deferring to the next allowed window**. Deferral needs a scheduled store keyed by send time — which the scheduling requirement needs anyway.

Critical notifications ignore quiet hours. That exception must be explicit in the priority model, not a special case in code.

### Retries and provider failure
Each channel retries with **exponential backoff and jitter** — and jitter genuinely matters here, because a provider outage means thousands of workers all retrying at once.

Wrap each provider in a **circuit breaker**: after repeated failures, stop calling it, fail fast, and let queued messages accumulate rather than burning workers on a dead endpoint. When the breaker half-opens, one trial request tests recovery.

**Fall back across channels for critical notifications only:** if push fails for an OTP, send SMS. For marketing, failure is simply failure.

After N attempts, messages go to a **dead letter queue** for inspection. Without one, a permanently-failing message blocks its partition.

### Templates
Store templates with placeholders, versioned, with per-locale variants. Render at send time. **Keep template changes out of deploys** — marketing teams change copy constantly and should not need an engineer.

### Delivery tracking
Providers deliver asynchronously and report back via webhook: sent → delivered → opened → clicked (or bounced/failed). Record the state transitions per notification.

At 1B/day this is 200 GB/day of tracking data; keep 30 days hot for support queries and tier the rest to analytics storage.

---

## 6. Bottlenecks and failure modes

| Risk | Impact | Mitigation |
|---|---|---|
| Campaign blast | Critical notifications delayed | **Separate priority topics** — the single most important mitigation |
| Provider outage (APNs down) | That channel stops | Circuit breaker; queue and drain on recovery; cross-channel fallback for critical only |
| Provider rate limit exceeded | Throttled or blocked | Per-provider token bucket in the worker; queue paces the calls |
| Duplicate sends | User annoyance, or double OTP | Idempotency keys |
| Stale device tokens | Wasted calls, growing table | Delete on the provider's invalid-token response |
| Consumer lag | Notifications arrive late | Autoscale workers on lag; alert on the critical topic specifically |
| Email reputation damage | All email lands in spam | Suppress hard bounces permanently; monitor complaint rate |

**What to alert on:** consumer lag on the **critical** topic (not the aggregate), per-provider error rate, and per-channel delivery success rate. Aggregate metrics hide exactly the failure that matters.

---

## 7. Trade-offs to state

| Choice | Alternative | Why |
|---|---|---|
| Separate topic per priority | One topic with priority field | Most brokers have no true priority; separate topics are the practical mechanism |
| Worker per channel | One worker handling all channels | Different providers, rate limits, retry policies and failure modes |
| Preference filtering before the queue | Filtering in the worker | Do not enqueue what will be discarded |
| At-least-once + idempotency key | Attempting exactly-once | Not achievable; idempotency is |
| Cross-channel fallback for critical only | Fallback everywhere | SMS costs money; falling back on marketing would be expensive and unwelcome |
| Defer non-critical during quiet hours | Drop them | Deferral preserves the message; dropping loses it |
| Tracking hot for 30 days | Keep forever | 200 GB/day; support queries are almost always recent |

---

## 8. Practice

- [ ] Design from scratch in 45 minutes
- [ ] Design the collapse/aggregation window ("3 people liked your post")
- [ ] Add a new channel (WhatsApp) and verify nothing existing changes
- [ ] Design the campaign blast path: 50M messages without starving OTPs
- [ ] Design the device-token lifecycle, including cleanup
