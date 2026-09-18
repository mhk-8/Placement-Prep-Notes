# SQL — Syntax Reference

> **Scope note.** `02_Core_CS/02_DBMS_and_SQL` covers the *theory* — normalisation, transactions, indexes — and the query-writing problems. This folder covers the *language*: clauses, functions, window frames and dialect differences.

---

## 1. Logical execution order

```
FROM → JOIN → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT
```

Everything confusing about SQL follows from this one list:
- a `SELECT` alias is invisible in `WHERE` (it does not exist yet) but usable in `ORDER BY`
- aggregates cannot appear in `WHERE`; they belong in `HAVING`
- `DISTINCT` runs after `SELECT`, so it de-duplicates the *projected* rows
- `LIMIT` applies last, after the sort

---

## 2. SELECT

```sql
SELECT DISTINCT col1, col2 AS alias, col1 * 2 AS doubled
FROM   table t
WHERE  cond
GROUP  BY col1, col2
HAVING aggregate_cond
ORDER  BY col1 ASC, col2 DESC
LIMIT  10 OFFSET 20;
```

`DISTINCT` applies to the **whole projected row**, not to one column: `SELECT DISTINCT a, b` removes duplicate (a, b) *pairs*.

`ORDER BY` supports positional references (`ORDER BY 2 DESC`) and aliases, and puts NULLs last by default in some engines and first in others — say `NULLS LAST` explicitly when it matters.

---

## 3. Filtering

```sql
WHERE col = 5
  AND col BETWEEN 1 AND 10          -- inclusive on both ends
  AND col IN (1, 2, 3)
  AND name LIKE 'A%'                -- % any sequence, _ exactly one char
  AND name ILIKE 'a%'               -- case-insensitive (Postgres)
  AND col IS NULL                   -- NEVER  col = NULL
  AND col IS NOT NULL
  AND NOT (cond)
  AND EXISTS (SELECT 1 FROM other o WHERE o.id = t.id);
```

**Three-valued logic.** Any comparison involving NULL yields UNKNOWN, and `WHERE` keeps only TRUE rows. So `WHERE col <> 'x'` silently **excludes** rows where `col` is NULL — write `WHERE col <> 'x' OR col IS NULL` when you want them.

---

## 4. Joins

```sql
FROM a INNER JOIN b ON a.id = b.a_id      -- only matching rows
FROM a LEFT  JOIN b ON a.id = b.a_id      -- all of a, NULLs where unmatched
FROM a RIGHT JOIN b ON a.id = b.a_id
FROM a FULL OUTER JOIN b ON a.id = b.a_id
FROM a CROSS JOIN b                        -- Cartesian product
FROM emp e JOIN emp m ON e.mgr_id = m.id   -- self join
```

**The most important join subtlety:** a condition on the right table belongs in `ON`, not `WHERE`.

```sql
-- keeps unmatched rows of a
FROM a LEFT JOIN b ON a.id = b.a_id AND b.status = 'active'

-- silently becomes an INNER join: unmatched rows have b.status NULL, which fails the filter
FROM a LEFT JOIN b ON a.id = b.a_id  WHERE b.status = 'active'
```

**Anti-join** (in a, not in b):
```sql
SELECT a.* FROM a LEFT JOIN b ON a.id = b.a_id WHERE b.a_id IS NULL;
-- or
SELECT a.* FROM a WHERE NOT EXISTS (SELECT 1 FROM b WHERE b.a_id = a.id);
```

---

## 5. Aggregation

```sql
COUNT(*)            -- rows, NULLs included
COUNT(col)          -- non-NULL values only
COUNT(DISTINCT col)
SUM, AVG, MIN, MAX  -- all skip NULLs
STRING_AGG(col, ',')            -- Postgres;  GROUP_CONCAT in MySQL
ARRAY_AGG(col)                  -- Postgres
```

Every non-aggregated column in `SELECT` must appear in `GROUP BY` (Postgres and standard SQL enforce this; MySQL historically did not, which produced arbitrary values).

`AVG` divides by the **non-null count**, not the row count. Use `AVG(COALESCE(col, 0))` if NULLs should count as zero.

---

## 6. Window functions

```sql
func() OVER (
    PARTITION BY col
    ORDER BY col2
    ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
)
```

A window function computes a value **per row without collapsing rows** — the essential difference from `GROUP BY`.

| Function | Behaviour |
|---|---|
| `ROW_NUMBER()` | 1, 2, 3, 4 — arbitrary tiebreak |
| `RANK()` | 1, 2, 2, **4** — gaps after ties |
| `DENSE_RANK()` | 1, 2, 2, **3** — no gaps |
| `NTILE(n)` | bucket number |
| `LAG(col, n, default)` | value n rows back |
| `LEAD(col, n, default)` | value n rows forward |
| `FIRST_VALUE` / `LAST_VALUE` | first/last in the frame |
| `SUM/AVG/COUNT ... OVER` | running or windowed aggregate |

**The frame default is a genuine trap.** With an `ORDER BY` and no explicit frame, the default is `RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`, which includes **all peer rows** (rows with the same ORDER BY value). For a true row-by-row running total, say `ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW` explicitly.

```sql
ROWS  BETWEEN 2 PRECEDING AND CURRENT ROW      -- physical rows
RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW  -- logical, includes peers
```

---

## 7. CTEs and subqueries

```sql
WITH monthly AS (
    SELECT DATE_TRUNC('month', dt) AS m, SUM(amt) AS total
    FROM   sales GROUP BY 1
),
ranked AS (
    SELECT *, RANK() OVER (ORDER BY total DESC) AS rnk FROM monthly
)
SELECT * FROM ranked WHERE rnk <= 3;
```

CTEs make multi-step logic readable and can be referenced more than once. **Recursive CTE** for hierarchies:

```sql
WITH RECURSIVE tree AS (
    SELECT id, mgr_id, 1 AS depth FROM emp WHERE mgr_id IS NULL   -- anchor
    UNION ALL
    SELECT e.id, e.mgr_id, t.depth + 1                            -- recursive step
    FROM   emp e JOIN tree t ON e.mgr_id = t.id
)
SELECT * FROM tree;
```

**Correlated** subqueries reference the outer query and conceptually run per outer row; they are usually rewritable as a join or a window function, which is what an interviewer wants to see.

---

## 8. Conditional and NULL handling

```sql
CASE WHEN cond THEN a WHEN cond2 THEN b ELSE c END
COALESCE(a, b, c)          -- first non-NULL
NULLIF(a, b)               -- NULL when a = b — the divide-by-zero guard
COALESCE(col, 0)
```

```sql
-- conditional aggregation: the portable PIVOT
SELECT dept,
       SUM(CASE WHEN yr = 2025 THEN 1 ELSE 0 END) AS y2025,
       SUM(CASE WHEN yr = 2026 THEN 1 ELSE 0 END) AS y2026
FROM   emp GROUP BY dept;
```

`NULLIF(denominator, 0)` in a division is the guard interviewers look for.

---

## 9. Set operations

```sql
SELECT a FROM t1 UNION     SELECT a FROM t2;   -- removes duplicates (sorts — slower)
SELECT a FROM t1 UNION ALL SELECT a FROM t2;   -- keeps duplicates (faster)
SELECT a FROM t1 INTERSECT SELECT a FROM t2;
SELECT a FROM t1 EXCEPT    SELECT a FROM t2;   -- MINUS in Oracle
```

Default to `UNION ALL` unless duplicates genuinely must be removed.

---

## 10. Common functions

```sql
-- strings
UPPER, LOWER, TRIM, LENGTH, SUBSTRING(s FROM 2 FOR 3),
REPLACE(s, a, b), CONCAT(a, b), a || b, POSITION(sub IN s), LEFT, RIGHT

-- numbers
ROUND(x, 2), CEIL, FLOOR, ABS, MOD(a, b), POWER, SQRT

-- dates
CURRENT_DATE, NOW(), EXTRACT(YEAR FROM dt), DATE_TRUNC('month', dt),
dt + INTERVAL '1 day', AGE(a, b), DATEDIFF(a, b)   -- MySQL
```

---

## 11. DDL and DML

```sql
CREATE TABLE t (
    id      SERIAL PRIMARY KEY,
    name    VARCHAR(100) NOT NULL,
    dept_id INT REFERENCES dept(id) ON DELETE SET NULL,
    created TIMESTAMP DEFAULT NOW(),
    CONSTRAINT uq_name UNIQUE (name)
);
CREATE INDEX idx_dept ON t(dept_id);
ALTER TABLE t ADD COLUMN age INT;

INSERT INTO t (name, dept_id) VALUES ('A', 1), ('B', 2);
UPDATE t SET name = 'X' WHERE id = 1;
DELETE FROM t WHERE id = 1;
```

`DELETE` is DML and transactional; `TRUNCATE` is DDL, faster, usually not rollback-able, and resets identity counters; `DROP` removes the table itself.

---

## 12. Dialect differences that bite

| Task | PostgreSQL | MySQL | SQL Server | Oracle |
|---|---|---|---|---|
| Row limit | `LIMIT n` | `LIMIT n` | `TOP n` / `OFFSET … FETCH` | `FETCH FIRST n ROWS ONLY` |
| String concat | `\|\|` or `CONCAT` | `CONCAT` | `+` | `\|\|` |
| Current time | `NOW()` | `NOW()` | `GETDATE()` | `SYSDATE` |
| Date part | `EXTRACT`/`DATE_TRUNC` | `YEAR()`, `DATE_FORMAT` | `DATEPART` | `EXTRACT` |
| String agg | `STRING_AGG` | `GROUP_CONCAT` | `STRING_AGG` | `LISTAGG` |
| Auto increment | `SERIAL` | `AUTO_INCREMENT` | `IDENTITY` | sequence |
| NULL and `\|\|` | result is NULL | `CONCAT` gives NULL | — | **treats NULL as empty** |

**In an OA, state your dialect assumption** if the question does not name one. Most graders accept standard SQL, and window functions are supported everywhere modern.
