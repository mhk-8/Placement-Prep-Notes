# SQL — Gotchas

> SQL fails quietly. A wrong query usually returns *a* result rather than an error, which is what makes these worth memorising.

---

## NULL — three-valued logic

- `NULL = NULL` is **UNKNOWN**, not TRUE. Use `IS NULL`.
- `WHERE col <> 'x'` **excludes** rows where `col` is NULL, because the comparison is UNKNOWN and `WHERE` keeps only TRUE. Add `OR col IS NULL` when you want them.
- `NOT IN (subquery)` returns **no rows** if the subquery yields a single NULL, because the expanded conjunction can never be TRUE. Use `NOT EXISTS`, or filter NULLs out of the subquery.
- `COUNT(*)` counts rows including NULLs; `COUNT(col)` skips them; `AVG(col)` divides by the **non-null** count.
- `SUM` over an all-NULL (or empty) set returns **NULL**, not 0. Wrap with `COALESCE(SUM(x), 0)`.
- `'abc' || NULL` is NULL in standard SQL and Postgres; Oracle treats NULL as an empty string. MySQL's `CONCAT` returns NULL.
- `GROUP BY` treats all NULLs as **one group**, and `UNIQUE` permits multiple NULLs in most engines — two places where NULLs behave as equal despite the comparison rule.
- `ORDER BY` puts NULLs first in some engines and last in others. Say `NULLS LAST` explicitly.

## Joins

- **A filter on the right table in `WHERE` turns a `LEFT JOIN` into an `INNER JOIN`**, because unmatched rows have NULL there and fail the predicate. Put the condition in `ON` instead.
- **Fan-out:** joining on a non-unique key multiplies rows, so any subsequent `SUM` is inflated. Five matching rows on the left and three on the right give fifteen. Aggregate the many-side first.
- A missing `ON` clause silently becomes a cross join — the usual cause of a query that "hangs".
- `USING(col)` merges the column; `ON a.col = b.col` keeps both. This changes what `SELECT *` returns.
- Joining on columns of different types forces an implicit conversion that usually disables the index.

## GROUP BY and HAVING

- Every non-aggregated `SELECT` column must be in `GROUP BY`. MySQL historically allowed otherwise and returned an arbitrary row's value.
- Aggregates are not allowed in `WHERE` — they belong in `HAVING`.
- Putting a row-level condition in `HAVING` still works but is slower: filter before grouping, not after.
- `HAVING COUNT(*) > 1` finds duplicates; `WHERE COUNT(*) > 1` is a syntax error.
- Grouping by a name rather than an id merges two genuinely different entities that happen to share a name.

## Window functions

- **The default frame is `RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`**, which includes all **peer** rows with the same `ORDER BY` value. A "running total" over tied values jumps to the full tied sum. Write `ROWS BETWEEN …` explicitly.
- Window functions cannot be used in `WHERE` or `HAVING` — they are computed after those clauses. Wrap the query in a subquery or CTE and filter outside.
- `RANK` leaves gaps after ties; `DENSE_RANK` does not; `ROW_NUMBER` never ties. Picking the wrong one is the most common error in "Nth highest" questions.
- `LAST_VALUE` with the default frame returns the current row, not the partition's last — you must specify `ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING`.
- `PARTITION BY` is not `GROUP BY`: it does not collapse rows.

## Set operations and DISTINCT

- `UNION` removes duplicates and therefore sorts, which is slower; `UNION ALL` just concatenates. Default to `UNION ALL`.
- `SELECT DISTINCT a, b` de-duplicates the **pair**, not each column separately.
- `DISTINCT` runs after `SELECT`, so it cannot see columns you did not project.
- `COUNT(DISTINCT col)` is much more expensive than `COUNT(col)` on large tables.

## Indexes and performance

- A function or arithmetic on an indexed column makes the predicate non-sargable: `WHERE YEAR(dt) = 2026` cannot use the index on `dt`. Rewrite as a range on the bare column.
- A leading wildcard (`LIKE '%abc'`) cannot use an index; a trailing one (`LIKE 'abc%'`) can.
- Implicit type conversion disables an index.
- A composite index on `(a, b, c)` serves `a`, `a+b` and `a+b+c` — the **left-prefix rule** — but not `b` alone.
- `SELECT *` fetches columns you do not need and prevents covering-index-only scans.
- `OR` across different columns often defeats indexes; a `UNION ALL` of two indexed queries can be faster.

## DML

- `UPDATE` or `DELETE` **without a `WHERE`** affects every row. Run the `SELECT` first, always.
- `DELETE` is transactional and row-logged; `TRUNCATE` is DDL, usually not rollback-able, and resets identity counters; `DROP` removes the table.
- `INSERT` without a column list breaks when the table gains a column.
- Autocommit means a mistake may be permanent before you notice it.

## Reading and writing

- `BETWEEN` is **inclusive** on both ends — `BETWEEN '2026-01-01' AND '2026-01-31'` misses timestamps on the 31st after midnight. Use `>= start AND < next_start`.
- `LIKE` is case-sensitive in Postgres and case-insensitive in MySQL's default collation.
- `'10' < '9'` is true — string comparison is lexicographic.
- Integer division: `1/2` is 0 in most engines. Multiply by `1.0` or cast.
- Reserved words as column names need quoting (`"order"`, `` `order` ``), which differs by dialect.

## The five that cost the most marks

1. `NOT IN` with a NULL-producing subquery returning zero rows
2. A right-table filter in `WHERE` collapsing a `LEFT JOIN` into an inner join
3. Join fan-out inflating a `SUM`
4. `RANK` vs `DENSE_RANK` vs `ROW_NUMBER` chosen wrongly
5. The default `RANGE` window frame producing a wrong "running" total
