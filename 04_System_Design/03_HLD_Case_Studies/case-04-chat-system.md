# HLD — Chat System (WhatsApp / Messenger)

> The case study where **connection management** is a first-class problem. Most systems are request/response; this one is long-lived bidirectional connections, which changes the shape of everything.

---

## 1. Requirements

**Functional**
- One-to-one messaging, and group chats up to ~500 members.
- Online/offline presence.
- Delivery receipts: sent, delivered, read.
- Message history, retrievable on a new device.
- Push notification when the recipient is offline.

**Non-functional**
- 50M DAU, ~40 messages sent per user per day.
- **Message delivery latency under 500 ms** for online users.
- Messages must not be lost, and must arrive **in order within a conversation**.
- At-least-once delivery with deduplication (exactly-once is not achievable).

**Out of scope:** voice and video calls, end-to-end encryption key exchange (mentioned, not designed).

---

## 2. Estimation

```
MESSAGES     50M × 40 = 2B/day ÷ 10^5 s   = 20,000/s     peak ×3 = 60,000/s

CONNECTIONS  ~10% concurrently online     = 5M concurrent WebSockets
             ~10 KB state per connection  = 50 GB of connection state
             50,000 connections/server    = 100 connection servers

STORAGE      message ≈ 200 B (id, sender, chat, body, ts, status)
             2B × 200 B = 400 GB/day  → 146 TB/year
```

**What the numbers decide:** 5 million concurrent connections cannot live on a few servers, so there must be a **connection tier** with its own scaling, plus a way to find **which server currently holds a given user's connection**. That discovery problem is the architectural heart of this system.

---

## 3. Why WebSocket, and the alternatives

| Approach | Mechanism | Verdict |
|---|---|---|
| Polling | client asks every N seconds | Simple; high latency and enormous wasted load |
| Long polling | server holds the request until data or timeout | Workable fallback; one connection per pending message |
| Server-sent events | server → client stream over HTTP | One-directional only |
| **WebSocket** | full-duplex over one upgraded TCP connection | **The right choice** — bidirectional, low overhead per message |

**WebSocket costs:** a stateful connection (so the servers holding them are not stateless), memory per connection, and load balancers that must support long-lived upgraded connections rather than balancing per request.

**Keep the connection tier thin.** It holds connections and routes frames; it should contain as little logic as possible, because it is the tier you cannot redeploy casually — every restart drops millions of connections and triggers a reconnect storm.

---

## 4. Architecture

```
                     ┌──────────────────────┐
   Clients ─────────►│  L4 Load Balancer    │  (sticky by connection)
   (WebSocket)       └──────────┬───────────┘
                                ▼
                 ┌──────────────────────────────┐
                 │   Connection servers (×100)  │  5M WebSockets total
                 │   thin: hold + route frames  │
                 └───┬──────────────────────┬───┘
                     │ registers            │ delivers
                     ▼                      │
          ┌────────────────────┐            │
          │  Presence / Session│            │  user_id -> server_id
          │  store (Redis)     │────────────┘
          └────────────────────┘
                     ▲
                     │ lookup
          ┌──────────┴─────────┐     ┌──────────────────┐
          │   Chat Service     │────►│  Kafka (messages)│
          └──────────┬─────────┘     └────────┬─────────┘
                     ▼                        ▼
          ┌────────────────────┐    ┌───────────────────────┐
          │ Message store      │    │ Push notification svc │
          │ (Cassandra)        │    │ (APNs / FCM)          │
          └────────────────────┘    └───────────────────────┘
```

**Message flow, one-to-one:**
1. Sender's client sends the message over its WebSocket to connection server A.
2. A forwards it to the Chat Service, which assigns a **server-side message id and sequence number** and persists it. Persist before acknowledging — this is what makes "not lost" true.
3. Chat Service acks the sender (status: **sent**).
4. Chat Service looks up the recipient in the session store: which connection server holds them?
5. If online: forward to connection server B, which pushes over the recipient's WebSocket. B acks → status **delivered**.
6. If offline: enqueue for push notification, and leave the message for retrieval on next connect.
7. When the recipient's client displays it, it sends a read receipt back along the same path.

---

## 5. Deep dives

### Finding the recipient's connection server
A Redis map `user_id → {server_id, connected_at}`, written when a connection is established and removed (with a TTL as a safety net) when it drops. The Chat Service reads it to route.

**Failure mode:** a connection server crashes without cleaning up, leaving stale entries pointing at a dead server. The TTL plus connection-server heartbeats bound the staleness; a routed message that fails to deliver falls back to the offline path, so a stale entry costs a retry rather than a lost message.

**Alternative:** a pub/sub channel per user, with the connection server subscribing on connect. The broker handles routing and there is no session map to keep consistent — at the cost of 5 million subscriptions.

### Ordering
Messages must be ordered **within a conversation**, not globally. So:
- The Chat Service assigns a **monotonic per-conversation sequence number** (from a counter, or by partitioning conversations so one writer owns each).
- Kafka topics are partitioned by `conversation_id`, so all messages for a conversation are ordered in one partition.
- Clients sort by sequence number and can detect gaps, requesting a resync if one appears.

**Client timestamps are not usable for ordering** — device clocks are wrong and adversarial. Server-assigned sequence numbers are the only reliable order.

### Delivery guarantees and deduplication
At-least-once, with a **client-generated message id** carried end to end. Retries reuse the id; the server's insert is conditional on the id not existing, so a duplicate send is idempotent. Recipients likewise deduplicate by id.

**This is the standard "at-least-once plus idempotency = observationally exactly-once" pattern.**

### Group chat
For a 500-member group, fan-out on write into each member's inbox is 500 writes per message — acceptable at this size, and it makes reads trivial.

**For much larger groups** the celebrity problem returns, and you switch to fan-out on read: store the message once per conversation and have members pull. The threshold is the same reasoning as the news feed.

### Message storage and retrieval
```
messages  PRIMARY KEY ((conversation_id), seq DESC)
```
Partitioned by conversation, clustered by sequence descending. "Fetch the last 50 messages of this conversation" is then a **single sequential read within one partition** — the canonical wide-column access pattern.

Old messages tier to cheaper storage; recent messages stay hot in the cache.

### Presence
Presence is high-volume and low-value: a naive design broadcasts every status change to every contact, which at 5 million users churning is enormous.

Practical approach: heartbeat every ~30 seconds, store `last_seen` in Redis with a TTL, and **only compute presence for contacts the user is actually looking at**. Do not broadcast; let clients poll the small set on screen. This is a good example of scoping a requirement down for a large saving.

### Offline delivery
Undelivered messages remain in the store with a per-user "last delivered sequence" pointer. On reconnect, the client sends its last-seen sequence per conversation and the server streams the delta. This also handles multi-device: each device has its own pointer.

---

## 6. Bottlenecks and failure modes

| Risk | Impact | Mitigation |
|---|---|---|
| Connection server restart | ~50,000 clients reconnect at once | Staggered deploys, client reconnect with **exponential backoff and jitter** |
| Mass reconnect storm | Thundering herd on the session store | Jittered backoff is essential, not optional, here |
| Stale session entries | Messages routed to a dead server | TTL + heartbeats; fall back to the offline path |
| Hot conversation partition | One very active group saturates a partition | Rare at 500 members; for larger, switch to pull |
| Push provider outage | Offline users get no notification | Queue and retry; messages are still stored and delivered on reconnect |
| Message store growth | 146 TB/year | Partition by conversation; tier cold data to object storage |

---

## 7. Trade-offs to state

| Choice | Alternative | Why |
|---|---|---|
| WebSocket | Long polling | Bidirectional, low per-message overhead — worth the stateful tier |
| Thin connection tier | Logic in the connection servers | Restarts drop millions of connections; keep them boring |
| Server-assigned sequence numbers | Client timestamps | Device clocks are unreliable and adversarial |
| Fan-out on write for groups ≤ 500 | Pull | 500 writes is cheap; it makes reads trivial |
| At-least-once + client message id | Attempting exactly-once | Exactly-once is not achievable; idempotency is |
| Presence on demand | Broadcast presence | Broadcasting is enormous volume for very little value |
| Cassandra partitioned by conversation | Relational | Append-heavy, one dominant access pattern, needs linear write scaling |

---

## 8. Practice

- [ ] Design from scratch in 45 minutes
- [ ] Work out the connection server count from your own memory-per-connection assumption
- [ ] Design multi-device sync — what changes in the delivery pointers?
- [ ] Handle a group of 100,000 members
- [ ] Sketch how end-to-end encryption changes the server's role (hint: it can no longer read bodies, so search and server-side fan-out of content change)
