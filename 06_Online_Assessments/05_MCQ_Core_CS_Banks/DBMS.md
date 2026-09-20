
# DBMS — Theory and MCQ Bank

> **Why it matters.** DBMS is the most heavily weighted core-CS MCQ topic across product companies
> (Oracle especially) and appears in every service-company technical section. The questions are
> highly repetitive: normalisation, keys, joins, ACID, indexing and SQL semantics account for the
> overwhelming majority.

---

# Part 1 — Theory

## 1.1 Keys ⭐⭐⭐

| Key | Definition |
|---|---|
| **Super key** | Any set of attributes that uniquely identifies a row (may contain extras) |
| **Candidate key** | A **minimal** super key — remove any attribute and uniqueness is lost |
| **Primary key** | The candidate key chosen by the designer. **Unique + NOT NULL** ⭐ |
| **Alternate key** | The candidate keys not chosen as primary |
| **Foreign key** | An attribute referencing a primary key in another (or the same) table. **May be NULL**, and may repeat ⭐ |
| **Composite key** | A key made of two or more attributes |
| **Surrogate key** | An artificial key (auto-increment id) with no business meaning |

```
Relationship:   Primary key  ⊆  Candidate keys  ⊆  Super keys
```

⚠️ **The three most-tested distinctions:**
1. A primary key **cannot** be NULL; a **unique** constraint **can** permit one NULL (varies by DBMS).
2. A foreign key **can** be NULL and **can** have duplicates.
3. A candidate key is minimal; a super key need not be.

## 1.2 Normalisation ⭐⭐⭐

Normalisation removes redundancy and the update/insert/delete anomalies it causes.

| Form | Requirement | Removes |
|---|---|---|
| **1NF** | All attributes atomic; no repeating groups or multi-valued attributes | Multi-valued fields |
| **2NF** | 1NF **and** no **partial dependency** (no non-prime attribute depends on part of a composite key) ⭐ | Partial dependencies |
| **3NF** | 2NF **and** no **transitive dependency** (no non-prime attribute depends on another non-prime attribute) ⭐ | Transitive dependencies |
| **BCNF** | For every non-trivial FD `X → Y`, **X must be a super key** ⭐⭐ | Anomalies 3NF still allows |
| **4NF** | BCNF and no non-trivial **multi-valued dependency** | MVDs |
| **5NF** | 4NF and no join dependency | Join dependencies |

**Mnemonic for the first three ⭐:** *"The key, the whole key, and nothing but the key."*
- 1NF/2NF: depends on **the whole key** (no partial dependency)
- 3NF: depends on **nothing but the key** (no transitive dependency)

⚠️ **2NF only matters when the primary key is composite.** With a single-attribute key, a 1NF
relation is automatically in 2NF.

**Worked normalisation example:**
```
STUDENT(RollNo, CourseID, StudentName, CourseName, Instructor, InstructorPhone)
PK = (RollNo, CourseID)

1NF? Yes (assume atomic).

2NF? StudentName depends only on RollNo (part of the key) → PARTIAL dependency ✗
     CourseName depends only on CourseID → PARTIAL ✗
  Decompose:
     STUDENT(RollNo, StudentName)
     COURSE(CourseID, CourseName, Instructor, InstructorPhone)
     ENROLMENT(RollNo, CourseID)

3NF? In COURSE: CourseID → Instructor → InstructorPhone  → TRANSITIVE ✗
  Decompose:
     COURSE(CourseID, CourseName, Instructor)
     INSTRUCTOR(Instructor, InstructorPhone)
```

**3NF vs BCNF ⭐⭐:** 3NF permits `X → Y` where X is not a super key, **provided Y is a prime
attribute**. BCNF removes that exemption. Consequence: a decomposition into 3NF is always
dependency-preserving and lossless; a decomposition into BCNF is always lossless but **may not
preserve dependencies**. This trade-off is a standard exam question.

## 1.3 Joins ⭐⭐⭐

Given `A` with `m` rows and `B` with `n` rows:

| Join | Result |
|---|---|
| **CROSS JOIN** (Cartesian product) | `m × n` rows ⭐ |
| **INNER JOIN** | Only matching rows |
| **LEFT (OUTER) JOIN** | All of A + matching B; unmatched B columns are NULL |
| **RIGHT (OUTER) JOIN** | All of B + matching A |
| **FULL OUTER JOIN** | All rows of both; NULLs where unmatched |
| **SELF JOIN** | A table joined to itself (employee → manager) |
| **NATURAL JOIN** | Inner join on all **same-named** columns, with duplicates removed ⚠️ implicit and dangerous |
| **EQUI JOIN** | A join whose condition uses `=` only |

⚠️ **A LEFT JOIN returns at least `m` rows, and more if a left row matches several right rows.**
The common wrong answer is "exactly m".

## 1.4 SQL execution order ⭐⭐⭐

**Written order:**
```
SELECT … FROM … WHERE … GROUP BY … HAVING … ORDER BY … LIMIT
```
**Logical execution order:**
```
FROM → JOIN → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT
```

Three consequences that are asked constantly:
1. **`WHERE` cannot use an aggregate** (it runs before GROUP BY) — use `HAVING`. ⭐⭐
2. **`WHERE` cannot use a SELECT alias** in most DBMSs (the alias is created later);
   `ORDER BY` **can**, because it runs after SELECT. ⭐
3. Any non-aggregated column in the SELECT of a grouped query must appear in the `GROUP BY`.

## 1.5 ACID and transactions ⭐⭐⭐

| Property | Meaning | Enforced by |
|---|---|---|
| **A**tomicity | All or nothing | Transaction manager, undo log |
| **C**onsistency | Constraints hold before and after | Application + integrity constraints |
| **I**solation | Concurrent transactions do not interfere | Locking / MVCC |
| **D**urability | Committed changes survive a crash | Write-ahead log, stable storage |

**Isolation levels and the anomalies they permit ⭐⭐⭐** — this table is asked verbatim:

| Level | Dirty read | Non-repeatable read | Phantom read |
|---|---|---|---|
| READ UNCOMMITTED | ✅ possible | ✅ possible | ✅ possible |
| READ COMMITTED | ❌ prevented | ✅ possible | ✅ possible |
| REPEATABLE READ | ❌ | ❌ prevented | ✅ possible* |
| SERIALIZABLE | ❌ | ❌ | ❌ prevented |

`*` MySQL's InnoDB prevents phantoms at REPEATABLE READ via next-key locking — a well-known exception.

**The three anomalies:**
```
Dirty read          : reading data written by an UNCOMMITTED transaction
Non-repeatable read : re-reading the SAME ROW gives a different value (an UPDATE committed between)
Phantom read        : re-running the SAME QUERY returns a different SET of rows (an INSERT/DELETE)
```

## 1.6 Indexing ⭐⭐

| Aspect | Clustered index | Non-clustered index |
|---|---|---|
| Physical ordering | **Determines** the table's physical order | Separate structure with pointers |
| Number per table | **One** ⭐ | Many |
| Leaf node holds | The actual data rows | A pointer / the clustering key |
| Typical default | The primary key (in InnoDB, SQL Server) | Secondary indexes |

**B-tree vs hash index ⭐:**
```
B+ tree : supports =, <, >, BETWEEN, ORDER BY, prefix LIKE 'abc%'   → the default
Hash    : supports = only; O(1) average; no range queries            → niche
```

⚠️ **When an index is NOT used** (a favourite question):
- A function or arithmetic is applied to the column: `WHERE YEAR(dt) = 2025` ⚠️
- A leading wildcard: `WHERE name LIKE '%son'`
- Implicit type conversion between the column and the literal
- The query returns a large fraction of the table (a full scan is cheaper)
- `OR` across columns without a suitable composite index
- The **leftmost prefix rule** is violated for a composite index `(a, b, c)`: a query filtering only
  on `b` cannot use it ⭐⭐

**Index trade-off:** faster `SELECT`, slower `INSERT/UPDATE/DELETE` (every index must be
maintained), plus storage cost.

## 1.7 Other concepts worth a line each

```
VIEW              : a stored query. Updatable only under restrictions (single table, no aggregates,
                    no DISTINCT/GROUP BY). A MATERIALISED view stores the result physically.
TRIGGER           : procedural code fired automatically on INSERT/UPDATE/DELETE.
STORED PROCEDURE  : precompiled routine; reduces network round-trips; may have OUT parameters.
CURSOR            : row-by-row processing; slow, avoid where set-based SQL works.
DEADLOCK          : two transactions each holding a lock the other needs. Detected by a wait-for
                    graph cycle; resolved by aborting a victim. ⭐
2-PHASE LOCKING   : growing phase (acquire only) then shrinking phase (release only) ⇒ guarantees
                    serialisability. STRICT 2PL holds all exclusive locks until commit. ⭐
ER MODEL          : entity, attribute, relationship; cardinality 1:1, 1:N, M:N. An M:N relationship
                    requires a junction table. ⭐
DDL/DML/DCL/TCL   : CREATE-ALTER-DROP-TRUNCATE / SELECT-INSERT-UPDATE-DELETE /
                    GRANT-REVOKE / COMMIT-ROLLBACK-SAVEPOINT
DELETE vs TRUNCATE vs DROP ⭐⭐:
    DELETE   — DML, row-by-row, WHERE allowed, fires triggers, can be rolled back, keeps structure
    TRUNCATE — DDL, deallocates pages, no WHERE, no triggers, resets identity, usually not
               rollback-able, keeps structure
    DROP     — DDL, removes the table definition entirely
```

---

# Part 2 — MCQ Bank (20 questions with full explanations)

---

**Q1.** Which of the following is **not** a valid property of a primary key?
```
(a) It uniquely identifies each row
(b) It cannot contain NULL values
(c) A table can have multiple primary keys
(d) It can be composite
```
<details><summary>Answer: (c)</summary>

**(c) is false** and therefore the answer. A table has **exactly one** primary key, though that
key may be *composite* (made of several columns). Multiple **candidate** keys can exist; only one
is designated primary.

**Why the others are true:**
- **(a)** Uniqueness is the defining property.
- **(b)** The entity-integrity rule forbids NULL in a primary key, because NULL means "unknown" and
  an unknown value cannot identify a row.
- **(d)** `(OrderID, LineNo)` is a perfectly normal composite primary key.
</details>

---

**Q2.** A relation is in 2NF if it is in 1NF and:
```
(a) has no transitive dependency
(b) has no partial dependency on a composite key
(c) every determinant is a candidate key
(d) all attributes are atomic
```
<details><summary>Answer: (b)</summary>

**(b)** is the definition of 2NF: no non-prime attribute is functionally dependent on a *proper
subset* of a candidate key.

**Why not the others:**
- **(a)** describes **3NF**, the next form up.
- **(c)** describes **BCNF** — every determinant being a super key is exactly the BCNF condition.
- **(d)** describes **1NF**, which is the precondition, not the added requirement.

⭐ Remember: 2NF is only meaningful when the key is composite.
</details>

---

**Q3.** Consider `R(A, B, C, D)` with functional dependencies `A → B`, `B → C`, `C → D`. What is
the highest normal form of R if A is the only candidate key?
```
(a) 1NF        (b) 2NF        (c) 3NF        (d) BCNF
```
<details><summary>Answer: (b) 2NF</summary>

The key is `A` (single attribute), so there can be **no partial dependency** — R is automatically
in 2NF.

But `A → B → C` is a **transitive dependency**: `C` is a non-prime attribute determined by another
non-prime attribute `B`. That violates 3NF. So the highest normal form is **2NF**.

**Why not (c)/(d):** 3NF requires no transitive dependency on the key, which fails here. BCNF is
stronger still and also fails, since `B` is a determinant but not a super key.
**Why not (a):** 1NF is satisfied and so is 2NF, so 1NF is not the *highest*.
</details>

---

**Q4.** Table A has 10 rows and table B has 5 rows. `SELECT * FROM A CROSS JOIN B` returns:
```
(a) 15 rows    (b) 50 rows    (c) 10 rows    (d) 5 rows
```
<details><summary>Answer: (b) 50</summary>

A cross join is the Cartesian product: **every** row of A paired with **every** row of B, giving
`10 × 5 = 50` rows.

**Why the others are wrong:** (a) 15 would be a `UNION ALL`; (c) 10 would be a left join with at
most one match each; (d) 5 would be a right join with at most one match each.
</details>

---

**Q5.** Which clause filters rows **after** grouping and aggregation?
```
(a) WHERE      (b) HAVING     (c) GROUP BY    (d) ORDER BY
```
<details><summary>Answer: (b) HAVING</summary>

The logical order is `FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY`. `HAVING` therefore
sees the aggregated result and can filter on aggregates:
```sql
SELECT dept, COUNT(*) FROM emp GROUP BY dept HAVING COUNT(*) > 5;
```

**Why not (a):** `WHERE` runs **before** grouping, so it cannot reference an aggregate —
`WHERE COUNT(*) > 5` is a syntax error. ⭐ This is the single most-asked DBMS MCQ.
**Why not (c):** `GROUP BY` creates the groups; it does not filter.
**Why not (d):** `ORDER BY` sorts the final result.
</details>

---

**Q6.** Which anomaly does the **READ COMMITTED** isolation level prevent?
```
(a) Phantom read    (b) Non-repeatable read    (c) Dirty read    (d) All of these
```
<details><summary>Answer: (c) Dirty read</summary>

READ COMMITTED guarantees you only ever see **committed** data, which eliminates dirty reads.

**Why not (b):** between two reads of the same row within your transaction, another transaction can
commit an update — so non-repeatable reads are still possible.
**Why not (a):** a concurrent INSERT can add rows matching your predicate — phantoms are possible.
**Why not (d):** only SERIALIZABLE prevents all three.
</details>

---

**Q7.** In a transaction, which property guarantees that committed changes survive a system crash?
```
(a) Atomicity    (b) Consistency    (c) Isolation    (d) Durability
```
<details><summary>Answer: (d) Durability</summary>

Durability is implemented with a **write-ahead log** flushed to stable storage before commit is
acknowledged; recovery replays it after a crash.

**Why not (a):** atomicity is all-or-nothing *within* a transaction, enforced by undo.
**Why not (b):** consistency is about constraints remaining valid.
**Why not (c):** isolation concerns concurrent interference, not crash survival.
</details>

---

**Q8.** Which of the following can contain NULL values?
```
(a) Primary key    (b) Foreign key    (c) Both    (d) Neither
```
<details><summary>Answer: (b) Foreign key</summary>

A foreign key **may be NULL**, meaning "no related row yet" — e.g. an employee with no manager
assigned. It may also repeat (many employees, one department).

**Why not (a):** the entity-integrity rule forbids NULL in a primary key.
</details>

---

**Q9.** `DELETE FROM employees;` versus `TRUNCATE TABLE employees;` — which statement is true?
```
(a) Both can be rolled back in all DBMSs
(b) TRUNCATE is DML, DELETE is DDL
(c) TRUNCATE is generally faster and does not fire row-level triggers
(d) DELETE removes the table structure
```
<details><summary>Answer: (c)</summary>

`TRUNCATE` deallocates whole data pages rather than logging each row deletion, so it is far faster,
and because it does not process rows individually it does not fire row-level triggers. It also
typically resets the identity/auto-increment counter.

**Why not (a):** `TRUNCATE` is DDL and in many DBMSs (notably Oracle) performs an implicit commit
and cannot be rolled back. (PostgreSQL is a transactional exception.)
**Why not (b):** it is the reverse — DELETE is DML, TRUNCATE is DDL.
**Why not (d):** neither removes the structure; **DROP** does.
</details>

---

**Q10.** Which index type cannot efficiently support a range query such as `WHERE age BETWEEN 20
AND 30`?
```
(a) B-tree    (b) B+ tree    (c) Hash    (d) Clustered B+ tree
```
<details><summary>Answer: (c) Hash</summary>

A hash index maps a key to a bucket via a hash function, which **destroys ordering**. It supports
equality lookups in O(1) average time but cannot answer range or `ORDER BY` queries.

**Why the others work:** B-tree and B+ tree keep keys in sorted order, and a B+ tree additionally
links its leaf nodes, making range scans especially efficient — which is why B+ trees are the
standard database index. ⭐
</details>

---

**Q11.** A relation is in BCNF if:
```
(a) it is in 3NF and has no multi-valued dependency
(b) every non-prime attribute is fully dependent on the key
(c) for every non-trivial FD X → Y, X is a super key
(d) it has no transitive dependencies
```
<details><summary>Answer: (c)</summary>

That is the definition of BCNF, and it is strictly stronger than 3NF because 3NF exempts the case
where `Y` is a prime attribute.

**Why not (a):** removing multi-valued dependencies is **4NF**.
**Why not (b):** that is 2NF.
**Why not (d):** that is 3NF.

⭐ Follow-up worth knowing: a BCNF decomposition is always lossless but **may not preserve all
functional dependencies**, whereas a 3NF decomposition can always be both lossless and
dependency-preserving.
</details>

---

**Q12.** Two transactions T1 and T2 each hold a lock the other requires. This is:
```
(a) starvation    (b) deadlock    (c) livelock    (d) a phantom read
```
<details><summary>Answer: (b) deadlock</summary>

Deadlock requires all four Coffman conditions: mutual exclusion, hold-and-wait, no preemption and
circular wait. The DBMS detects it by finding a cycle in the **wait-for graph** and aborts a victim
transaction.

**Why not (a):** starvation is indefinite postponement of one transaction while others proceed —
there is no cycle.
**Why not (c):** in livelock the transactions keep changing state but make no progress; they are
not blocked.
**Why not (d):** a phantom read is a concurrency *anomaly*, not a blocking condition.
</details>

---

**Q13.** Which normal form deals with multi-valued dependencies?
```
(a) 2NF    (b) 3NF    (c) BCNF    (d) 4NF
```
<details><summary>Answer: (d) 4NF</summary>

4NF requires that for every non-trivial multi-valued dependency `X ↠ Y`, X is a super key. The
classic violation: a table storing a person's *skills* and their *languages* together produces a
Cartesian explosion of rows; splitting into two tables fixes it.

(2NF handles partial, 3NF transitive, BCNF determinant-not-super-key. 5NF handles join dependencies.)
</details>

---

**Q14.** `SELECT COUNT(*)` versus `SELECT COUNT(column)` on a column containing NULLs:
```
(a) Both return the same value
(b) COUNT(*) counts all rows; COUNT(column) skips NULLs
(c) COUNT(*) skips NULLs
(d) COUNT(column) is always faster
```
<details><summary>Answer: (b)</summary>

`COUNT(*)` counts **rows**, including those where every column is NULL. `COUNT(column)` counts
**non-NULL values** in that column.

⭐ The same principle applies to every aggregate: `SUM`, `AVG`, `MIN` and `MAX` all **ignore NULLs**.
That is why `AVG(col)` over `{10, 20, NULL}` is `15`, not `10`.

**Why not (d):** `COUNT(*)` is typically as fast or faster, since it can use the smallest available
index without reading the column.
</details>

---

**Q15.** Which is **not** a type of join?
```
(a) Inner join    (b) Outer join    (c) Cross join    (d) Parallel join
```
<details><summary>Answer: (d)</summary>

"Parallel join" is not a relational join type (it describes an *execution* strategy). The relational
join types are inner, outer (left/right/full), cross, natural, self and equi/theta joins.
</details>

---

**Q16.** In the ER model, an M:N relationship between two entities is implemented in a relational
schema by:
```
(a) adding a foreign key to either table
(b) creating a separate junction/bridge table
(c) merging the two tables
(d) using a composite primary key in one of the tables
```
<details><summary>Answer: (b)</summary>

An M:N relationship cannot be represented with a single foreign key column, because a column holds
one value. The standard solution is a **junction table** whose primary key is the composite of the
two foreign keys:
```sql
CREATE TABLE student_course (
    student_id INT REFERENCES student(id),
    course_id  INT REFERENCES course(id),
    PRIMARY KEY (student_id, course_id)
);
```
**Why not (a):** that works only for **1:N** (put the FK on the "many" side).
</details>

---

**Q17.** Which SQL statement is used to remove a table's structure **and** its data?
```
(a) DELETE    (b) TRUNCATE    (c) DROP    (d) REMOVE
```
<details><summary>Answer: (c) DROP</summary>

`DROP TABLE` removes the definition, the data, the indexes, the triggers and the constraints.
`DELETE` removes rows only; `TRUNCATE` removes all rows but keeps the structure; `REMOVE` is not
SQL.
</details>

---

**Q18.** Consider a composite index on `(last_name, first_name, city)`. Which query can use it?
```
(a) WHERE first_name = 'Ravi'
(b) WHERE city = 'Chennai'
(c) WHERE last_name = 'Kumar' AND first_name = 'Ravi'
(d) WHERE first_name = 'Ravi' AND city = 'Chennai'
```
<details><summary>Answer: (c)</summary>

The **leftmost prefix rule**: a composite index on `(a, b, c)` can serve queries filtering on
`a`, on `(a,b)`, or on `(a,b,c)` — but not on `b` or `c` alone.

**(c)** filters on `last_name` and `first_name`, which is the prefix `(a, b)` ✓.
**(a), (b), (d)** all skip the leading column `last_name`, so the index cannot be used for seeking
(at best a full index scan). ⭐⭐ This is a standard Oracle/SQL-performance interview question.
</details>

---

**Q19.** Which of the following is a **DCL** command?
```
(a) COMMIT    (b) GRANT    (c) UPDATE    (d) ALTER
```
<details><summary>Answer: (b) GRANT</summary>

```
DDL (Data Definition)   : CREATE, ALTER, DROP, TRUNCATE, RENAME
DML (Data Manipulation) : SELECT*, INSERT, UPDATE, DELETE, MERGE
DCL (Data Control)      : GRANT, REVOKE
TCL (Transaction Control): COMMIT, ROLLBACK, SAVEPOINT, SET TRANSACTION
```
(*Some classifications place SELECT in a separate DQL category.)

**(a)** is TCL, **(c)** is DML, **(d)** is DDL.
</details>

---

**Q20.** Given `Employee(id, name, salary, dept_id)`, which query correctly finds the
**second-highest** salary?
```
(a) SELECT MAX(salary) FROM Employee WHERE salary < MAX(salary)
(b) SELECT MAX(salary) FROM Employee WHERE salary < (SELECT MAX(salary) FROM Employee)
(c) SELECT salary FROM Employee ORDER BY salary DESC LIMIT 2
(d) SELECT TOP 2 salary FROM Employee
```
<details><summary>Answer: (b)</summary>

**(b)** is the classic correct form: find the maximum among the salaries strictly below the overall
maximum. It also handles duplicates correctly (if three people earn the top salary, it still
returns the genuine second-highest *distinct* value).

**Why not (a):** an aggregate cannot appear in `WHERE` — `WHERE salary < MAX(salary)` is a syntax
error. ⚠️ This is the planted trap, and it is the same rule as Q5.
**Why not (c)/(d):** these return **two rows** (the top two salaries), not the second-highest value.

⭐ Two other correct solutions worth knowing:
```sql
-- Window function (modern, handles ties explicitly)
SELECT DISTINCT salary FROM (
  SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) rnk FROM Employee
) t WHERE rnk = 2;

-- OFFSET form
SELECT DISTINCT salary FROM Employee ORDER BY salary DESC LIMIT 1 OFFSET 1;
```
Note `DENSE_RANK` vs `RANK`: with salaries `{100, 100, 90}`, `DENSE_RANK` gives the second-highest
as 90, while `RANK` skips rank 2 entirely. Interviewers ask this follow-up. ⭐⭐
</details>

---

## Scoring

| Correct | Read as |
|---|---|
| 18-20 | DBMS is interview-ready |
| 14-17 | Revisit normalisation and isolation levels |
| 10-13 | Re-read Part 1 and redo the bank |
| < 10 | One full day here; DBMS is the highest-frequency core-CS topic |

---

## Recall questions

1. Order super key, candidate key and primary key by containment.
2. Give the "key, whole key, nothing but the key" mnemonic and map it to 1NF/2NF/3NF.
3. State the BCNF condition and how it differs from 3NF.
4. Write the logical execution order of a SQL query and give two consequences.
5. Complete the isolation-level/anomaly table from memory.
6. Compare DELETE, TRUNCATE and DROP on five dimensions.
7. State the leftmost prefix rule and give a query that violates it.
8. Name five situations in which an index will not be used.
9. How is an M:N relationship implemented, and how is 1:N different?
10. Give three correct ways to find the second-highest salary.
