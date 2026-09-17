# DBMS & SQL — Concepts

## 1. Core idea in 3 lines
A DBMS exists to let many users query and modify shared, persistent data **correctly and concurrently**. Almost everything in this folder is one of two answers: how do we structure data so it cannot become inconsistent (keys, normalisation), and how do we let concurrent transactions interleave without corrupting it (ACID, isolation, locking). SQL is the interface, and it is the part tested directly in OAs.

---

## 2. The relational model

A **relation** is a set of tuples over named attributes. "Set" implies no duplicates and no ordering — SQL relaxes this to a multiset, which is why `DISTINCT` exists.

| Term | Meaning |
|---|---|
| **Super key** | Any attribute set that uniquely identifies a tuple |
| **Candidate key** | A *minimal* super key — remove any attribute and it stops being unique |
| **Primary key** | The chosen candidate key; implicitly `NOT NULL UNIQUE` |
| **Alternate key** | Candidate keys not chosen as primary |
| **Composite key** | A key spanning more than one attribute |
| **Foreign key** | An attribute set referencing another relation's key |
| **Prime attribute** | An attribute belonging to *some* candidate key |

**Integrity constraints:** *entity integrity* (no part of a primary key may be NULL) and *referential integrity* (a foreign key must match some existing primary-key value, or be NULL). On delete, the options are `CASCADE`, `SET NULL`, `SET DEFAULT`, `RESTRICT` / `NO ACTION`.

### ER model → relations
- **Strong entity** → its own table, primary key = the entity's key.
- **Weak entity** → its own table with the owner's key as part of a composite primary key; the relationship is total and identifying.
- **1:1** → merge into either table, or keep the foreign key on the side with total participation.
- **1:N** → put the foreign key on the **N** side. Never create a separate table for a plain 1:N.
- **M:N** → a separate junction table whose primary key is the pair of foreign keys.
- **Multivalued attribute** → its own table.
- **Generalisation/specialisation** → one table per subclass, or one table with a discriminator, depending on overlap and completeness.

---

## 3. Functional dependencies and normalisation

**X → Y** means: any two tuples agreeing on X must agree on Y.

**Armstrong's axioms:** reflexivity (Y ⊆ X ⇒ X → Y), augmentation (X → Y ⇒ XZ → YZ), transitivity (X → Y, Y → Z ⇒ X → Z). Derived: union, decomposition, pseudo-transitivity.

**Attribute closure X⁺** is the set of attributes determined by X. Compute it by repeatedly adding the right-hand side of any FD whose left-hand side is already inside. **X is a super key iff X⁺ = all attributes.** This one procedure answers almost every FD question in an OA.

**Finding candidate keys quickly:**
1. Attributes appearing **only on the left** of any FD must be in *every* key.
2. Attributes appearing **only on the right** are in *no* key.
3. Attributes appearing on neither side must be in every key.
4. Start from the mandatory set, compute its closure, and add attributes until the closure is everything; then check minimality.

### The normal forms

| Form | Requirement | Removes |
|---|---|---|
| **1NF** | Atomic values; no repeating groups | multivalued cells |
| **2NF** | 1NF + no **partial** dependency (a non-prime attribute depending on part of a composite key) | partial dependencies |
| **3NF** | 2NF + no **transitive** dependency: for every X → Y, either X is a super key or Y is prime | transitive dependencies |
| **BCNF** | For every non-trivial X → Y, **X is a super key** | all remaining anomalies |
| **4NF** | BCNF + no non-trivial multivalued dependency unless X is a super key | MVD redundancy |

**3NF vs BCNF** is the standard interview probe. Every BCNF relation is in 3NF, not conversely. The canonical counterexample: R(A, B, C) with F = {AB → C, C → A}. Candidate keys are AB and BC, so every attribute is prime; 3NF is satisfied because C → A has a prime right-hand side. But C is not a super key, so BCNF is violated.

**The trade-off:** a 3NF decomposition can always be made both lossless and **dependency-preserving**; a BCNF decomposition is always lossless but may **lose a dependency**. That is why 3NF is what real schemas usually target.

**Lossless join test** for a decomposition into R1 and R2: the join is lossless iff `(R1 ∩ R2) → R1` or `(R1 ∩ R2) → R2`, i.e. the common attributes form a key of at least one fragment.

**Denormalisation** is deliberate: duplicate data to avoid joins when reads vastly outnumber writes. Say it as a conscious trade-off (read speed for write complexity and redundancy risk), not as sloppiness.

---

## 4. SQL

**Logical execution order** — this is the single most useful fact for writing and debugging queries:

```
FROM → JOIN → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT
```

It explains why a `SELECT` alias cannot be used in `WHERE` (the alias does not exist yet) but *can* be used in `ORDER BY`, and why aggregates are filtered in `HAVING` rather than `WHERE`.

### Joins

| Join | Returns |
|---|---|
| `INNER` | rows matching in both |
| `LEFT` | all left rows; NULLs where no match |
| `RIGHT` | all right rows; NULLs where no match |
| `FULL OUTER` | all rows from both |
| `CROSS` | Cartesian product |
| `SELF` | a table joined to itself (manager hierarchies, consecutive rows) |

**Anti-join** — rows in A with no match in B — is written as `LEFT JOIN … WHERE b.id IS NULL`, or `NOT EXISTS`. Avoid `NOT IN` when the subquery can return NULL, because `x NOT IN (1, NULL)` evaluates to UNKNOWN and returns *nothing*.

### Aggregation
`COUNT(*)` counts rows including NULLs; `COUNT(col)` skips NULLs. Every other aggregate ignores NULLs. `WHERE` filters rows before grouping; `HAVING` filters groups after.

### Window functions
`func() OVER (PARTITION BY … ORDER BY … [frame])` computes a value per row **without collapsing rows** — the key difference from `GROUP BY`.

| Function | Behaviour on ties |
|---|---|
| `ROW_NUMBER()` | 1, 2, 3, 4 — arbitrary tiebreak |
| `RANK()` | 1, 2, 2, 4 — gaps after ties |
| `DENSE_RANK()` | 1, 2, 2, 3 — no gaps |
| `LAG/LEAD(col, n)` | previous/next row's value |
| `SUM() OVER (ORDER BY …)` | running total |
| `NTILE(n)` | bucket number |

### Subqueries
**Uncorrelated** subqueries run once; **correlated** subqueries reference the outer query and conceptually run per outer row — usually rewritable as a join or a window function, which is what an interviewer wants to see. `EXISTS` short-circuits on the first match and is generally preferable to `IN` on large subqueries.

### NULL semantics — the biggest source of wrong answers
SQL uses three-valued logic: TRUE, FALSE, UNKNOWN. `NULL = NULL` is UNKNOWN, not TRUE. Use `IS NULL`. `WHERE` keeps only TRUE rows, so UNKNOWN behaves like FALSE there. `NULL + 5` is NULL. Aggregates skip NULLs, so `AVG` over a column with NULLs divides by the non-null count. `GROUP BY` treats all NULLs as one group, and `UNIQUE` permits multiple NULLs in most engines — two places where NULLs are treated as *equal* despite the comparison rule.

---

## 5. Indexes

An index is an auxiliary structure mapping key values to row locations.

**B+ tree** is the default: all data pointers live in the leaves, the leaves are linked for range scans, and the tree stays balanced with height ~log_b(n), typically 3–4 levels for millions of rows. It supports equality **and** range queries and ordered traversal.

**Hash index** gives O(1) equality lookup and is useless for ranges or ordering.

**Clustered index** determines the *physical* order of rows, so there can be only one per table; the leaf *is* the row. **Non-clustered** indexes store a pointer to the row, so using one costs an extra lookup unless the index is **covering** (it contains every column the query needs).

**When an index hurts:** every insert, update and delete must maintain it, so write-heavy tables pay. A low-selectivity index (a `gender` column) is usually ignored by the optimiser because a full scan is cheaper than many random row lookups. Indexes also consume space.

**When an index is not used even though it exists:** a function or arithmetic applied to the indexed column (`WHERE YEAR(dt) = 2026`), a leading wildcard (`LIKE '%abc'`), an implicit type conversion, or skipping the leading column of a composite index. Composite indexes are usable left-to-right only — an index on `(a, b, c)` serves `a`, `a,b` and `a,b,c`, but not `b` alone.

---

## 6. Transactions and concurrency

**ACID:**
- **Atomicity** — all or nothing (undo log)
- **Consistency** — constraints hold before and after
- **Isolation** — concurrent execution is equivalent to some serial order
- **Durability** — committed changes survive a crash (redo log, write-ahead logging)

**The anomalies, in increasing order of subtlety:**

| Anomaly | What happens |
|---|---|
| **Dirty read** | T1 reads a value T2 wrote but has not committed |
| **Non-repeatable read** | T1 reads a row twice and gets different values because T2 updated it between |
| **Phantom read** | T1 runs the same *range* query twice and sees new rows T2 inserted |
| Lost update | Two transactions read-modify-write the same value; one is overwritten |

| Isolation level | Dirty | Non-repeatable | Phantom |
|---|---|---|---|
| READ UNCOMMITTED | possible | possible | possible |
| READ COMMITTED | prevented | possible | possible |
| REPEATABLE READ | prevented | prevented | possible* |
| SERIALIZABLE | prevented | prevented | prevented |

*MySQL's InnoDB prevents phantoms at REPEATABLE READ using next-key locks, which is a common "gotcha" question.*

**Concurrency control:**
- **Two-phase locking (2PL):** a growing phase acquires locks, a shrinking phase releases them, and no lock is acquired after the first release. 2PL guarantees serialisability but can deadlock. **Strict 2PL** holds all exclusive locks until commit, which also prevents cascading aborts.
- **Timestamp ordering:** each transaction gets a timestamp; conflicting operations must follow timestamp order, else the transaction is aborted. Deadlock-free but can cause starvation.
- **MVCC:** readers see a consistent snapshot rather than blocking on writers. This is why PostgreSQL and InnoDB readers do not block writers, and it is the answer to "how do modern databases avoid read locks".

**Schedules:** a schedule is *conflict-serialisable* if its precedence graph is acyclic. Conflicts are read–write, write–read and write–write on the same item; two reads never conflict.

---

## 7. SQL vs NoSQL, and CAP

**Relational** databases give a fixed schema, joins, and strong transactional guarantees — the right default whenever the data is relational and consistency matters.

**NoSQL** families: key-value (Redis, DynamoDB), document (MongoDB), wide-column (Cassandra, HBase), graph (Neo4j). They trade joins and sometimes consistency for horizontal scalability and schema flexibility.

**CAP theorem:** under a network **partition**, a distributed system must choose between **consistency** and **availability**. It says nothing about the partition-free case, which is where most people misquote it. **PACELC** extends it: else (no partition), choose between **latency** and **consistency**.

---

## 8. Recall questions

1. Give the four steps for finding candidate keys from a set of FDs.
2. How do you compute an attribute closure, and what does X⁺ = R tell you?
3. State 2NF, 3NF and BCNF precisely.
4. Give a relation in 3NF but not BCNF and explain why.
5. What can a 3NF decomposition guarantee that a BCNF decomposition cannot?
6. State the lossless-join condition for a two-way decomposition.
7. Write the SQL logical execution order and explain what it predicts about aliases.
8. Why is `NOT IN` dangerous with NULLs?
9. Distinguish `RANK`, `DENSE_RANK` and `ROW_NUMBER`.
10. Name five reasons an existing index is not used.
11. Define the three read anomalies and the level that prevents each.
12. What does 2PL guarantee and what does it not prevent?
13. What does MVCC buy you?
14. State CAP precisely, including what it does *not* claim.
