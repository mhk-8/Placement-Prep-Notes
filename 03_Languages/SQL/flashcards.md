# SQL — Flashcards

## Questions

1. Give the logical execution order of a SELECT statement.
2. Why is a SELECT alias usable in `ORDER BY` but not in `WHERE`?
3. Why does `WHERE c <> 'x'` exclude rows where `c` is NULL?
4. Why does `NOT IN` with a NULL-producing subquery return no rows, and what replaces it?
5. `COUNT(*)` vs `COUNT(col)` vs `AVG(col)` vs `SUM(col)` when NULLs are present?
6. What happens to a `LEFT JOIN` when you filter the right table in `WHERE`?
7. What is join fan-out and how do you avoid it inflating a SUM?
8. Distinguish `ROW_NUMBER`, `RANK` and `DENSE_RANK` on 100, 200, 200, 300.
9. Which ranking function does "Nth highest distinct value" need, and why?
10. What is the **default window frame** and why is it a trap?
11. Where may a window function appear, and how do you filter on one?
12. Give the gaps-and-islands trick for consecutive records.
13. What does `NULLIF(x, 0)` guard against?
14. `UNION` vs `UNION ALL` — which is faster and why?
15. What does `SELECT DISTINCT a, b` de-duplicate?
16. Name five reasons an existing index is not used.
17. What is the left-prefix rule?
18. `DELETE` vs `TRUNCATE` vs `DROP`?
19. What is a recursive CTE made of?
20. Give the five checks for reviewing an unfamiliar query.

---

## Answers

1. FROM → JOIN → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT.
2. Aliases are created in `SELECT`, which runs after `WHERE` and before `ORDER BY`.
3. `NULL <> 'x'` evaluates to UNKNOWN, and `WHERE` keeps only TRUE rows. Add `OR c IS NULL`.
4. It expands to a conjunction containing `x <> NULL`, which is UNKNOWN, so the whole condition can never be TRUE. Use `NOT EXISTS` or a `LEFT JOIN … IS NULL` anti-join.
5. `COUNT(*)` counts all rows; `COUNT(col)` counts non-NULLs; `AVG` sums non-NULLs and divides by the **non-null count**; `SUM` over an all-NULL or empty set returns **NULL**, not 0.
6. Unmatched rows have NULL on the right, so the predicate is UNKNOWN and they are discarded — the join effectively becomes an INNER JOIN. Put the condition in `ON` instead.
7. Joining on a non-unique key multiplies rows (5 × 3 = 15), inflating any subsequent aggregate. Aggregate the many-side in a subquery first, then join.
8. ROW_NUMBER 1,2,3,4. RANK 1,2,2,**4**. DENSE_RANK 1,2,2,**3**.
9. `DENSE_RANK`, because it assigns one rank level per distinct value; `RANK` skips positions after ties and `ROW_NUMBER` never ties.
10. `RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`, which includes all **peer** rows sharing the current ORDER BY value — so a "running total" jumps to the full tied sum. Write `ROWS BETWEEN …` explicitly.
11. Only in `SELECT` and `ORDER BY`, because they are computed after `WHERE`, `GROUP BY` and `HAVING`. To filter on one, wrap the query in a subquery or CTE and filter outside.
12. `date − ROW_NUMBER() OVER (PARTITION BY id ORDER BY date)` is constant within a consecutive run, so grouping by it isolates each streak.
13. Division by zero — it turns the zero denominator into NULL, so the expression yields NULL rather than raising an error.
14. `UNION ALL` is faster; `UNION` must perform a distinct operation (a sort or hash pass) to remove duplicates.
15. The entire projected row — the (a, b) pair — not each column separately.
16. A function or arithmetic on the column; a leading `%` wildcard; an implicit type conversion; `OR` across different columns; skipping the leading column of a composite index.
17. A composite index on (a, b, c) is usable for a, for a+b and for a+b+c, but not for b or c alone, because it is sorted by a first.
18. `DELETE` is DML, row-logged, transactional, supports `WHERE`. `TRUNCATE` is DDL, fast, usually not rollback-able, resets identity counters, no `WHERE`. `DROP` removes the table definition entirely.
19. An anchor member, `UNION ALL`, and a recursive member that joins back to the CTE itself — with a depth bound on real data to avoid cycling.
20. Find the innermost FROM and work outward; identify the grain at each stage; check every join for fan-out; check every LEFT JOIN for a WHERE that turns it inner; check NULL handling in `NOT IN`, `<>` and aggregates.
