# DBMS & SQL — Solved OA Questions

> 25 questions in OA style, with every option explained. DBMS carries more OA marks than any other core-CS subject, and the failure modes are unusually predictable — NULL handling, 3NF vs BCNF, and `WHERE` vs `HAVING` account for a large share of lost marks.
> Cover the answers. **45 seconds** for conceptual, **2 minutes** for FD and query questions.

---

## Set A — Keys and normalisation

**Q1.** R(A, B, C, D) with F = {AB→C, C→D, D→A}. How many candidate keys does R have?
(a) 1  (b) 2  (c) 3  (d) 4

<details><summary>Answer</summary>

**(c) 3.**

B appears only on the left of any FD, so **B is in every candidate key**. Now grow from B:
```
B+   = {B}                                          not a key
AB+  = {A,B} → C → {A,B,C} → D → all                ✔ key
BC+  = {B,C} → D → {B,C,D} → A → all                ✔ key
BD+  = {B,D} → A → {A,B,D} → C → all                ✔ key
```
Each is minimal (B alone fails, and A, C, D alone all fail since their closures omit B).

**Candidate keys: AB, BC, BD.**

**Method to reuse:** identify attributes appearing only on the left — they are mandatory — then extend that mandatory set one attribute at a time.
</details>

---

**Q2.** Which normal form removes **transitive** dependencies of non-prime attributes on the key?
(a) 1NF  (b) 2NF  (c) 3NF  (d) BCNF

<details><summary>Answer</summary>

**(c) 3NF.**

1NF removes repeating groups. 2NF removes **partial** dependencies (a non-prime attribute depending on part of a composite key). 3NF removes **transitive** ones. BCNF goes further and requires *every* determinant to be a super key, including cases where the dependent attribute is prime.
</details>

---

**Q3.** R(A, B, C) with F = {AB→C, C→A}. R is in
(a) 1NF only  (b) 2NF but not 3NF  (c) 3NF but not BCNF  (d) BCNF

<details><summary>Answer</summary>

**(c) 3NF but not BCNF.**

Candidate keys are **AB** and **BC** (check: (AB)⁺ = ABC, (BC)⁺ = BCA), so **every attribute is prime**.

- 2NF ✔ — there are no non-prime attributes at all, so no partial dependency is possible.
- 3NF ✔ — `AB→C` has a super-key left side; `C→A` has a non-super-key left side but a **prime** right side, which 3NF permits.
- BCNF ✘ — `C→A` is non-trivial and C⁺ = {C, A} ≠ R, so C is not a super key.

**This is *the* canonical 3NF-not-BCNF relation.** If you memorise one example in this whole folder, make it this one.
</details>

---

**Q4.** Which statement about BCNF decomposition is true?
(a) It is always lossless and always dependency-preserving
(b) It is always lossless but may not be dependency-preserving
(c) It may be lossy but is always dependency-preserving
(d) It is neither guaranteed

<details><summary>Answer</summary>

**(b).**

A BCNF decomposition can always be made lossless, but some dependencies may end up split across fragments where they cannot be enforced. **3NF** is the form that guarantees *both* lossless join and dependency preservation — which is precisely why production schemas usually target 3NF rather than BCNF.
</details>

---

**Q5.** R(A,B,C,D) with F = {A→B, B→C, C→D}. Is the decomposition R1(A,B), R2(B,C), R3(C,D) lossless?
(a) Yes  (b) No  (c) Only if A is a key  (d) Cannot be determined

<details><summary>Answer</summary>

**(a) Yes.**

Apply the two-way test repeatedly. Join R2 and R3 first: R2 ∩ R3 = {C}, and C⁺ = {C, D} ⊇ R3 ✔ → lossless, giving R23(B,C,D). Now R1 ∩ R23 = {B}, and B⁺ = {B, C, D} ⊇ R23 ✔ → lossless.

**General rule for a chain decomposition along a chain of FDs**: it is always lossless, because each shared attribute determines everything to its right.
</details>

---

## Set B — SQL semantics

**Q6.** Table `T` has 5 rows; the column `c` holds `1, 2, NULL, NULL, 5`. What do `COUNT(*)`, `COUNT(c)` and `AVG(c)` return?
(a) 5, 5, 1.6  (b) 5, 3, 2.67  (c) 3, 3, 2.67  (d) 5, 3, 1.6

<details><summary>Answer</summary>

**(b) 5, 3, 2.67.**

`COUNT(*)` counts **rows**, NULLs included → 5.
`COUNT(c)` counts **non-NULL values** → 3.
`AVG(c)` = (1 + 2 + 5) / **3** = 8/3 = 2.67 — aggregates both skip NULLs *and* divide by the non-null count.

(a) and (d) come from dividing by 5, which is the mistake being tested. If you want NULLs treated as zero, write `AVG(COALESCE(c, 0))` and say so explicitly.
</details>

---

**Q7.** `Employee.dept_id` contains at least one NULL. What does this return?
```sql
SELECT dept_name FROM Department
WHERE dept_id NOT IN (SELECT dept_id FROM Employee);
```
(a) Departments with no employees  (b) All departments  (c) **No rows**  (d) An error

<details><summary>Answer</summary>

**(c) No rows.**

`x NOT IN (1, 2, NULL)` expands to `x <> 1 AND x <> 2 AND x <> NULL`. The last comparison is **UNKNOWN**, so the whole conjunction can never be TRUE — it is UNKNOWN at best. `WHERE` keeps only TRUE rows, so nothing is returned.

**The fix:** use `NOT EXISTS` (which uses row existence, not value comparison) or `LEFT JOIN … WHERE e.dept_id IS NULL`, or add `WHERE dept_id IS NOT NULL` to the subquery.

This is the most commonly failed SQL question in OAs, and it is worth being able to explain the three-valued logic out loud.
</details>

---

**Q8.** Which clause filters **groups** rather than rows?
(a) WHERE  (b) HAVING  (c) GROUP BY  (d) ORDER BY

<details><summary>Answer</summary>

**(b) HAVING.**

From the logical execution order `FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY`: `WHERE` runs before grouping and therefore cannot reference aggregates; `HAVING` runs after and can.

**Performance corollary worth saying aloud:** push any row-level condition into `WHERE`, because filtering before grouping means fewer rows to group.
</details>

---

**Q9.** Column `salary` holds `100, 200, 200, 300`. What do `RANK()` and `DENSE_RANK()` (ordered ascending) return for the value 300?
(a) 4 and 4  (b) 4 and 3  (c) 3 and 3  (d) 3 and 4

<details><summary>Answer</summary>

**(b) 4 and 3.**

| salary | ROW_NUMBER | RANK | DENSE_RANK |
|---|---|---|---|
| 100 | 1 | 1 | 1 |
| 200 | 2 | 2 | 2 |
| 200 | 3 | 2 | 2 |
| 300 | 4 | **4** | **3** |

`RANK` leaves a gap after a tie (it reports the position); `DENSE_RANK` does not (it reports the distinct level). `ROW_NUMBER` never ties.

**Choosing correctly:** "3rd highest **distinct** salary" → `DENSE_RANK`. "3rd row" → `ROW_NUMBER`. "Top 2 including ties" → `RANK` or `DENSE_RANK`.
</details>

---

**Q10.** Table A has 5 rows with `x = 1`; table B has 3 rows with `x = 1`. How many rows does `A INNER JOIN B ON A.x = B.x` return?
(a) 5  (b) 8  (c) 15  (d) 3

<details><summary>Answer</summary>

**(c) 15.**

An equi-join produces the Cartesian product **within each matching key group**: 5 × 3 = 15.

This is the arithmetic behind "why did my report double-count" — a join on a non-unique key fans out the rows, and any subsequent `SUM` is inflated. Recognising fan-out is a genuine interview discriminator.
</details>

---

**Q11.** What is the difference between `UNION` and `UNION ALL`?
(a) None  (b) `UNION` removes duplicates; `UNION ALL` keeps them  (c) `UNION ALL` is slower  (d) `UNION` requires identical table names

<details><summary>Answer</summary>

**(b).**

`UNION` performs a distinct operation, which requires a sort or hash and is therefore **slower**. `UNION ALL` simply concatenates.

(c) is backwards and is the intended distractor. Default to `UNION ALL` unless duplicates genuinely must be removed.
</details>

---

**Q12.** `DELETE`, `TRUNCATE` and `DROP` — which statement is correct?
(a) All three can be rolled back
(b) `TRUNCATE` is DDL, cannot be rolled back in most engines, and resets identity counters
(c) `DELETE` removes the table structure
(d) `DROP` keeps the structure and removes rows

<details><summary>Answer</summary>

**(b).**

| | Type | Rollback | WHERE | Identity reset | Triggers |
|---|---|---|---|---|---|
| DELETE | DML | yes | yes | no | fires |
| TRUNCATE | DDL | usually no | no | yes | does not fire |
| DROP | DDL | no | no | — | — |

`DELETE` logs each row, so it is slow but transactional; `TRUNCATE` deallocates pages, so it is fast but not row-logged. `DROP` removes the table definition entirely.
</details>

---

## Set C — Indexes

**Q13.** How many **clustered** indexes can a table have?
(a) One  (b) One per column  (c) Unlimited  (d) Two

<details><summary>Answer</summary>

**(a) One.**

A clustered index defines the **physical ordering** of the rows on disk, and rows can only be stored in one order. Non-clustered indexes are separate structures pointing at rows, so there can be many.
</details>

---

**Q14.** An index exists on `Employee(hire_date)`. Which query can use it?
(a) `WHERE YEAR(hire_date) = 2026`
(b) `WHERE hire_date >= '2026-01-01' AND hire_date < '2027-01-01'`
(c) `WHERE hire_date + INTERVAL '1 day' > '2026-01-01'`
(d) All of them

<details><summary>Answer</summary>

**(b).**

Wrapping an indexed column in a function or arithmetic makes the predicate **non-sargable** — the optimiser cannot map it onto the index's ordering, so it scans. Rewriting the condition as a **range on the bare column**, as in (b), restores index usage.

**The full list of index-defeating patterns:** a function on the column, arithmetic on the column, a leading wildcard (`LIKE '%x'`), an implicit type conversion, `OR` across different columns, and skipping the leading column of a composite index.
</details>

---

**Q15.** A composite index exists on `(a, b, c)`. Which predicate cannot use it?
(a) `WHERE a = 1`  (b) `WHERE a = 1 AND b = 2`  (c) `WHERE b = 2`  (d) `WHERE a = 1 AND b = 2 AND c = 3`

<details><summary>Answer</summary>

**(c) `WHERE b = 2`.**

A composite index is sorted by `a`, then `b` within equal `a`, then `c`. Without a constraint on `a`, the values of `b` are scattered throughout the index, so it offers no useful ordering — the **left-prefix rule**.

(Modern optimisers may still do an index *skip scan* when `a` has very few distinct values, but the rule as stated is what OAs test.)
</details>

---

**Q16.** Which is true of a B+ tree index compared with a B tree?
(a) B+ trees store data in internal nodes as well as leaves
(b) B+ trees keep all data pointers in the leaves, which are linked
(c) B trees support range queries better
(d) B+ trees are unbalanced

<details><summary>Answer</summary>

**(b).**

Keeping data only in leaves means internal nodes hold more keys, so the tree is shallower; and the linked leaves make a **range scan** a simple sequential walk after one descent. That combination is exactly why databases use B+ trees rather than B trees.

(a) describes a B tree. (c) is backwards. (d) both are balanced by construction.
</details>

---

## Set D — Transactions

**Q17.** T1 reads a row, T2 updates and commits, T1 reads the same row again and sees a different value. This is a
(a) dirty read  (b) non-repeatable read  (c) phantom read  (d) lost update

<details><summary>Answer</summary>

**(b) non-repeatable read.**

(a) would require T1 to read T2's **uncommitted** data. (c) is about new **rows** appearing in a repeated *range* query, not a changed value in an existing row. (d) is when two transactions overwrite each other's updates.

**The distinguishing question:** same row with a different value → non-repeatable. Different *set* of rows → phantom.
</details>

---

**Q18.** Which isolation level prevents dirty reads and non-repeatable reads but still permits phantoms (per the SQL standard)?
(a) READ UNCOMMITTED  (b) READ COMMITTED  (c) REPEATABLE READ  (d) SERIALIZABLE

<details><summary>Answer</summary>

**(c) REPEATABLE READ.**

It locks the rows it reads, so their values cannot change, but it does not lock the *range*, so new rows matching the predicate can still appear.

**Worth knowing for interviews:** MySQL's InnoDB actually prevents phantoms at REPEATABLE READ using next-key (gap) locks, so it is stricter than the standard requires. Mentioning that distinction reads very well.
</details>

---

**Q19.** Two-phase locking guarantees
(a) no deadlock  (b) conflict serialisability  (c) no cascading aborts  (d) all of the above

<details><summary>Answer</summary>

**(b) conflict serialisability.**

2PL guarantees a serialisable schedule but **can deadlock** — two transactions each holding a lock the other needs. Preventing cascading aborts requires **strict** 2PL, which holds exclusive locks until commit. Rigorous 2PL holds all locks until commit.

(d) is the tempting wrong answer; read exactly which variant the question names.
</details>

---

**Q20.** Which ACID property is primarily enforced by the write-ahead log?
(a) Atomicity only  (b) Durability only  (c) Atomicity and Durability  (d) Isolation

<details><summary>Answer</summary>

**(c).**

The **undo** portion of the log supports atomicity (roll back an incomplete transaction); the **redo** portion supports durability (replay committed changes after a crash). Isolation is enforced by locking or MVCC, not by the log.
</details>

---

**Q21.** MVCC primarily achieves
(a) readers do not block writers and writers do not block readers
(b) faster writes
(c) smaller indexes
(d) automatic normalisation

<details><summary>Answer</summary>

**(a).**

Each transaction reads a consistent **snapshot** rather than acquiring shared locks, so read and write workloads stop contending. The cost is storing multiple row versions and periodically reclaiming them (`VACUUM` in PostgreSQL, the purge thread in InnoDB).
</details>

---

## Set E — Mixed

**Q22.** A foreign key column can be NULL.
(a) True  (b) False  (c) Only if it is part of the primary key  (d) Only in MySQL

<details><summary>Answer</summary>
**(a) True.** A NULL foreign key means "no relationship", and referential integrity is only checked for non-NULL values. It cannot be NULL if it is *also* part of the primary key, by entity integrity.
</details>

**Q23.** Table A has 4 rows, table B has 6 rows. How many rows does `A CROSS JOIN B` return?
(a) 10  (b) 24  (c) 4  (d) 6

<details><summary>Answer</summary>
**(b) 24.** A cross join is the full Cartesian product, 4 × 6. A join with no `ON` clause, or with a condition that is always true, silently becomes this — the usual cause of a query that "hangs".
</details>

**Q24.** The CAP theorem states that a distributed system, **during a network partition**, must choose between
(a) consistency and availability
(b) consistency and partition tolerance
(c) availability and partition tolerance
(d) latency and durability

<details><summary>Answer</summary>
**(a).** Partition tolerance is not optional in a real distributed system — networks do fail — so the actual choice is between consistency and availability *while partitioned*. CAP says nothing about behaviour when there is no partition; **PACELC** covers that case, where the trade-off is latency versus consistency.
</details>

**Q25.** Which of these is **not** guaranteed by a view?
(a) It always reflects the current base-table data
(b) It can always be updated
(c) It can restrict which columns a user sees
(d) It can encapsulate a complex join

<details><summary>Answer</summary>
**(b).** A view is updatable only when the mapping back to base rows is unambiguous — typically a single base table, no aggregation, no `DISTINCT`, no `GROUP BY`, no set operations. A materialised view also breaks (a), since it is a stored snapshot refreshed on a schedule.
</details>

---

## Scoring

| Score /25 | Reading |
|---|---|
| 22+ | DBMS is OA-ready |
| 17–21 | Solid; drill the missed subtopics |
| 12–16 | Re-read `concepts.md` and redo `numericals.md` |
| < 12 | Study properly before attempting timed MCQs |

**If your misses cluster in one area**, that tells you where to spend the next session: NULL semantics and join fan-out are usually the top two, followed by 3NF vs BCNF.
