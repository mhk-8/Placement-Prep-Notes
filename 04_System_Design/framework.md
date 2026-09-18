# The System Design Interview Framework

> A 45-minute round, minute by minute. This structure is itself part of what is graded — a candidate who drives the conversation in this order reads as experienced regardless of the specific answers.

---

## The shape of the round

| Minutes | Stage | What you produce |
|---|---|---|
| 0–5 | **Requirements** | A written list of functional and non-functional requirements, and an explicit scope |
| 5–10 | **Estimation** | QPS, storage, bandwidth — enough to justify later choices |
| 10–15 | **API and data model** | The interface and the schema |
| 15–25 | **High-level design** | Boxes and arrows: the request path end to end |
| 25–40 | **Deep dive** | Two or three components examined properly |
| 40–45 | **Bottlenecks and wrap-up** | What breaks first, and what you would do next |

The timings are a guide, not a contract. The interviewer will steer; your job is to have a default structure so you are never stalled.

---

## Stage 1 — Requirements (0–5 min)

**Never start designing here.** The single most common failure is drawing boxes in minute two.

### Functional requirements
What must the system *do*? Write 4–6 bullets, then explicitly say which are in scope.

> "So the core is: a user can shorten a URL, and anyone can visit the short link and be redirected. Should I also cover custom aliases, expiry, and analytics? I'll treat analytics as out of scope unless you'd like it."

**Deliberately scoping things out is a positive signal**, not a dodge. It shows you know a real system is larger than 45 minutes.

### Non-functional requirements
These drive every architectural decision, so make them explicit and numeric:

| Dimension | Question to ask |
|---|---|
| **Scale** | How many users? Daily active? Requests per second? |
| **Read/write ratio** | Is this read-heavy (feeds, URL shorteners: 100:1) or write-heavy (logging, metrics)? |
| **Latency** | What is acceptable — 10 ms, 200 ms, 2 s? p50 or p99? |
| **Availability** | Is downtime acceptable? 99.9% is 8.7 hours a year; 99.99% is 52 minutes |
| **Consistency** | Must a read see the latest write, or is a few seconds of staleness fine? |
| **Durability** | Can we ever lose data? (Payments: no. View counts: yes.) |

**The consistency question is the most valuable one you can ask**, because the answer determines your entire storage strategy. "If two users book the last seat simultaneously, what should happen?" is worth more than five minutes of diagramming.

### Say the scope aloud before moving on
> "So: read-heavy, 100 million DAU, p99 under 200 ms for reads, eventual consistency acceptable for the feed but strong for the write path. In scope: posting and reading. Out of scope: search, ads, moderation. Does that match what you had in mind?"

---

## Stage 2 — Estimation (5–10 min)

You are not being tested on arithmetic. You are being tested on whether your design is **proportionate to its load** — a system for 1,000 users and one for 100 million are different systems, and choosing the wrong one is the failure this stage prevents.

### The four numbers to compute

```
1. QPS        = DAU × actions per user per day / 86,400
   Peak QPS   = 2–3 × average   (some systems spike 10×)

2. Storage    = writes per day × bytes per write × retention (× replication factor)

3. Bandwidth  = QPS × bytes per request

4. Memory     = working set for the cache
                (often the 20% of data serving 80% of requests)
```

### Make the arithmetic easy
- 86,400 seconds/day ≈ **10⁵**
- 1 million writes/day ≈ **12 writes/second**
- 1 billion writes/day ≈ **12,000 writes/second**
- 1 KB × 1 million = 1 GB; 1 KB × 1 billion = 1 TB

**Round aggressively and say you are rounding.** "Call it 10⁵ seconds in a day — that's within 20%, and we only need the order of magnitude." Precision here is a waste of the clock.

### Connect each number to a decision
An estimate you do not use is theatre. Say what it implies:

> "1.2 TB a year means a single machine holds this comfortably for a couple of years — so we do not need sharding on day one, and I will design for it but not build it."

> "Peak 50,000 reads per second against 100 writes per second means this is overwhelmingly read-heavy, so a cache in front of the database is the highest-leverage component."

---

## Stage 3 — API and data model (10–15 min)

### API
Three to five endpoints, with the signature and the important parameters:

```
POST /v1/urls              { longUrl, customAlias?, expiryAt? } -> { shortUrl }
GET  /{shortCode}          -> 302 redirect
GET  /v1/urls/{code}/stats -> { clicks, createdAt }
```

Mention **authentication** (an API key or token on writes), **pagination** (cursor-based, not offset — say why: offset pagination drifts when rows are inserted), **idempotency** for anything that creates or charges, and **rate limits**.

### Data model
State the entities, their key fields, and — most importantly — **the access patterns**.

> "Reads are always by short code, so that is the primary key. There is no query that scans by long URL except deduplication, which I'll handle with a hash index."

**Then justify the storage choice from the access pattern**, not the other way round. "Key-value lookups by a single key, no joins, high read volume" *implies* a key-value store; do not announce the technology first and rationalise afterwards.

---

## Stage 4 — High-level design (15–25 min)

Draw the request path. A workable default skeleton:

```
Client → DNS → CDN (static) → Load Balancer → API Gateway
      → Application servers (stateless)
      → Cache  →  Database (primary + replicas)
      → Message queue → Async workers
      → Object storage (blobs)
```

**Walk one request through it out loud, end to end.** That narration is what the interviewer is grading, far more than the neatness of the boxes.

Two rules:
- **Keep application servers stateless.** Session state goes in a shared cache, so any server can serve any request and scaling is just adding instances.
- **Do slow work asynchronously.** Anything the user does not need to wait for — emails, thumbnails, fan-out, analytics — goes on a queue.

---

## Stage 5 — Deep dive (25–40 min)

The interviewer usually picks the topic. If they do not, offer the one that is genuinely hardest in this system:

| System type | The interesting deep dive |
|---|---|
| Feed / timeline | Fan-out on write vs on read, and the celebrity problem |
| Chat | Connection management, delivery guarantees, ordering |
| Booking | Concurrency control on the last seat |
| Search / typeahead | Index structure and update path |
| Video | Chunking, transcoding pipeline, CDN strategy |
| Ride hailing | Geospatial indexing and matching |
| Any at scale | Sharding key choice and hot partitions |

**Go deep on two or three, not shallow on ten.** Depth is the differentiator; breadth is table stakes.

---

## Stage 6 — Bottlenecks and wrap-up (40–45 min)

Volunteer the weaknesses before you are asked. It reads as maturity, and it lets you frame them.

- **Single points of failure:** what happens if the cache dies? The primary database? A whole availability zone?
- **Hot spots:** a celebrity user, a viral link, a popular shard key.
- **The thundering herd:** a cache expiry that sends every request to the database at once.
- **What scales next:** which component hits its limit first as traffic grows 10×.
- **What you would monitor:** the three or four metrics that would tell you this system is unhealthy.

---

## What separates a strong candidate

| Weak | Strong |
|---|---|
| Starts drawing immediately | Spends five minutes on requirements |
| "We'd use Kafka" | "We need durable, ordered, replayable delivery — Kafka gives that; SQS wouldn't preserve order" |
| Presents one design | Presents an option, names the trade-off, picks one and says why |
| Goes quiet while thinking | Narrates: "I'm weighing two options here…" |
| Defends every choice | "That's a fair point — if writes dominate, my choice is wrong. I'd switch to…" |
| Ignores failure | Volunteers what breaks and how it degrades |
| Even detail everywhere | Deep on the two components that matter |

**Being talked out of a position by a good argument is a positive signal, not a loss.** Interviewers frequently push back specifically to see whether you can update.

---

## Phrases worth having ready

- "Let me make sure I understand the scope before I design anything."
- "I'll assume X — tell me if that's wrong and I'll revisit."
- "The trade-off here is A versus B; I'm choosing A because the requirement says…"
- "This is the part I'd want to be most careful about, so let me go deeper."
- "That's the bottleneck. Here's how I'd address it if we had to scale 10×."
- "I don't know that in detail, but here's how I'd reason about it."

That last one is important. **Saying "I don't know" honestly, and then reasoning from principles, scores far better than bluffing** — and bluffing is detected almost every time.

---

## Practising alone

1. Pick a case study from `03_HLD_Case_Studies`.
2. Set a 45-minute timer and work through these six stages on paper, **speaking aloud**.
3. Only then read the notes, and mark every decision you missed.
4. Write your own version.
5. Repeat the same case a week later — you will be surprised how much needs re-deriving.

Four case studies done this way beat twelve read passively.
