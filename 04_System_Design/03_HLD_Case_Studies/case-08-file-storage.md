# HLD — Distributed File Storage (Dropbox / Google Drive)

> The case study about **sync**, not storage. Storing bytes is a solved problem; keeping several devices consistent while people edit offline is not.

---

## 1. Requirements

**Functional**
- Upload, download, delete files.
- Automatic sync across a user's devices.
- Sharing with other users, with permissions.
- Version history and restore.
- Offline editing, syncing on reconnect.

**Non-functional**
- 50M users, average 100 files, average 1 MB.
- Sync should propagate within seconds when online.
- **Durability is paramount** — losing a user's file is unacceptable in a way that losing a view count is not.
- Bandwidth efficiency matters: users are on metered and slow connections.

---

## 2. Estimation

```
STORAGE
  50M users × 100 files × 1 MB   = 5 PB raw
  with deduplication (~30% saving) ≈ 3.5 PB
  with 3× replication              ≈ 10 PB

METADATA
  50M × 100 = 5B file records × ~500 B = 2.5 TB   ← fits a sharded database

TRAFFIC
  Say 10% of users active daily, 10 file changes each
  5M × 10 = 50M changes/day ÷ 10^5 s   = 500 writes/second
  Reads ~10× that                       = 5,000/second
```

**What the numbers decide:** 10 PB of blobs but only 2.5 TB of metadata — a factor of 4,000. **These are two entirely different storage problems and must be separated**: blobs to object storage, metadata to a sharded database. That separation is the first architectural decision.

---

## 3. The chunking decision — the heart of the design

**Do not store files as single objects. Split them into fixed-size chunks (typically 4 MB).**

This single decision buys four things:

1. **Resumable transfer.** A dropped connection resumes at the last chunk, not from the beginning of a 2 GB file.
2. **Delta sync.** Editing one paragraph of a document changes one chunk; only that chunk is re-uploaded. **This is the big one** — it turns a 100 MB re-upload into 4 MB.
3. **Deduplication.** Chunks are content-addressed by hash, so the same chunk stored by a thousand users is stored **once**. For commonly-shared files this is a very large saving.
4. **Parallel transfer.** Chunks upload and download concurrently.

```
file.pdf (12 MB)
  ├── chunk 0  hash a3f9...   4 MB
  ├── chunk 1  hash 7b2c...   4 MB
  └── chunk 2  hash e14d...   4 MB

metadata: file → ordered list of chunk hashes
blob store: hash → bytes     (shared across all users)
```

**Content-addressed storage** means the chunk store is a simple immutable key-value map from hash to bytes. Immutability makes it trivially cacheable and trivially replicable.

**Variable-size chunking** (content-defined, using a rolling hash) is better still: inserting a byte at the start of a file shifts all fixed-size boundaries and invalidates every chunk, whereas content-defined boundaries move with the content. Worth mentioning as a refinement.

---

## 4. Architecture

```
                  ┌──────────────────────────┐
   Clients ──────►│    API / Sync Service    │
   (desktop,      └───┬──────────────────┬───┘
    mobile, web)      │                  │
                      ▼                  ▼
          ┌────────────────────┐  ┌──────────────────────┐
          │  Metadata store    │  │  Block/Chunk service │
          │  (sharded SQL)     │  │                      │
          │  files, versions,  │  └──────────┬───────────┘
          │  chunk lists, ACLs │             ▼
          └─────────┬──────────┘  ┌──────────────────────┐
                    │             │  Object storage (S3) │
                    │             │  hash → chunk bytes  │
                    ▼             └──────────────────────┘
          ┌────────────────────┐
          │ Notification svc   │  long-poll / WebSocket to other devices
          └────────────────────┘
```

**Metadata and blobs are separate services with separate stores.** Metadata is small, relational (files, folders, permissions, versions) and queried in complex ways. Blobs are enormous, immutable and accessed only by key. Nothing is gained by putting them together and a great deal is lost.

**Uploads go client → object storage directly** via pre-signed URLs; the API server handles only metadata. At petabyte scale, routing bytes through application servers is not viable.

---

## 5. The sync protocol

1. The client watches the local filesystem for changes.
2. On a change, it chunks the file and computes each chunk's hash.
3. It asks the server **which chunks are already known** (`POST /chunks/check` with the hash list).
4. It uploads only the unknown chunks, in parallel, directly to object storage.
5. It commits the new file version: a metadata write listing the ordered chunk hashes.
6. The server notifies the user's **other devices** via a long-poll or WebSocket connection.
7. Those devices fetch the new metadata, determine which chunks they lack, and download only those.

**Step 3 is what makes this efficient**, and it is worth calling out: the client never uploads a chunk the server already has, whether it came from this user, this device, or an entirely different user.

**Long polling is a legitimate choice here**, not a weaker one. Changes are infrequent relative to a chat system, so holding 50 million WebSockets open is unjustified. A long-poll that returns on change or after 60 seconds is cheaper and simpler.

---

## 6. Conflict resolution

Two devices edit the same file while offline. Both sync. Now what?

**Option 1 — last write wins.** Simple, and it silently destroys someone's work. Unacceptable for user files.

**Option 2 — keep both.** Create `document (Alice's conflicted copy).docx`. Ugly, and **it is what Dropbox actually does**, because it never loses data and the user can resolve it. For a general-purpose file store this is the right answer, and saying so plainly — including that it is deliberately unglamorous — is stronger than proposing automatic merging that cannot work for binary files.

**Option 3 — operational transformation or CRDTs.** Genuine automatic merging, as in collaborative editors. **Only possible for structured, text-like content**; meaningless for a JPEG or a zip archive.

**The version-vector mechanism** for *detecting* the conflict: each file version carries a vector of per-device counters. If neither version's vector dominates the other, the edits were concurrent and it is a conflict. This is how you tell "B is a descendant of A" from "A and B diverged" — and it is the detail that distinguishes a considered answer.

---

## 7. Deep dives

### Versioning
Because chunks are immutable and content-addressed, **a new version is just a new chunk list** — unchanged chunks are shared between versions at zero cost. Version history is therefore nearly free, which is an elegant consequence of the chunking decision.

Retention: keep all versions for 30 days, then thin them (hourly → daily → weekly). Garbage-collect chunks referenced by no surviving version, using reference counting or a periodic mark-and-sweep.

### Sharing and permissions
An ACL per file or folder, with inheritance down the tree. The interesting problem is **permission checks on deep hierarchies**: checking access to a file nested 20 folders deep should not require 20 lookups. Denormalise by storing the materialised path or an effective-permissions cache per file, invalidated when an ancestor's ACL changes.

### Deduplication scope
**Global dedup** (across all users) maximises savings but leaks information: a user can determine whether a file already exists in the system by observing whether the upload was skipped. **Per-user dedup** avoids the leak with lower savings. Most consumer services accept the trade-off; a security-conscious one should not.

This is a good point to raise unprompted, because it shows you think about a design's second-order effects.

### Durability
Object storage with cross-region replication gives eleven nines. Metadata needs its own backups plus point-in-time recovery, because losing the chunk list makes the chunks unreachable — **the metadata is more critical than the blobs**, even though it is four thousand times smaller. That inversion is worth stating.

---

## 8. Bottlenecks and failure modes

| Risk | Impact | Mitigation |
|---|---|---|
| Metadata store growth | 5B rows | Shard by `user_id`; a user's files are always queried together |
| Large-file upload failure | Wasted bandwidth | Chunked, resumable, parallel |
| Sync storms (a shared folder changes) | Many devices fetch at once | Stagger notifications; the CDN absorbs chunk downloads |
| Chunk GC deleting a live chunk | Data loss | Reference counting with a grace period; never delete a chunk younger than N days |
| Notification service outage | Sync stalls | Clients fall back to periodic polling |
| Hot chunk (a widely shared file) | Object storage hot key | Chunks are immutable — cache and CDN them freely |

---

## 9. Trade-offs to state

| Choice | Alternative | Why |
|---|---|---|
| Chunked, content-addressed storage | Whole-file objects | Delta sync, dedup, resumability and cheap versioning all follow from it |
| Metadata and blobs separated | One store | 2.5 TB of relational metadata and 10 PB of immutable blobs are different problems |
| Direct-to-object-storage upload | Through app servers | Petabytes cannot be proxied |
| Conflicted copies | Last write wins | Never lose user data; automatic merge is impossible for binary files |
| Long polling | WebSockets | Change frequency does not justify 50M persistent connections |
| Global dedup | Per-user dedup | Large saving, at the cost of an existence-inference leak — state the choice |

---

## 10. Practice

- [ ] Design from scratch in 45 minutes
- [ ] Work out the dedup saving for a file shared by 10,000 users
- [ ] Design the chunk garbage collector safely
- [ ] Design the permission check for a file 20 folders deep
- [ ] Explain version vectors and show a concurrent-edit example
