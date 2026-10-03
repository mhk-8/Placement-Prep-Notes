
# DBMS — One Pager

> **Use:** every core-CS round; especially Oracle, Arcesium, Goldman Sachs, Flipkart.
> SQL syntax lives in `sql-one-pager.md` — this sheet is the theory. ⭐

---

## 1. ACID ⭐⭐⭐

| Property | Means | Enforced by |
|---|---|---|
| **Atomicity** | All or nothing | Undo log / rollback |
| **Consistency** | Constraints hold before and after | Integrity constraints, triggers |
| **Isolation** | Concurrent transactions appear serial | Locking / MVCC |
| **Durability** | Committed survives a crash | **WAL** + fsync ⭐ |

**WAL (write-ahead logging):** write the log record *before* the data page, flush the log on
commit. Recovery = redo committed, undo uncommitted. The ARIES protocol: analysis → redo → undo.

---

## 2. Isolation levels and anomalies ⭐⭐⭐

| Level | Dirty read | Non-repeatable read | Phantom |
|---|---|---|---|
| READ UNCOMMITTED | ✅ | ✅ | ✅ |
| READ COMMITTED | ❌ | ✅ | ✅ |
| REPEATABLE READ | ❌ | ❌ | ✅* |
| SERIALIZABLE | ❌ | ❌ | ❌ |

\*MySQL InnoDB's REPEATABLE READ blocks phantoms using **next-key / gap locks**.

```
Dirty read          : read uncommitted data from another transaction
Non-repeatable read : re-reading the same ROW gives a different value
Phantom read        : re-running the same RANGE query returns a different SET of rows ⭐
Lost update         : two read-modify-writes, one overwrites the other
Write skew          : two transactions each read, then write, breaking a joint constraint
                      (only SERIALIZABLE/SSI prevents it) ⭐ good answer to offer
```
Defaults: PostgreSQL, Oracle, SQL Server → READ COMMITTED. MySQL InnoDB → REPEATABLE READ.

---

## 3. Concurrency control ⭐⭐

| Mechanism | How |
|---|---|
| **2PL** | Growing phase acquires locks, shrinking phase releases. Serialisable but deadlock-prone |
| **Strict 2PL** | Hold all exclusive locks until commit → avoids cascading aborts ⭐ what engines use |
| Lock modes | Shared (read) / Exclusive (write); intention locks for hierarchies |
| **MVCC** | Readers see a snapshot; writers create new versions → **readers never block writers** ⭐ Postgres, InnoDB, Oracle |
| Timestamp ordering | Serialise by timestamp; abort out-of-order accesses |
| Optimistic (OCC) | Execute, then validate at commit; good for low contention |
| Deadlock handling | Wait-for graph detection, or timeout and abort the younger transaction |

**Conflict serialisability:** a schedule is serialisable if its precedence graph (edges for
conflicting read/write pairs on the same item) is **acyclic**. 📐 This is the test to state.
**Recoverable schedule:** no transaction commits before one it read from.
**Cascadeless:** reads only committed data.

---

## 4. Normalisation ⭐⭐

| Form | Requirement |
|---|---|
| **1NF** | Atomic values, no repeating groups |
| **2NF** | 1NF + no **partial** dependency (non-key attribute on part of a composite key) |
| **3NF** | 2NF + no **transitive** dependency (non-key → non-key) |
| **BCNF** | Every determinant is a superkey ⭐ strictly stronger than 3NF |
| 4NF | No non-trivial multivalued dependency |
| 5NF | No join dependency not implied by candidate keys |

```
Functional dependency X → Y : X determines Y.
Armstrong's axioms: reflexivity · augmentation · transitivity.
Closure X⁺ : everything functionally determined by X. A key is an X with X⁺ = all attributes.
Candidate key : minimal superkey.  Prime attribute : part of some candidate key.
Decomposition must be LOSSLESS (common attributes form a superkey of one relation) and ideally
DEPENDENCY-PRESERVING. ⚠️ BCNF may not be dependency-preserving; 3NF always can be. ⭐
```

> **Why denormalise:** fewer joins on read-heavy workloads, at the cost of update anomalies and
> storage. Say the trade-off explicitly rather than treating normalisation as always correct.

---

## 5. Keys and constraints

```
Super key       : any set that uniquely identifies a row
Candidate key   : minimal super key
Primary key     : the chosen candidate key — NOT NULL + UNIQUE, one per table
Alternate key   : a candidate key not chosen
Composite key   : multiple columns
Foreign key     : references another table's PK/unique → referential integrity
Surrogate key   : artificial (auto-increment/UUID) vs natural key
ON DELETE CASCADE | SET NULL | RESTRICT | NO ACTION
CHECK · NOT NULL · UNIQUE (⚠️ permits multiple NULLs) · DEFAULT
```

---

## 6. Indexing and storage ⭐⭐

| Topic | Key point |
|---|---|
| **B⁺-tree** | All data in leaves, leaves linked → **range scans**; internal nodes are keys only ⭐ |
| Why B⁺ over B | Higher fanout (no data in internal nodes) → shallower tree → fewer disk I/Os |
| Why B⁺ over hash | Hash gives O(1) equality but no ordering or range queries |
| Why B⁺ over BST | Node = disk page; fanout of hundreds → height ~3-4 for millions of rows ⭐ |
| **Clustered index** | Determines physical row order; one per table (InnoDB: the PK) |
| Secondary index | Key → PK (InnoDB) or row-id; may need a second lookup |
| **Covering index** | Query answered from the index alone — no table access ⭐ |
| Composite index | **Leftmost-prefix rule**: `(a,b,c)` serves `a`, `a,b`, `a,b,c`, not `b` alone ⚠️ |
| Dense vs sparse | Entry per record vs per block |
| Index selectivity | High-cardinality columns benefit; a boolean flag usually does not |
| Hash index | Equality only; used for in-memory tables |
| Bitmap index | Low-cardinality columns in analytics warehouses |
| **LSM-tree** | Write-optimised: memtable → SSTables → compaction. RocksDB, Cassandra ⭐ contrast with B⁺-tree's read optimisation |
| Row vs column store | OLTP point access vs OLAP scans + compression ⭐ |

### What prevents index use ⚠️
```
WHERE YEAR(created) = 2024          → wrap the range instead: created >= '2024-01-01' AND < '2025-01-01'
WHERE name LIKE '%smith'            → leading wildcard kills the B-tree
WHERE int_col = '42'                → implicit cast
OR across different columns         → may force a scan; consider UNION
Very low selectivity                → the optimiser prefers a full scan on purpose
```

---

## 7. Query processing ⭐

```
SQL → parse → rewrite → OPTIMISE (cost-based, using statistics) → physical plan → execute
```

| Join algorithm | Cost | When |
|---|---|---|
| Nested loop | O(n·m) | Small inner relation, or an index on the inner join key (index nested loop) |
| **Hash join** | O(n+m) | Equi-joins, no useful index, enough memory ⭐ |
| **Sort-merge join** | O(n log n + m log m) | Inputs already sorted, or non-equi range joins |

```
Selection/projection pushdown · join reordering · predicate simplification
Statistics: cardinality, histograms, distinct counts. Stale stats → bad plans ⚠️
EXPLAIN / EXPLAIN ANALYZE: read actual vs estimated rows. A 100x mis-estimate is the usual culprit
```
> ⭐ **Your pitch to Oracle:** query optimisation and compiler optimisation are the same problem
> shape — a cost model over a space of semantically-equivalent plans. See
> `../08_Company_Wise/Oracle/00-process-and-oa.md`.

---

## 8. Distributed and NoSQL ⭐

```
CAP: under a network Partition you choose Consistency or Availability. ⚠️ Not "pick 2 of 3" —
     P is not optional in a distributed system. CP: HBase, etcd. AP: Cassandra, Dynamo. ⭐
PACELC: else (no partition) choose Latency or Consistency.
BASE: Basically Available, Soft state, Eventual consistency.
```

| Technique | Purpose |
|---|---|
| **Sharding** | Horizontal partition by key. Range · hash · directory. ⚠️ hot keys, resharding |
| Consistent hashing | Adding a node moves only 1/n of keys; virtual nodes for balance ⭐ |
| Replication | Leader-follower (sync/async) · multi-leader · leaderless (quorum) |
| Quorum | `R + W > N` gives strong consistency |
| 2PC | Prepare → commit; blocking if the coordinator dies ⚠️ |
| Saga | Chain of local transactions with compensations — microservices alternative to 2PC ⭐ |
| Partition vs replication | Scale capacity vs scale reads + fault tolerance |

| NoSQL family | Example | Fits |
|---|---|---|
| Key-value | Redis, DynamoDB | Caching, sessions |
| Document | MongoDB | Flexible schema, nested objects |
| Wide-column | Cassandra, HBase | Huge write throughput, time series |
| Graph | Neo4j | Relationship traversal |

---

## 9. Transactions in practice

```
BEGIN; ... COMMIT / ROLLBACK;  SAVEPOINT s1; ROLLBACK TO s1;
SELECT ... FOR UPDATE          -- pessimistic row lock ⭐
Optimistic locking             -- version column; UPDATE ... WHERE version = ?
Idempotency                    -- retry safety; essential with at-least-once delivery
```

---

## Recall questions
1. Name the three read anomalies and the isolation level that first prevents each.
2. Why B⁺-tree rather than a hash index or a balanced BST for a database index?
3. What is the leftmost-prefix rule? Give a query that an index on `(a,b,c)` does not help.
4. BCNF vs 3NF — which can fail to preserve dependencies, and why does that matter?
5. State the test for conflict serialisability.
6. What does MVCC buy you over pure 2PL?
7. Explain CAP without saying "pick two of three".
8. Why is a B⁺-tree read-optimised and an LSM-tree write-optimised?
