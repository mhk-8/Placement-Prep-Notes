# DBMS & SQL — Rapid Fire Q&A

> Three sentences or fewer, in your own words. For data and ML roles this section is asked more than DSA.

---

**1. What is a DBMS and why not just use files?**
It provides concurrent multi-user access with transactional guarantees, a declarative query language, and enforced integrity constraints. Files give you none of those — you would reimplement locking, crash recovery and indexing yourself.

**2. Super key vs candidate key vs primary key?**
A super key uniquely identifies a tuple; a candidate key is a *minimal* super key; the primary key is the candidate key you choose, and it is implicitly NOT NULL and UNIQUE.

**3. How do you find candidate keys from a set of FDs?**
Attributes appearing only on the left of any FD must be in every key, and attributes appearing only on the right are in none. Start from the mandatory set, compute its closure, and extend one attribute at a time until the closure covers the relation.

**4. What does an attribute closure tell you?**
X⁺ is everything X determines. If X⁺ is the whole relation, X is a super key — that single test drives almost every normalisation question.

**5. Explain the normal forms in one line each.**
1NF: atomic values. 2NF: no non-prime attribute depends on part of a composite key. 3NF: every determinant is a super key or every dependent attribute is prime. BCNF: every determinant is a super key, without exception.

**6. Give a relation in 3NF but not BCNF.**
R(A,B,C) with AB→C and C→A. Both AB and BC are candidate keys so every attribute is prime, which satisfies 3NF; but C is not a super key, so C→A violates BCNF.

**7. Why do real schemas stop at 3NF?**
Because 3NF can always be achieved with a decomposition that is both lossless and dependency-preserving, whereas BCNF may force you to lose a dependency you then have to enforce in application code.

**8. When would you denormalise?**
When reads vastly outnumber writes and the joins are measurably the bottleneck — a reporting table, a cached aggregate, a duplicated display name. The cost is that every write now has to update several places, so it is a deliberate trade, not a shortcut.

**9. What is the SQL logical execution order?**
FROM, JOIN, WHERE, GROUP BY, HAVING, SELECT, DISTINCT, ORDER BY, LIMIT. It explains why a SELECT alias is invisible in WHERE but usable in ORDER BY, and why aggregates must be filtered in HAVING.

**10. WHERE vs HAVING?**
WHERE filters rows before grouping; HAVING filters groups after. Push anything row-level into WHERE, because fewer rows reach the grouping step.

**11. Explain the join types.**
Inner returns only matching rows; left returns all of the left side with NULLs for non-matches; full outer returns everything from both; cross returns the Cartesian product. A self-join is any table joined to itself, used for hierarchies and row-to-row comparisons.

**12. How do you write an anti-join?**
`LEFT JOIN … WHERE right.key IS NULL`, or `NOT EXISTS`. Avoid `NOT IN` when the subquery can produce NULL, because the comparison becomes UNKNOWN and the query silently returns nothing.

**13. Explain NULL semantics.**
SQL uses three-valued logic, so `NULL = NULL` is UNKNOWN rather than TRUE, and WHERE keeps only TRUE rows. Aggregates skip NULLs, `COUNT(*)` does not, and GROUP BY treats all NULLs as a single group.

**14. RANK vs DENSE_RANK vs ROW_NUMBER?**
ROW_NUMBER always gives distinct consecutive numbers. RANK repeats a value on a tie and then skips positions. DENSE_RANK repeats without skipping, which is what "Nth highest distinct value" needs.

**15. When would you use a window function over GROUP BY?**
When you need an aggregate *alongside* each row rather than instead of it — running totals, per-row ranks, comparisons to the group average. GROUP BY collapses rows; a window function does not.

**16. What is a correlated subquery and why avoid it?**
One that references the outer query, so conceptually it re-executes per outer row. It is usually rewritable as a join or a window function that the optimiser can evaluate in a single pass.

**17. How does an index work?**
A B+ tree keyed on the indexed columns, with the actual row pointers in linked leaves. A lookup costs a few page reads down the tree instead of scanning the table, and the linked leaves make range scans cheap.

**18. Clustered vs non-clustered index?**
A clustered index determines the physical row order, so there is at most one, and the leaf *is* the row. A non-clustered index stores a pointer, so using it costs an extra lookup unless it covers every column the query needs.

**19. When does an index hurt?**
Every write must maintain it, so insert-heavy tables pay for each one. A low-selectivity index is also usually ignored, because many random row lookups cost more than one sequential scan.

**20. Why might an existing index not be used?**
A function or arithmetic on the column, a leading wildcard in LIKE, an implicit type conversion, or skipping the leading column of a composite index. Rewriting the predicate as a bare range on the column usually restores it.

**21. What is a covering index?**
One that contains every column the query touches, so the engine answers entirely from the index without visiting the table. It is the usual fix when a query is index-seeking but still slow.

**22. Explain ACID.**
Atomicity: all or nothing, via the undo log. Consistency: constraints hold across the transaction. Isolation: the result is equivalent to some serial order. Durability: committed data survives a crash, via the redo log.

**23. Explain the read anomalies.**
A dirty read sees uncommitted data. A non-repeatable read sees the same row with a different value on a second read. A phantom read sees new rows appear in a repeated range query.

**24. Which isolation level would you choose in production?**
Usually READ COMMITTED, which is the default in PostgreSQL and Oracle — it prevents dirty reads at low cost. Raise it only where an operation genuinely needs a stable read set, because higher levels cost concurrency.

**25. What is two-phase locking?**
A growing phase that only acquires locks and a shrinking phase that only releases them. It guarantees conflict serialisability but can deadlock, and strict 2PL additionally holds exclusive locks until commit to prevent cascading aborts.

**26. How do databases detect deadlock?**
By building a wait-for graph and looking for a cycle, then aborting the cheapest victim. Some engines simply use a lock-wait timeout instead.

**27. What is MVCC?**
Each transaction reads a consistent snapshot rather than taking read locks, so readers and writers stop blocking each other. The cost is storing old row versions and reclaiming them later.

**28. SQL vs NoSQL — how do you choose?**
Relational when the data is relational, the schema is stable and you need transactions and joins. NoSQL when you need horizontal scale, flexible documents or extreme write throughput and can live with weaker guarantees.

**29. State the CAP theorem carefully.**
During a network partition a distributed system must choose between consistency and availability. It says nothing about the partition-free case, which is where people most often misapply it.

**30. What is a stored procedure, and a trigger?**
A stored procedure is precompiled SQL invoked by name, useful for encapsulating multi-statement logic. A trigger fires automatically on an insert, update or delete, which makes it powerful and also easy to turn into invisible, hard-to-debug behaviour.

**31. What is a view, and when is it updatable?**
A named stored query that always reflects current base data. It is updatable only when rows map back unambiguously — one base table, no aggregation, no DISTINCT, no GROUP BY.

**32. What is sharding, and how does it differ from replication?**
Sharding splits *different* rows across machines to scale writes and storage; replication copies the *same* rows to scale reads and provide failover. Most large systems use both.

**33. How would you debug a slow query?**
Read the execution plan first — look for sequential scans on large tables, nested loops over big row counts, and a large gap between estimated and actual rows. Then check indexes, predicate sargability and whether statistics are stale.

**34. What is the N+1 query problem?**
Fetching a list with one query, then issuing one query per row for related data. The fix is a single join or a batched `IN` query — it is the most common ORM performance bug and worth naming by name.

**35. Tell me about the database work in your own project.**
*(Answer from your projects: the schema decision you made, one query you had to optimise and what the plan showed, and one thing you would design differently now.)*
