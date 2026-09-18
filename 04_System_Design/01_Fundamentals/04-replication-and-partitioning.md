# Replication and Partitioning

> Two different things that are constantly confused. **Replication copies the same data to several nodes** (for availability and read throughput). **Partitioning splits different data across nodes** (for write throughput and capacity). Real systems do both, and the interaction is where the difficulty lives.

---

## 1. Replication

### Why replicate
1. **Availability** — a node dies, another serves.
2. **Read throughput** — reads spread across replicas.
3. **Geographic latency** — a replica near the user avoids a cross-continent round trip.
4. **Durability** — data survives the loss of a machine or a datacentre.

### Leader–follower (single-leader)

All writes go to one leader, which streams its change log to followers. Reads may go to any node.

```
        writes
          │
          ▼
      ┌────────┐   replication log    ┌──────────┐
      │ LEADER │ ───────────────────► │ FOLLOWER │ ◄── reads
      └────────┘ ───────────────────► ├──────────┤
                                      │ FOLLOWER │ ◄── reads
                                      └──────────┘
```

**Advantages.** No write conflicts — there is one authority for ordering. Simple to reason about. This is what Postgres, MySQL, MongoDB and most managed databases do by default.

**Limits.** Write throughput is bounded by one machine. A leader failure requires **failover**, which is where the hard problems are.

### Synchronous vs asynchronous replication

| | Synchronous | Asynchronous | Semi-synchronous |
|---|---|---|---|
| Leader waits for | follower acknowledgement | nothing | **one** follower |
| Write latency | higher, bounded by the slowest follower | low | moderate |
| Data loss on leader failure | none | **possible** | bounded |
| Availability | a slow follower blocks writes | unaffected | tolerates all but one |

**Fully synchronous replication to all followers is almost never used**: one slow or dead follower stops all writes. **Semi-synchronous** — wait for one follower, let the rest lag — is the common compromise, and is worth naming as the default.

### Replication lag and its user-visible symptoms

A follower is behind by milliseconds to seconds, and occasionally much more under load. Three anomalies follow:

**Read-your-writes violation.** A user posts a comment, the write goes to the leader, their next read hits a lagging follower, and the comment is gone. The standard fix is to route a user's reads to the **leader** for a short window after they write (or to route by a session token carrying the write's log position).

**Monotonic reads violation.** Two successive reads hit different followers with different lag, so the user sees a newer value and then an older one — time appears to run backwards. Fixed by pinning a user to one replica, usually by hashing the user id.

**Consistent prefix violation.** With partitioning, a reply can become visible before the message it replies to, because they live on differently-lagging partitions. Fixed by keeping causally-related writes in the same partition.

Naming these three by name is a strong signal in a design round, because they are the concrete meaning of "eventual consistency" rather than the abstraction.

### Failover

When the leader dies:
1. **Detect** — usually a timeout on heartbeats.
2. **Elect** a new leader — the most up-to-date follower, via a consensus protocol (Raft, Paxos) or an external coordinator.
3. **Reconfigure** — clients and remaining followers point at the new leader.

Three ways this goes wrong, all worth knowing:

- **Lost writes.** With asynchronous replication, writes acknowledged by the old leader but not yet replicated are gone. If the old leader rejoins, its extra writes are usually discarded — silent data loss.
- **Split brain.** Two nodes both believe they are leader and both accept writes. Prevented by requiring a **quorum** to elect, and by fencing the old leader (STONITH, or a fencing token the storage layer checks).
- **Bad timeout tuning.** Too short causes unnecessary failovers under transient load, each of which is itself disruptive. Too long extends the outage.

### Multi-leader replication

Several nodes accept writes, each replicating to the others. Used for multi-datacentre deployments (a local leader per region), offline-capable clients, and collaborative editing.

**The cost is write conflicts.** Two regions update the same record concurrently; both accepted; now what? Resolution strategies:

- **Last write wins (LWW)** by timestamp — simple and **lossy**, and clock skew makes "last" unreliable.
- **Application-level merge** — the application is handed both versions and decides.
- **CRDTs** (conflict-free replicated data types) — structures whose merge is mathematically guaranteed to converge: grow-only counters, observed-remove sets, sequence CRDTs for text. What collaborative editors use.
- **Operational transformation** — the older alternative for text, transforming concurrent operations against each other.

### Leaderless replication (quorum)

*Dynamo, Cassandra, Riak.* Any node accepts a write; the client (or a coordinator) writes to several nodes and reads from several.

With **N** replicas, **W** write acknowledgements and **R** read responses:

```
R + W > N   ⇒   the read set and write set must overlap
                ⇒ at least one replica returns the latest value
```

Common configurations with N=3: `W=2, R=2` (balanced, strongly consistent reads), `W=3, R=1` (fast reads, writes fail if any node is down), `W=1, R=1` (fast and eventually consistent).

Repair mechanisms keep replicas converging: **read repair** (on a read, the coordinator notices a stale replica and updates it) and **anti-entropy** (a background process comparing Merkle trees of ranges and reconciling differences).

**Sloppy quorums and hinted handoff:** if the designated nodes are unreachable, write to other nodes temporarily and hand the data off when they return. Improves availability at the cost of a weaker guarantee — a quorum that did not include the "right" nodes does not actually guarantee overlap.

---

## 2. Partitioning (sharding)

### Why partition
When one machine cannot hold the data or absorb the write rate. Reads can be scaled by replication; **writes and capacity can only be scaled by partitioning.**

### Strategies

**Range partitioning.** Shard by key ranges: A–F on shard 1, G–M on shard 2. Range scans are efficient because adjacent keys live together. **The risk is hot spots** — partitioning users by name puts everyone starting with "S" on one shard, and partitioning by timestamp puts *all current writes* on the newest shard, which is the worst case.

**Hash partitioning.** `shard = hash(key) mod N`. Distribution is uniform, which removes hot spots from skewed keys. **The cost is that range queries now touch every shard**, and changing N remaps almost everything — which is what consistent hashing (`08`) solves.

**Directory / lookup-table partitioning.** A separate service maps keys to shards. Maximum flexibility (move any key anywhere), at the cost of a lookup on every request and a new component to keep available.

**Composite.** Hash the first component, range the second — Cassandra's `((user_id), timestamp)` hashes the user across the cluster and keeps that user's messages sorted within a partition. This gets uniform distribution *and* efficient range scans, and is the shape worth remembering.

### Choosing the partition key — the most consequential decision

The key must satisfy three properties:

1. **High cardinality** — many distinct values, or you cannot spread the data.
2. **Even distribution** — no value dominates.
3. **Present in the common query** — otherwise every query becomes a scatter-gather across all shards.

**Worked examples:**

| System | Bad key | Why | Better key |
|---|---|---|---|
| Social posts | `country` | a handful of values, wildly uneven | `user_id` |
| Messages | `timestamp` | all writes hit the newest shard | `conversation_id` |
| Orders | `status` | a few values, and it *changes* | `order_id` or `customer_id` |
| IoT readings | `device_type` | low cardinality | `device_id` |

**Never partition on a mutable attribute.** If a row's `status` changes, its shard changes, and moving a row between shards is a distributed transaction you did not want.

### Hot partitions

Even with a well-chosen key, one value can dominate: a celebrity's user id, a viral product, a single very active conversation.

Mitigations:
- **Salting** — append a random suffix, `celebrity_id:0` … `celebrity_id:9`, spreading writes across ten partitions. Reads must now query all ten and merge, so apply it only to the keys that need it.
- **Dedicated handling** — detect celebrity accounts and treat them differently (see the news-feed case study).
- **A cache in front** absorbs read hot spots entirely; it does nothing for write hot spots.

### Secondary indexes on a partitioned store

The awkward problem in partitioned systems.

- **Local (document-partitioned) index.** Each shard indexes only its own data. Writes are cheap — one shard. Reads by the secondary attribute must **scatter-gather across every shard**, and tail latency becomes the slowest shard's.
- **Global (term-partitioned) index.** The index itself is partitioned by the indexed term, so a read touches one shard. Writes now touch two shards (the data shard and the index shard) and are no longer atomic without a distributed transaction — so the index is usually **asynchronous and slightly stale**.

There is no free option. DynamoDB's LSI and GSI are exactly this distinction, and naming it demonstrates real familiarity.

### Rebalancing

When shards are added:
- **`hash mod N` is the trap** — changing N remaps nearly every key. Never design for it.
- **Fixed partitions.** Create far more partitions than nodes (say 1,024 partitions across 10 nodes) and move whole partitions when rebalancing. Simple, predictable, and what Elasticsearch and Riak do.
- **Consistent hashing.** Only ~K/N keys move when a node joins or leaves (`08`).
- **Dynamic splitting.** Split a partition when it exceeds a size threshold, as HBase and MongoDB do. Adapts to skew automatically.

Rebalancing should be **throttled and preferably manual-triggered**: an automatic rebalance during a partial outage can mistake a slow node for a dead one and move terabytes at exactly the wrong moment.

### Routing

How does a request find its shard?

1. **Client-aware** — the client holds the partition map (Cassandra drivers do this). One fewer hop, but every client must be kept current.
2. **Routing tier** — a proxy holds the map (Vitess, a coordinator node). Clients stay simple.
3. **Any node routes** — send to any node, which forwards if needed (Cassandra's coordinator role).

The map itself is typically kept in a consensus store (ZooKeeper, etcd, Consul), which is the component that must not be forgotten when you draw the diagram.

---

## 3. Replication and partitioning together

Real systems combine them: the data is partitioned into shards, and **each shard is replicated**.

```
            Shard A               Shard B               Shard C
        ┌────────────┐        ┌────────────┐        ┌────────────┐
        │ leader A   │        │ leader B   │        │ leader C   │
        ├────────────┤        ├────────────┤        ├────────────┤
        │ follower   │        │ follower   │        │ follower   │
        │ follower   │        │ follower   │        │ follower   │
        └────────────┘        └────────────┘        └────────────┘
          keys 0-33             keys 34-66            keys 67-99
```

Two consequences worth stating:
- **A transaction spanning shards is a distributed transaction** — two-phase commit, or a saga with compensating actions. Design so the common case stays within one shard.
- **Leaders should be spread across machines**, not concentrated, or one machine carries all writes.

---

## 4. Recall questions

1. State precisely the difference between replication and partitioning, and what each scales.
2. Why is fully synchronous replication to all followers almost never used?
3. Name the three replication-lag anomalies and the fix for each.
4. What are the three failure modes of leader failover?
5. What is split brain and how is it prevented?
6. Name four multi-leader conflict-resolution strategies and the weakness of last-write-wins.
7. State the quorum condition and explain why it guarantees a fresh read.
8. What are read repair and anti-entropy?
9. What does hinted handoff buy and what does it weaken?
10. Compare range and hash partitioning on hot spots and range queries.
11. Give the three properties a good partition key must have.
12. Why must you never partition on a mutable attribute?
13. What is salting, and what does it cost on the read path?
14. Contrast local and global secondary indexes on a partitioned store.
15. Why is `hash mod N` a trap, and what are the three alternatives for rebalancing?
16. Where does the partition map live, and why is that component easy to forget?
