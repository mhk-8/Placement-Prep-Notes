# SQL — Solved OA Questions

> 16 questions in OA style with every option explained. SQL is often a **separate timed section**, and the failure modes are unusually predictable: NULL semantics, join behaviour and ranking-function choice.
> Cover the answers. **60–90 seconds** each.

---

**Q1.** Table `T(c)` holds the values `'x'`, `'y'`, `NULL`. How many rows does this return?
```sql
SELECT * FROM T WHERE c <> 'x';
```
(a) 1  (b) 2  (c) 3  (d) 0

<details><summary>Answer</summary>

**(a) 1.**

`'y' <> 'x'` is TRUE ✔. `'x' <> 'x'` is FALSE. **`NULL <> 'x'` is UNKNOWN**, and `WHERE` keeps only TRUE rows — so the NULL row is silently dropped.

**To include it:** `WHERE c <> 'x' OR c IS NULL`.

This is the most under-appreciated NULL trap, because "not equal to x" reads as though it should include everything that is not x.
</details>

---

**Q2.** `Emp.dept_id` contains at least one NULL. What does this return?
```sql
SELECT name FROM Dept
WHERE id NOT IN (SELECT dept_id FROM Emp);
```
(a) Departments with no employees  (b) All departments  (c) **No rows**  (d) Error

<details><summary>Answer</summary>

**(c) No rows.**

`x NOT IN (1, 2, NULL)` expands to `x <> 1 AND x <> 2 AND x <> NULL`. The last conjunct is UNKNOWN, so the whole expression is UNKNOWN at best and never TRUE.

**The fixes:** `NOT EXISTS`, a `LEFT JOIN … WHERE right IS NULL`, or adding `WHERE dept_id IS NOT NULL` to the subquery.

The most commonly failed SQL question in OAs, and a genuine production bug — it typically appears only after the first NULL enters the column.
</details>

---

**Q3.** `A` has 5 rows with `k = 1`; `B` has 3 rows with `k = 1`. How many rows does `A INNER JOIN B ON A.k = B.k` return?
(a) 5  (b) 8  (c) **15**  (d) 3

<details><summary>Answer</summary>

**(c) 15.**

An equi-join takes the Cartesian product **within each matching key group**: 5 × 3.

**Why it matters:** a subsequent `SUM(A.amount)` is now inflated threefold. When an analytical total looks too large, count the rows before and after the join — recognising fan-out is a genuine interview discriminator.
</details>

---

**Q4.** What does this return?
```sql
SELECT a.id
FROM   a LEFT JOIN b ON a.id = b.a_id
WHERE  b.status = 'active';
```
(a) All rows of `a`  (b) Only rows of `a` with an active match — effectively an inner join  (c) Rows of `a` with no match  (d) Error

<details><summary>Answer</summary>

**(b) — it behaves as an INNER JOIN.**

Unmatched rows of `a` get NULL for `b.status`, and `NULL = 'active'` is UNKNOWN, so `WHERE` discards exactly the rows the `LEFT JOIN` was there to preserve.

**The fix:** move the condition into the join:
```sql
FROM a LEFT JOIN b ON a.id = b.a_id AND b.status = 'active'
```
**The rule:** conditions on the *optional* side belong in `ON`; conditions on the *driving* side belong in `WHERE`.
</details>

---

**Q5.** Salaries are `100, 200, 200, 300`. What do `ROW_NUMBER`, `RANK` and `DENSE_RANK` (ordered ascending) give the value 300?
(a) 4, 4, 4  (b) 4, 4, 3  (c) 3, 3, 3  (d) 4, 3, 3

<details><summary>Answer</summary>

**(b) 4, 4, 3.**

| salary | ROW_NUMBER | RANK | DENSE_RANK |
|---|---|---|---|
| 100 | 1 | 1 | 1 |
| 200 | 2 | 2 | 2 |
| 200 | 3 | 2 | 2 |
| 300 | 4 | **4** | **3** |

`RANK` reports the row's *position* and leaves a gap after a tie; `DENSE_RANK` reports the distinct *level* and does not.

**Choosing:** "Nth highest **distinct** salary" → `DENSE_RANK`. "The Nth **row**" → `ROW_NUMBER`. "Top N **including ties**" → `RANK` or `DENSE_RANK`.
</details>

---

**Q6.** Rows are `(t=1, v=10)`, `(t=1, v=20)`, `(t=2, v=30)`. What does this produce?
```sql
SELECT v, SUM(v) OVER (ORDER BY t) AS running FROM T;
```
(a) 10, 30, 60  (b) 30, 30, 60  (c) 10, 20, 30  (d) 60, 60, 60

<details><summary>Answer</summary>

**(b) 30, 30, 60.**

With an `ORDER BY` and **no explicit frame**, the default is `RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`, which includes all **peer** rows — every row sharing the current `ORDER BY` value. Both `t = 1` rows therefore see the full 10 + 20 = 30.

**For a true row-by-row running total:**
```sql
SUM(v) OVER (ORDER BY t ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW)
```
which gives (a) 10, 30, 60.

**Always write the `ROWS` frame explicitly.** This trap is invisible until the data contains ties.
</details>

---

**Q7.** Which query correctly finds the **3rd highest distinct** salary?
(a) `SELECT salary FROM emp ORDER BY salary DESC LIMIT 1 OFFSET 2`
(b) `SELECT salary FROM (SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) r FROM emp) t WHERE r = 3`
(c) `SELECT MAX(salary) FROM emp WHERE salary < (SELECT MAX(salary) FROM emp)`
(d) `SELECT salary FROM emp WHERE salary = 3`

<details><summary>Answer</summary>

**(b).**

(a) returns the 3rd **row**, which is wrong whenever duplicates exist — with salaries 300, 300, 200, 100 it returns 200 where the 3rd distinct is 100.
(c) gives the 2nd highest, not the 3rd.
(d) is nonsense.

Only `DENSE_RANK` collapses ties into a single rank level, which is exactly what "distinct" asks for.
</details>

---

**Q8.** Column `c` holds `1, 2, NULL, NULL, 5` across 5 rows. What do `COUNT(*)`, `COUNT(c)`, `SUM(c)` and `AVG(c)` return?
(a) 5, 5, 8, 1.6  (b) 5, 3, 8, 2.67  (c) 3, 3, 8, 2.67  (d) 5, 3, 8, 1.6

<details><summary>Answer</summary>

**(b) 5, 3, 8, 2.67.**

`COUNT(*)` counts rows, NULLs included. `COUNT(c)` counts non-NULL values. `SUM` skips NULLs. `AVG` = 8 / **3** = 2.67 — it divides by the non-null count, not the row count.

(a) and (d) divide by 5, which is the intended trap. For NULLs-as-zero semantics write `AVG(COALESCE(c, 0))` and say so.

**One more worth knowing:** `SUM` over an empty or all-NULL set returns **NULL**, not 0. Wrap it in `COALESCE`.
</details>

---

**Q9.** Which clause filters **groups** rather than rows?
(a) WHERE  (b) **HAVING**  (c) GROUP BY  (d) ORDER BY

<details><summary>Answer</summary>

**(b) HAVING.**

From the logical order `FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY`: `WHERE` runs before grouping and so cannot see aggregates; `HAVING` runs after and can.

**The performance corollary:** push every row-level condition into `WHERE`, because fewer rows then reach the grouping step.
</details>

---

**Q10.** Why can this not be used?
```sql
SELECT name, salary * 12 AS annual FROM emp WHERE annual > 100000;
```
(a) `salary` cannot be multiplied
(b) `WHERE` is evaluated before `SELECT`, so the alias does not exist yet
(c) Aliases need quotes
(d) It works fine

<details><summary>Answer</summary>

**(b).**

`SELECT` — where the alias is created — runs *after* `WHERE`. Repeat the expression (`WHERE salary * 12 > 100000`), or wrap the query in a subquery or CTE.

**Consistency check:** the same alias **is** usable in `ORDER BY`, because `ORDER BY` runs after `SELECT`. That asymmetry is exactly what the logical execution order predicts.
</details>

---

**Q11.** What is the difference between `UNION` and `UNION ALL`?
(a) None  (b) `UNION` removes duplicates and is slower; `UNION ALL` keeps them  (c) `UNION ALL` is slower  (d) `UNION` requires identical table names

<details><summary>Answer</summary>

**(b).**

`UNION` performs a distinct operation, requiring a sort or hash pass; `UNION ALL` simply concatenates.

(c) is backwards and is the intended distractor. **Default to `UNION ALL`** unless duplicates genuinely must be removed — on large result sets the difference is substantial.
</details>

---

**Q12.** Which query can use an index on `orders(order_date)`?
(a) `WHERE YEAR(order_date) = 2026`
(b) `WHERE order_date >= '2026-01-01' AND order_date < '2027-01-01'`
(c) `WHERE order_date + INTERVAL '1 day' > '2026-01-01'`
(d) All of them

<details><summary>Answer</summary>

**(b).**

Wrapping an indexed column in a function or arithmetic makes the predicate **non-sargable** — the optimiser cannot map it onto the index's ordering and falls back to a scan. Expressing the same condition as a **range on the bare column** restores index usage.

**The full list of index-defeating patterns:** a function on the column, arithmetic on the column, a leading `%` wildcard, an implicit type conversion, `OR` across different columns, and skipping the leading column of a composite index.
</details>

---

**Q13.** How many rows does `SELECT DISTINCT a, b FROM T` return for rows `(1,1), (1,2), (1,1)`?
(a) 1  (b) 2  (c) 3  (d) 4

<details><summary>Answer</summary>

**(b) 2.**

`DISTINCT` applies to the **entire projected row**, so it de-duplicates the *pairs*: `(1,1)` and `(1,2)`.

(a) would be the answer if `DISTINCT` applied only to `a`, which is the misconception being tested. There is no per-column `DISTINCT` in standard SQL — `COUNT(DISTINCT a)` is the closest, and it works on one column at a time.
</details>

---

**Q14.** What does this return when `total` is 0 for the previous month?
```sql
SELECT 100.0 * (total - prev) / NULLIF(prev, 0) AS pct FROM t;
```
(a) An error  (b) Infinity  (c) **NULL**  (d) 0

<details><summary>Answer</summary>

**(c) NULL.**

`NULLIF(prev, 0)` returns NULL when `prev` is 0, and dividing by NULL yields NULL rather than raising a divide-by-zero error.

This is the idiomatic guard for growth and ratio calculations, and interviewers specifically look for it. Wrap the whole thing in `COALESCE(..., 0)` if you want a zero instead of a NULL in the output.
</details>

---

**Q15.** Which finds employees who logged in on **3 or more consecutive days**?
(a) A self join on `id = id + 1` chained twice
(b) `GROUP BY user_id HAVING COUNT(*) >= 3`
(c) Grouping on `date − ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY date)`
(d) `ORDER BY date LIMIT 3`

<details><summary>Answer</summary>

**(c).**

For a run of consecutive dates, `date − row_number` is **constant**, because both increase by one per row. Grouping by that difference isolates each run, and `HAVING COUNT(*) >= 3` keeps the long ones.

(a) works for exactly three but does not generalise to N. (b) counts total logins, not consecutive ones.

**This "gaps and islands" idiom is worth memorising outright** — it answers every consecutive-record question in one shape.
</details>

---

**Q16.** Where may a window function appear?
(a) In `WHERE`  (b) In `HAVING`  (c) In `SELECT` and `ORDER BY` only  (d) Anywhere

<details><summary>Answer</summary>

**(c) `SELECT` and `ORDER BY` only.**

Window functions are computed **after** `WHERE`, `GROUP BY` and `HAVING`, so those clauses cannot reference them.

**To filter on one**, wrap the query and filter outside:
```sql
SELECT * FROM (
    SELECT *, RANK() OVER (ORDER BY salary DESC) AS r FROM emp
) t WHERE r <= 3;
```
That wrapping is why nearly every top-N query in `query-patterns.md` has a subquery around it.
</details>

---

## Scoring

| Score /16 | Reading |
|---|---|
| 14+ | SQL is OA-ready |
| 10–13 | Drill the NULL and join items; redo `query-patterns.md` |
| 6–9 | Re-read `syntax-reference.md` §1 and §6 |
| < 6 | SQL is P1 for every track — make it the next session |

**Diagnostic:** misses on Q1, Q2 and Q8 all trace to three-valued logic. That single idea causes more wrong SQL than everything else in this file combined, and it takes one focused session to fix.
