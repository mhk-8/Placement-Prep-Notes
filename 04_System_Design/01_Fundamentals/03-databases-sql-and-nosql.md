# Databases: SQL and NoSQL

> The storage choice constrains everything else in a design, and it is the decision interviewers probe hardest. The goal is not to memorise product features but to be able to derive the right family from the **access patterns**.

---

## 1. Start from the access patterns, not the technology

Before naming any database, answer:

1. **What are the read queries?** By primary key? By range? By several attributes? Full-text? Aggregations across millions of rows?
2. **What is the write pattern?** Single-row inserts? Bulk loads? High-frequency updates to the same row?
3. **What is the read:write ratio?**
4. **Do entities have relationships that must be traversed or joined?**
5. **What consistency does the business require?** Can a read be five seconds stale?
6. **How much data, and how fast does it grow?**
7. **Is the schema stable, or genuinely variable per record?**

**"Key lookups by a single id, no joins, 100:1 read ratio, 10 TB" derives a key-value store.** Announcing "we'll use DynamoDB" and then rationalising is the failure mode to avoid.

---

## 2. Relational databases

### What they give you
- **A relational model with joins**, so data is stored once and combined at query time.
- **ACID transactions** across multiple rows and tables.
- **A declarative query language** with a cost-based optimiser — you say what you want, not how.
- **Constraints enforced by the engine**: foreign keys, uniqueness, checks. The database refuses to hold invalid data regardless of application bugs.
- **Decades of tooling**: backups, replication, migration frameworks, query analysers.

### Internals worth knowing

**B+ tree indexes.** Data pointers live only in the leaves, which are linked; internal nodes hold keys only, so they pack more keys per page and the tree is shallow (3–4 levels for millions of rows). A point lookup is a handful of page reads; a range scan is one descent plus a sequential walk along the linked leaves. This is why relational databases excel at range queries and ordered access.

**MVCC (multi-version concurrency control).** Each transaction sees a consistent snapshot, so readers never block writers and writers never block readers. The cost is storing old row versions and reclaiming them later (`VACUUM` in Postgres, the purge thread in InnoDB) — which is where the surprising operational problems live.

**The write-ahead log.** Changes are appended to a durable sequential log before being applied to data pages. Sequential writes are far faster than random ones, and the log gives both atomicity (undo) and durability (redo). It is also the foundation of replication: followers replay the leader's log.

### Scaling a relational database, in order

1. **Indexes and query tuning.** Most "we need to scale the database" problems are a missing index or an N+1 query pattern.
2. **A bigger machine.** Genuinely gets you a long way.
3. **Read replicas.** Route reads to followers. Solves read scaling completely; does nothing for writes, and introduces replication lag.
4. **Caching.** Removes read load before it reaches the database at all.
5. **Functional partitioning.** Move separate concerns to separate databases (users here, orders there). Loses cross-database joins and transactions.
6. **Sharding.** Split rows across databases by a key. Large complexity increase; see `04`.

**Do these in order.** Jumping to sharding when an index would do is a design error, and saying so is a positive signal.

### When relational is right
Financial data, anything with genuine invariants ("an order must reference a real customer"), complex ad-hoc querying, moderate scale, and — importantly — **when you do not yet know all the queries**. Relational schemas tolerate new query patterns; denormalised NoSQL schemas often do not.

---

## 3. The NoSQL families

"NoSQL" is not one thing. Four families with genuinely different shapes:

### Key-value stores
*Redis, DynamoDB, Memcached, etcd*

A dictionary: `get(key)`, `put(key, value)`. The value is opaque to the store.

**Strengths.** Simplest possible model, so the fastest and easiest to shard — the key hashes directly to a partition. Predictable O(1) latency.

**Limits.** No querying by value, no ranges unless the store adds them, no relationships.

**Fits:** sessions, caches, feature flags, user preferences, shortened URLs, rate-limit counters.

### Document stores
*MongoDB, Couchbase, DynamoDB (document mode), Firestore*

Store JSON-like documents; query on fields inside them; secondary indexes supported.

**Strengths.** The document usually matches the object the application manipulates, so no object-relational mapping. Schema is flexible per record. Embedding related data means one read instead of a join.

**Limits.** Joins are weak or absent. Embedding duplicates data, so updating a duplicated field means updating many documents. Transactions across documents are supported now but are not the natural grain.

**Fits:** content management, product catalogues with varying attributes, user profiles, event records.

**The modelling rule:** embed what is read together and written together; reference what is large, shared, or independently updated. A blog post embeds its tags; it references its author.

### Wide-column stores
*Cassandra, HBase, ScyllaDB, Bigtable*

Rows are identified by a **partition key** and contain many columns; within a partition, rows are sorted by a **clustering key**.

**Strengths.** Extremely high write throughput (LSM-tree storage: writes are appends), linear horizontal scaling, tuneable consistency per query, no single point of failure in Cassandra's leaderless design.

**Limits.** **You must design the table around the query.** There are no ad-hoc queries, no joins, and querying by anything other than the key requires a second table holding the same data differently. Adding a new query pattern later often means a migration.

**Fits:** time-series data, event logs, messaging history, IoT telemetry, anything append-heavy where the queries are known in advance.

**The canonical shape:** `PRIMARY KEY ((user_id), timestamp)` — partition by user, sort by time — gives "fetch this user's last N messages" as a single sequential read within one partition.

### Graph databases
*Neo4j, Neptune, JanusGraph*

Nodes and edges are first-class; traversals are the primitive operation.

**Strengths.** Multi-hop relationship queries ("friends of friends who like X") that would be five self-joins in SQL and are one traversal here.

**Limits.** Harder to shard (a graph resists partitioning — edges cross boundaries). Narrower tooling and ecosystem.

**Fits:** social graphs, recommendation engines, fraud-ring detection, knowledge graphs, permission hierarchies.

---

## 4. The comparison that matters

| | Relational | Key-value | Document | Wide-column | Graph |
|---|---|---|---|---|---|
| Query flexibility | **highest** | lowest | medium | low | high for traversals |
| Joins | yes | no | weak | no | traversals |
| Schema | fixed | none | flexible | semi-fixed | flexible |
| Horizontal scaling | hard | **easiest** | easy | **easiest** | hardest |
| Write throughput | moderate | very high | high | **very high** | moderate |
| Transactions | **full ACID** | single key | single document (multi now) | limited | varies |
| Ad-hoc queries | **yes** | no | some | **no** | yes |

**The honest summary:** relational databases trade write scalability for query flexibility and integrity; NoSQL stores trade query flexibility and integrity for write scalability and operational simplicity at scale. Neither is more modern than the other.

---

## 5. Storage engines: B+ tree vs LSM tree

This is the mechanism behind "why is Cassandra fast at writes", and it is worth being able to explain.

| | B+ tree | LSM tree |
|---|---|---|
| Write path | locate the page, modify in place (random I/O) | append to an in-memory memtable, flush sequentially |
| Read path | one descent, few page reads | may check memtable plus several SSTables |
| Write amplification | lower | higher (compaction rewrites data) |
| Read amplification | lower | higher (mitigated by Bloom filters) |
| Space | fragmentation from partly-full pages | compaction reclaims, but needs headroom |
| Used by | Postgres, MySQL/InnoDB, SQLite | Cassandra, RocksDB, LevelDB, HBase |

**LSM trees are write-optimised** because every write is a sequential append. The cost is deferred: background **compaction** merges SSTables, which consumes I/O and can cause latency spikes. Each SSTable carries a Bloom filter so a read can skip files that definitely do not contain the key.

**B+ trees are read-optimised** and give predictable latency, at the cost of random writes and in-place page modification.

---

## 6. Polyglot persistence

Real systems use several stores, each for what it is good at. A plausible e-commerce split:

| Data | Store | Why |
|---|---|---|
| Orders, payments, inventory | PostgreSQL | transactions and invariants are non-negotiable |
| Product catalogue | Elasticsearch | full-text search and faceted filtering |
| Sessions, cart | Redis | fast, ephemeral, key lookups |
| Clickstream / events | Kafka → S3 → warehouse | high-volume append, analytical later |
| Recommendations graph | Neo4j or a precomputed table | traversals |
| Images and video | S3 + CDN | blobs never belong in a database |

**The cost is real and worth naming:** more operational surface, more failure modes, and consistency *between* stores becomes your problem (usually solved with change-data-capture or an outbox pattern). Saying "I'd start with Postgres for everything and split out only what demonstrably needs it" is a mature answer.

---

## 7. Blob storage

Files — images, videos, documents, backups — do not belong in a database. Object storage (S3, GCS, Azure Blob) gives effectively unlimited capacity, very low cost per byte, and durability of eleven nines, at the cost of higher latency and no querying.

**The standard pattern:** store the **object in blob storage** and the **metadata plus the URL in the database**. Uploads go direct from the client to object storage via a **pre-signed URL**, so the bytes never pass through your application servers — a detail worth volunteering in any design involving uploads.

---

## 8. Choosing, in an interview

A defensible derivation sounds like this:

> "Reads are always by a single key, there are no joins, the read:write ratio is 100:1, and the data is 10 TB growing 3 TB a year. That's a key-value workload — I'd use DynamoDB or Cassandra, partitioned on the short code, with Redis in front for the hot set. I'm giving up ad-hoc querying, which is fine because the only query is the lookup."

> "Orders have real invariants — stock can't go negative, an order must reference a real customer — and the scale is a few thousand writes per second, which one Postgres instance handles. I'd use Postgres with read replicas, and revisit sharding if writes grow ten-fold."

Both name the access pattern, derive the family, and state what is being given up.

---

## 9. Recall questions

1. List the seven questions to answer before choosing a database.
2. Why are B+ tree leaves linked, and what does that buy?
3. What does MVCC achieve, and what does it cost operationally?
4. Give the five scaling steps for a relational database, in order.
5. Why is a key-value store the easiest family to shard?
6. Give the document-store modelling rule for embedding versus referencing.
7. Why must a wide-column table be designed around the query?
8. What does the primary key `((user_id), timestamp)` give you physically?
9. Contrast B+ tree and LSM tree on write path, read path and amplification.
10. What is compaction, and what problem does it cause?
11. Why do SSTables carry Bloom filters?
12. What is polyglot persistence and what does it cost?
13. Why do blobs not belong in a database, and what is the pre-signed URL pattern?
14. Derive a storage choice, out loud, for a system with 100:1 reads, key-only lookups and 10 TB.
