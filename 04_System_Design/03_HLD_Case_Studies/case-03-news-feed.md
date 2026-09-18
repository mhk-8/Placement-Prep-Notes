# HLD — News Feed (Twitter / Instagram timeline)

> The canonical "fan-out" problem, and the case study where **the estimation discovers the hard problem**. Study this one properly; the push/pull reasoning transfers to notifications, activity streams and chat.

---

## 1. Requirements

**Functional**
- A user posts content (text, optionally media).
- A user's feed shows recent posts from people they follow, newest first.
- Follow and unfollow.
- Feed loads must be fast and paginated.

**Non-functional**
- 300M DAU; each posts ~2/day and loads their feed ~10/day.
- **Feed load p99 under 200 ms.**
- Eventual consistency is acceptable — a post appearing a few seconds late is invisible. **Except** the author's own post must appear immediately (read-your-writes).
- Availability over consistency: a stale feed beats an error page.

**Out of scope:** ranking quality, ads, direct messages, stories.

---

## 2. Estimation — and the problem it uncovers

```
POSTS      300M × 2 = 600M/day ÷ 10^5 s = 6,000/s       peak ×3 = 18,000/s
FEED READS 300M × 10 = 3B/day ÷ 10^5    = 30,000/s      peak    = 90,000/s

STORAGE
  post metadata ~1 KB × 600M            = 600 GB/day ≈ 220 TB/year
  media: 10% with a 200 KB image
         60M × 200 KB                   = 12 TB/day    ← 20× the metadata

FAN-OUT (the actual problem)
  average 200 followers
  6,000 posts/s × 200 followers         = 1.2M timeline writes/second
```

**1.2 million timeline writes per second** is the number that shapes everything. It is why the naive "write to every follower's timeline" design fails, and why the hybrid model exists.

Note also that **media dominates storage by 20×** — so media goes to object storage plus a CDN, never into the database.

---

## 3. The central decision: fan-out on write vs on read

### Fan-out on write (push)
When a user posts, **write the post id into every follower's precomputed timeline**.

- **Read:** trivially cheap — the timeline is already assembled. One cache read.
- **Write:** expensive and proportional to follower count.
- **Catastrophic for celebrities:** a user with 100 million followers generates 100 million timeline writes for one post. At 18,000 posts/second overall, a handful of such accounts can saturate the entire write path.

### Fan-out on read (pull)
Store posts only once. At read time, **fetch the recent posts of everyone the user follows and merge**.

- **Write:** trivially cheap — one insert.
- **Read:** expensive — N queries (or one large scatter) plus a merge, on every feed load.
- **Catastrophic for users following thousands of accounts**, and it happens 90,000 times a second.

### The hybrid — what real systems do

**Push for ordinary users, pull for celebrities.**

- On posting, fan out to followers **unless** the author exceeds a follower threshold (say 100,000).
- On reading, take the precomputed timeline **and** merge in the recent posts of the small number of celebrities this user follows, at read time.

**Why this works:** the distribution is extremely skewed. The vast majority of users have few followers, so push is cheap for them. The tiny number of celebrities are followed by many, but each user follows only a handful of celebrities — so the read-time merge is small and bounded.

**This is the answer to give, and the reasoning is what is graded, not the conclusion.** Say the numbers: "push costs 1.2M writes/second overall, which is fine; the problem is entirely the tail of the follower distribution, so I special-case only that tail."

---

## 4. API

```
POST /v1/posts            { text, mediaIds[] }              → 201 { postId }
GET  /v1/feed?cursor=&limit=20                              → { posts[], nextCursor }
POST /v1/users/{id}/follow
GET  /v1/users/{id}/posts?cursor=&limit=20
POST /v1/media/upload-url { contentType }                   → { uploadUrl, mediaId }
```

**Cursor pagination, not offset.** New posts arrive constantly at the head of the feed, so `OFFSET 20` would show the user duplicates or skip posts. The cursor encodes `(timestamp, post_id)` and is stable under insertion.

**Media uploads use a pre-signed URL** so bytes go client → object storage directly, never through the application servers. At 12 TB/day that is not an optimisation, it is a requirement.

---

## 5. Data model

```
posts            (post_id PK, author_id, text, media_ids[], created_at)
                 partitioned by post_id (snowflake: time-ordered + shard bits)

follows          (follower_id, followee_id, created_at)
                 partitioned by follower_id  -- "who do I follow" is the hot query
follower_index   (followee_id, follower_id)
                 partitioned by followee_id  -- "who follows me", for fan-out

timelines        (user_id, post_id, created_at)   -- precomputed, push path
                 partitioned by user_id, clustered by created_at DESC
                 capped at ~800 entries per user

user_stats       (user_id, follower_count, is_celebrity)
```

**Two follow tables**, deliberately denormalised. The forward direction answers "whose posts do I merge at read time"; the reverse answers "who do I fan out to on write". A single table would force a scatter-gather for one of them.

**Post ids are Snowflake ids** — 41 bits of timestamp, 10 bits of machine, 12 bits of sequence. They are globally unique, roughly time-ordered (so sorting by id sorts by time), and generated without coordination. That last property matters at 18,000 posts/second.

**Timelines are capped** at a few hundred entries. Nobody scrolls past that, and an uncapped timeline for a user following thousands of prolific accounts grows without bound.

---

## 6. Architecture

```
                         ┌────────────────┐
   Client ──────────────►│ Load Balancer  │
                         └───────┬────────┘
                                 ▼
                    ┌────────────────────────┐
                    │     API Gateway        │
                    └──┬──────────────────┬──┘
                       ▼                  ▼
            ┌────────────────┐   ┌──────────────────┐
            │  Post Service  │   │   Feed Service   │
            └───────┬────────┘   └────────┬─────────┘
                    │                     │  read timeline cache
                    │ post created        │  + merge celebrity posts
                    ▼                     ▼
            ┌────────────────┐   ┌──────────────────┐
            │  Kafka (posts) │   │  Redis timelines │
            └───────┬────────┘   └──────────────────┘
                    ▼                     ▲
            ┌────────────────┐            │
            │ Fan-out worker │────────────┘  writes to non-celebrity followers
            └───────┬────────┘
                    ▼
            ┌────────────────────────────────────────┐
            │  posts store │ follows store │ media   │
            │  (Cassandra) │  (Cassandra)  │ (S3+CDN)│
            └────────────────────────────────────────┘
```

**Write path:** post is persisted → event on Kafka → fan-out workers look up followers → for each non-celebrity follower, prepend the post id to their Redis timeline (trimming to the cap). The user's response returns as soon as the post is durable; fan-out is asynchronous.

**Read path:** fetch the precomputed timeline from Redis → identify the celebrities this user follows → fetch their recent posts (cached, and shared across all their followers) → merge by time → hydrate post bodies → return.

**The celebrity posts cache is shared**, which is the efficiency that makes pull cheap: one cached list serves all 100 million followers.

---

## 7. Deep dives

### Read-your-writes
The author must see their own post immediately, even though fan-out is asynchronous. **Insert the post into the author's own timeline synchronously** on the write path, before returning. It is one extra write and it removes the most noticeable staleness artefact.

### The thundering herd on a celebrity post
When a celebrity posts, their cached recent-posts list is invalidated and millions of feed reads miss simultaneously. Mitigate with **request coalescing** (the first miss populates, the rest wait) and by **updating** the cached list in place rather than invalidating it.

### Fan-out latency
For a user with 100,000 followers (below the celebrity threshold), fan-out is 100,000 writes. Batch them, parallelise across workers, and **prioritise recently-active followers first** so the people most likely to look soon see it soonest. Complete fan-out within seconds is the target; complete fan-out within milliseconds is neither achievable nor necessary.

### Inactive users
Roughly half of registered users are not active. **Do not fan out to them** — mark accounts inactive after N days and build their timeline lazily on next login. This can cut fan-out volume dramatically for essentially no cost.

### Ranking
The requirements said chronological. If ranking is added, the timeline holds candidates and a ranking service scores them at read time using engagement features. **Keep ranking out of the fan-out path** — it changes far more often than the storage layer and must be independently deployable.

---

## 8. Bottlenecks and failure modes

| Risk | Impact | Mitigation |
|---|---|---|
| Celebrity post fan-out | Write path saturation | The hybrid model — pull for celebrities |
| Timeline cache loss | 90,000 reads/s fall through | Timelines are rebuildable from posts + follows; rebuild lazily, serve degraded |
| Fan-out worker backlog | Posts appear late | Autoscale on **consumer lag**; prioritise active followers |
| Hot partition (a viral post) | One shard saturates | The post body is cached; reads never reach the shard |
| Media bandwidth | 12 TB/day egress | CDN; the origin serves each object once per edge |
| A user follows 50,000 accounts | Read-time merge is large | Cap the merge set; fall back to pure push for them |

**What breaks first at 10×:** the fan-out tier. It is the component whose cost is proportional to the *product* of posts and followers, so it grows quadratically in a sense the others do not. Lowering the celebrity threshold is the lever.

---

## 9. Trade-offs to state

| Choice | Alternative | Why |
|---|---|---|
| Hybrid push/pull | Pure push or pure pull | Push fails on the follower-count tail; pull fails on read volume |
| Async fan-out via Kafka | Synchronous fan-out | The user must not wait for 200 writes |
| Sync insert into the author's own timeline | Wait for fan-out | Read-your-writes, at the cost of one write |
| Capped timelines | Unbounded | Nobody scrolls past a few hundred; unbounded growth is a leak |
| Two follow tables | One table | Both directions are hot queries; a scatter-gather on either is unacceptable |
| Cassandra | Relational | Append-heavy, no joins, known access patterns, needs linear write scaling |
| Eventual consistency | Strong | Nobody can tell if a post is three seconds late |

---

## 10. Practice

- [ ] Design from scratch in 45 minutes, deriving the 1.2M writes/second yourself
- [ ] Work out the celebrity threshold from the cost of push versus pull
- [ ] Add ranking — where does it go and why not in fan-out?
- [ ] Handle unfollow: does the timeline need cleaning immediately?
- [ ] Design the timeline rebuild path after a total cache loss
