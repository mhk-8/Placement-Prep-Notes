# HLD — Video Streaming (YouTube / Netflix)

> The case study where **the CDN is not an optimisation, it is the architecture**. It also has the cleanest example of an asynchronous processing pipeline.

---

## 1. Requirements

**Functional**
- Upload a video.
- Watch a video, with adaptive quality.
- Search and browse.
- View counts, likes, comments.

**Non-functional**
- 10M DAU, each watching ~30 minutes of 1080p a day.
- **Playback must start in under 2 seconds** and not rebuffer.
- Uploads may take minutes to become watchable — that is acceptable and should be stated.
- Global audience: viewers are far from any single origin.

---

## 2. Estimation — and why the CDN follows from it

```
BANDWIDTH (the dominant number)
  1080p ≈ 5 Mbps
  Assume 5% of DAU watching at peak = 500,000 concurrent streams
  500,000 × 5 Mbps                  = 2.5 Tbps

STORAGE
  Say 500,000 minutes uploaded/day at ~50 MB/min raw = 25 TB/day raw
  × ~5 transcoded renditions (240p…1080p)            = 125 TB/day
  Annually                                           ≈ 45 PB/year
```

**2.5 Tbps cannot come from an origin.** A well-connected datacentre has tens to low hundreds of Gbps of egress. This single number forces a CDN, and forces the design to be *about* content distribution rather than about a request/response service.

**The second consequence:** 125 TB/day of transcoded output means transcoding is a large batch pipeline, not something that happens during an upload request.

---

## 3. Architecture

```
  UPLOAD PATH
  ───────────
  Client ──pre-signed URL──► Object storage (raw)
     │                              │
     │ notify                       ▼
     ▼                     ┌──────────────────┐
  Upload Service ─────────►│  Kafka (jobs)    │
     │ metadata            └────────┬─────────┘
     ▼                              ▼
  Metadata DB            ┌────────────────────────┐
                         │  Transcoding pipeline  │
                         │  split → encode → pack │
                         └───────────┬────────────┘
                                     ▼
                         ┌────────────────────────┐
                         │ Object storage (HLS)   │
                         │ segments + manifests   │
                         └───────────┬────────────┘
                                     ▼
                         ┌────────────────────────┐
                         │        CDN             │
                         └────────────────────────┘

  PLAYBACK PATH
  ─────────────
  Client → API (metadata, manifest URL) → CDN edge → segments
           │
           └─► view event → Kafka → analytics
```

---

## 4. The upload and transcoding pipeline

**Upload.** The client requests a **pre-signed URL** and uploads directly to object storage — the bytes never touch the application servers. For large files, use **multipart upload with resumability**, so a dropped connection resumes rather than restarting a 2 GB upload.

**Transcoding**, once the raw file lands:

1. **Validate** — format, duration, codec; reject early.
2. **Split** into segments (typically 2–10 seconds each). This is what makes the next step parallel.
3. **Encode** each segment into each rendition (240p, 360p, 480p, 720p, 1080p) — **massively parallel** across a worker fleet, because segments are independent.
4. **Package** into HLS or DASH: the segments plus a **manifest** listing the available renditions and their segment URLs.
5. **Generate thumbnails** and extract metadata.
6. **Publish** — write the manifest URL into the metadata store and mark the video available.

**Why segment-level parallelism matters:** a 60-minute video at 5 renditions is a single enormous job if encoded whole, and 1,800 small independent jobs if segmented. The second finishes in minutes on a large fleet; the first does not.

**The DAG has dependencies** (package needs all segments of a rendition), so this is a workflow, not a plain queue — an orchestrator tracking per-video state, with retries per segment and a dead letter queue for repeated failures.

---

## 5. Adaptive bitrate streaming

The manifest lists several renditions. The **client** measures its throughput and buffer level and requests the next segment at an appropriate quality, switching mid-stream at segment boundaries.

```
manifest.m3u8
  ├── 240p/  segment0.ts  segment1.ts  ...
  ├── 480p/  segment0.ts  segment1.ts  ...
  └── 1080p/ segment0.ts  segment1.ts  ...
```

**The client decides, not the server.** This is the key architectural property: the server serves static files, so the CDN can cache everything and the intelligence lives at the edge of the system where the network conditions actually are.

**Start-up latency** comes from fetching the manifest plus the first segment. Starting at a low rendition and stepping up gives a fast start; short segments reduce start-up latency but increase request overhead. Two to four seconds is the usual compromise.

---

## 6. The CDN strategy

**Everything served to viewers is a static file** — segments and manifests — which is precisely what a CDN is good at. Segments are immutable, so they can be cached with effectively infinite TTLs and content-addressed URLs.

**Popularity is extremely skewed**, so:
- **Push** popular content to edges proactively (a major release, a trending video) before demand arrives.
- **Pull** the long tail on first request, with an **origin shield** layer so that a hundred edges missing simultaneously produce one origin fetch, not a hundred.

**Cost note worth mentioning:** CDN egress is the dominant operating cost of a video service. That is why providers build their own CDN appliances inside ISP networks (Netflix's Open Connect, Google's edge nodes) — it moves the bytes closer *and* removes the transit bill.

---

## 7. Metadata and view counts

```
videos      (video_id PK, uploader_id, title, description, duration,
             status, manifest_url, thumbnail_url, created_at)
view_events (append-only, to Kafka → analytics store)
video_stats (video_id PK, view_count, like_count)   -- rollups, async
```

**View counts are not incremented synchronously.** At peak a popular video receives enormous concurrent views; a row increment per view would be the hottest write in the system. Instead: emit an event, aggregate in windows, and write rollups. The count is seconds stale, which nobody can detect.

**Deduplication matters for views** — a "view" usually requires some watch threshold (30 seconds, say) and one count per user per period. That logic belongs in the stream processor, not the serving path.

---

## 8. Deep dives

### Search
A separate index (Elasticsearch) over title, description, tags and transcripts, updated asynchronously from metadata changes via change-data-capture. Search is a different access pattern from playback and deserves a different store.

### Comments
A straightforward service, but note the access pattern: comments are read far more than written, ordered by time or by score, and paginated with a cursor. Partition by `video_id`.

### Recommendations
Out of scope for the design, but the architectural point is worth stating: recommendations consume the **view event stream** and produce precomputed candidate lists per user, served from a cache. Keeping them off the playback path means a recommendation outage degrades the home page, not playback.

### Live streaming (a common follow-up)
The pipeline changes shape: ingest via RTMP, transcode **in real time** with a low-latency ladder, package into short segments (1–2 s), and accept 5–30 seconds of glass-to-glass latency. The CDN strategy is the same; the difference is that content is created and consumed simultaneously, so there is no pre-warming and the origin shield matters more.

---

## 9. Bottlenecks and failure modes

| Risk | Impact | Mitigation |
|---|---|---|
| CDN egress cost and capacity | The dominant cost and constraint | Multi-CDN, ISP-embedded caches, aggressive tiering by popularity |
| Transcoding backlog | Uploads take hours to publish | Autoscale on queue depth; prioritise by uploader tier |
| A viral video | Origin hammered before edges warm | Origin shield; proactive push for predicted hits |
| Object storage durability | Losing masters is unrecoverable | Keep the raw master; renditions are regenerable |
| Metadata store hot rows | View counters | Async aggregation, never synchronous increments |
| Cold start for the long tail | First viewer waits for an origin fetch | Accept it; the tail is by definition rarely watched |

**What breaks first at 10×:** CDN capacity and cost, not compute or storage. That is an unusual answer and it is the right one, which makes it worth saying explicitly.

---

## 10. Trade-offs to state

| Choice | Alternative | Why |
|---|---|---|
| CDN-centric architecture | Serving from origin | 2.5 Tbps is not servable from an origin at any price |
| Async transcoding | Transcode on upload | Minutes of work cannot sit in a request |
| Segment-level parallelism | Whole-file encode | Turns one long job into thousands of short independent ones |
| Client-driven ABR | Server-selected quality | Keeps served files static and cacheable; the client knows its own network |
| Pre-signed direct upload | Upload through app servers | 25 TB/day through the API tier is absurd |
| Async view counts | Synchronous increments | Hot-row writes would dominate the system |
| Keep the raw master | Store only renditions | Renditions can be regenerated; a lost master cannot |

---

## 11. Practice

- [ ] Design from scratch in 45 minutes, deriving the 2.5 Tbps yourself
- [ ] Design the transcoding orchestrator: state machine, retries, partial failure
- [ ] Add live streaming — what changes and what stays?
- [ ] Add DRM and signed URLs — where do they sit relative to the CDN?
- [ ] Estimate the CDN bill at 2.5 Tbps peak and argue for ISP-embedded caches
