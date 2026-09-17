# DBMS & SQL — Flashcards

## Questions

1. Define super key, candidate key, primary key and prime attribute.
2. How do you compute an attribute closure, and what does X⁺ = R mean?
3. Give the four-step procedure for finding all candidate keys.
4. State 2NF, 3NF and BCNF precisely.
5. Give a relation that is in 3NF but not BCNF, with the reason.
6. What does a 3NF decomposition guarantee that BCNF cannot?
7. State the lossless-join condition for a two-way decomposition.
8. Write the SQL logical execution order.
9. Why can a SELECT alias be used in ORDER BY but not in WHERE?
10. `COUNT(*)` vs `COUNT(col)` vs `AVG(col)` when NULLs are present?
11. Why does `NOT IN` with a NULL-producing subquery return no rows?
12. Distinguish RANK, DENSE_RANK and ROW_NUMBER on the data 100, 200, 200, 300.
13. Table A has 5 rows with k=1, B has 3 rows with k=1 — how many rows does the equi-join return, and why does it matter?
14. Give the gaps-and-islands trick for consecutive records.
15. Why do databases use B+ trees rather than B trees?
16. How many clustered indexes per table, and why?
17. List five reasons an existing index is not used.
18. What is the left-prefix rule for composite indexes?
19. Define dirty read, non-repeatable read and phantom read.
20. Which isolation level prevents each?
21. What does 2PL guarantee, and what does it not prevent?
22. What extra guarantee does strict 2PL add?
23. What does MVCC achieve and what does it cost?
24. Which ACID properties does the write-ahead log support, and via which parts?
25. State CAP precisely, and what PACELC adds.

---

## Answers

1. Super key: any attribute set that uniquely identifies a tuple. Candidate key: a minimal super key. Primary key: the chosen candidate key, implicitly NOT NULL UNIQUE. Prime attribute: one belonging to some candidate key.
2. Start with the attribute set and repeatedly add the right side of any FD whose left side is already contained, until nothing changes. X⁺ = R means X is a super key.
3. Attributes only on the left are in every key; attributes only on the right are in none; attributes on neither side are in every key. Start from the mandatory set, take closures, and extend minimally until the closure is everything.
4. 2NF: 1NF and no non-prime attribute depends on a proper subset of a candidate key. 3NF: for every non-trivial X→Y, X is a super key or Y is prime. BCNF: for every non-trivial X→Y, X is a super key.
5. R(A,B,C) with AB→C and C→A. Candidate keys AB and BC make every attribute prime, so 3NF holds; C is not a super key, so C→A breaks BCNF.
6. Dependency preservation. A 3NF decomposition can always be both lossless and dependency-preserving; a BCNF one is always lossless but may lose a dependency.
7. Lossless iff (R1 ∩ R2) → R1 or (R1 ∩ R2) → R2 — the common attributes must be a key of at least one fragment.
8. FROM → JOIN → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT.
9. SELECT is evaluated after WHERE but before ORDER BY, so the alias does not exist yet when WHERE runs and does exist when ORDER BY runs.
10. `COUNT(*)` counts all rows including NULLs; `COUNT(col)` counts non-NULL values only; `AVG(col)` sums the non-NULLs and divides by the **non-null count**, not the row count.
11. `x NOT IN (…, NULL)` expands to a conjunction containing `x <> NULL`, which is UNKNOWN; the whole condition can never be TRUE, and WHERE keeps only TRUE rows.
12. ROW_NUMBER: 1,2,3,4. RANK: 1,2,2,**4**. DENSE_RANK: 1,2,2,**3**.
13. 5 × 3 = 15 — the join fans out within each key group. It matters because a subsequent SUM is then silently inflated, which is the classic double-counting bug.
14. `date − ROW_NUMBER() OVER (PARTITION BY id ORDER BY date)` is constant within a consecutive run, so grouping by it isolates each streak.
15. Internal nodes hold no data, so they pack more keys and the tree is shallower; and the leaves are linked, making range scans a single descent plus a sequential walk.
16. One, because a clustered index defines the physical row order and rows can only be stored in one order.
17. A function or arithmetic on the column, a leading `%` wildcard, an implicit type conversion, OR across different columns, and skipping the leading column of a composite index.
18. A composite index on (a,b,c) is usable for a, for a+b and for a+b+c, but not for b or c alone, because it is sorted by a first.
19. Dirty read: reading uncommitted data. Non-repeatable read: the same row yields a different value on re-read. Phantom read: a repeated range query returns new rows.
20. READ COMMITTED prevents dirty reads; REPEATABLE READ additionally prevents non-repeatable reads; SERIALIZABLE additionally prevents phantoms (InnoDB prevents them at REPEATABLE READ via next-key locks).
21. Conflict serialisability. It does **not** prevent deadlock.
22. It holds all exclusive locks until commit, which additionally prevents cascading aborts.
23. Readers and writers stop blocking each other because readers see a snapshot. The cost is storing multiple row versions and reclaiming them (VACUUM / purge).
24. Atomicity via the undo portion and durability via the redo portion.
25. During a network partition, a system must choose consistency or availability; it makes no claim about the partition-free case. PACELC adds: *else*, when there is no partition, choose between latency and consistency.
