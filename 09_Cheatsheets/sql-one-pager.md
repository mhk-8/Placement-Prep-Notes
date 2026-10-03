
# SQL — One Pager

> **Use:** Arcesium, Goldman Sachs, Oracle, Flipkart, and almost every OA with a "DBMS/SQL"
> section. SQL questions are high-yield because they are *learnable in a weekend* and many
> candidates skip them. ⭐

---

## 1. Logical execution order ⭐⭐⭐ (the single most asked SQL concept)

```
 1. FROM / JOIN      → build the working set
 2. WHERE            → filter ROWS          (cannot see aggregates, cannot use SELECT aliases)
 3. GROUP BY         → collapse into groups
 4. HAVING           → filter GROUPS        (can see aggregates ⭐)
 5. SELECT           → compute expressions, aliases become defined here
 6. DISTINCT
 7. ORDER BY         → can use SELECT aliases ⭐
 8. LIMIT / OFFSET
```

> **Two consequences to be able to state:**
> 1. `WHERE COUNT(*) > 3` is invalid — use `HAVING`. `WHERE` runs before grouping.
> 2. `SELECT salary*12 AS annual ... WHERE annual > 100000` is invalid in most engines, but
>    `ORDER BY annual` is valid, because `ORDER BY` runs after `SELECT`.

---

## 2. Joins

| Join | Returns |
|---|---|
| `INNER JOIN` | Only matching rows from both |
| `LEFT [OUTER] JOIN` | All left rows; NULLs where no right match |
| `RIGHT JOIN` | Mirror of LEFT |
| `FULL OUTER JOIN` | All rows from both; NULLs where unmatched |
| `CROSS JOIN` | Cartesian product, n × m |
| `SELF JOIN` | Table joined to itself (employee → manager) ⭐ |
| `NATURAL JOIN` | Joins on all same-named columns — ⚠️ avoid, fragile |

```sql
-- anti-join: rows in A with no match in B  ⭐ the standard "find missing" pattern
SELECT a.* FROM a LEFT JOIN b ON a.id = b.a_id WHERE b.a_id IS NULL;

-- self join: employees earning more than their manager
SELECT e.name FROM emp e JOIN emp m ON e.mgr_id = m.id WHERE e.salary > m.salary;
```

> ⚠️ **`ON` vs `WHERE` on an outer join.** A predicate on the right table in `ON` filters
> *before* the join (keeps unmatched left rows); the same predicate in `WHERE` filters *after*,
> silently turning your LEFT JOIN into an INNER JOIN. This is a favourite interview trap. ⭐

---

## 3. Aggregates and `GROUP BY`

```sql
COUNT(*)          -- counts rows, including those with NULLs
COUNT(col)        -- ⚠️ ignores NULLs
COUNT(DISTINCT col)
SUM · AVG · MIN · MAX · STDDEV · VAR_POP
AVG(col)          -- ⚠️ ignores NULLs; AVG(COALESCE(col,0)) treats them as zero. Different answers.

SELECT dept, COUNT(*) AS n, AVG(salary) AS avg_sal
FROM emp
WHERE active = 1          -- row filter, before grouping
GROUP BY dept
HAVING COUNT(*) >= 5      -- group filter, after grouping
ORDER BY avg_sal DESC;
```

> **Rule:** every column in `SELECT` must be in `GROUP BY` or inside an aggregate.
> (MySQL historically allowed otherwise — `ONLY_FULL_GROUP_BY` now forbids it.)

---

## 4. Window functions ⭐⭐⭐ (the highest-value topic; expect at least one)

```sql
func() OVER (PARTITION BY col ORDER BY col2 ROWS BETWEEN ... AND ...)
```

| Function | Behaviour on ties |
|---|---|
| `ROW_NUMBER()` | 1,2,3,4 — arbitrary tiebreak |
| `RANK()` | 1,2,2,4 — gaps after ties ⭐ |
| `DENSE_RANK()` | 1,2,2,3 — no gaps ⭐ |
| `NTILE(4)` | quartile buckets |
| `LAG(col, 1)` / `LEAD(col, 1)` | previous / next row's value ⭐ |
| `FIRST_VALUE` / `LAST_VALUE` / `NTH_VALUE` | |
| `SUM() OVER (ORDER BY d)` | running total ⭐ |

```sql
-- 2nd highest salary per department  ⭐ the canonical question
SELECT * FROM (
  SELECT e.*, DENSE_RANK() OVER (PARTITION BY dept ORDER BY salary DESC) AS rk
  FROM emp e
) t WHERE rk = 2;

-- running total and month-over-month change
SELECT month, revenue,
       SUM(revenue)  OVER (ORDER BY month) AS running_total,
       revenue - LAG(revenue) OVER (ORDER BY month) AS mom_change
FROM monthly;

-- top 3 per group
SELECT * FROM (SELECT p.*, ROW_NUMBER() OVER (PARTITION BY cat ORDER BY sales DESC) rn
               FROM prod p) t WHERE rn <= 3;
```

> ⚠️ **Window functions cannot appear in `WHERE`** — they are computed after it. Wrap in a
> subquery or CTE and filter outside. Stating this unprompted signals real familiarity. ⭐

### Frame clauses
```sql
ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW     -- default for ORDER BY: running aggregate
ROWS BETWEEN 2 PRECEDING AND CURRENT ROW             -- 3-row moving average
RANGE BETWEEN INTERVAL '7' DAY PRECEDING AND CURRENT ROW
```

---

## 5. Subqueries and CTEs

```sql
-- scalar / IN / EXISTS
SELECT * FROM emp WHERE salary > (SELECT AVG(salary) FROM emp);
SELECT * FROM emp WHERE dept IN (SELECT id FROM dept WHERE region = 'APAC');
SELECT * FROM emp e WHERE EXISTS (SELECT 1 FROM sale s WHERE s.emp_id = e.id);

-- ⚠️ NOT IN with NULLs returns NO ROWS. NOT EXISTS is NULL-safe. Prefer NOT EXISTS. ⭐

-- CTE
WITH dept_avg AS (SELECT dept, AVG(salary) a FROM emp GROUP BY dept)
SELECT e.name FROM emp e JOIN dept_avg d ON e.dept = d.dept WHERE e.salary > d.a;

-- recursive CTE: org hierarchy  ⭐
WITH RECURSIVE tree AS (
  SELECT id, name, mgr_id, 1 AS lvl FROM emp WHERE mgr_id IS NULL
  UNION ALL
  SELECT e.id, e.name, e.mgr_id, t.lvl + 1 FROM emp e JOIN tree t ON e.mgr_id = t.id
) SELECT * FROM tree ORDER BY lvl;
```

---

## 6. NULL semantics ⚠️ (a whole class of trick questions)

```
NULL = NULL          → NULL (not true).  Use IS NULL / IS NOT NULL.
NULL + 5             → NULL
COUNT(col)           → skips NULLs; COUNT(*) does not
SUM/AVG              → skip NULLs (AVG divides by the non-null count)
x NOT IN (1, NULL)   → never TRUE  ⚠️ the NOT IN trap
ORDER BY             → engine-specific; use NULLS FIRST / NULLS LAST
COALESCE(a,b,c)      → first non-null
NULLIF(a,b)          → NULL if a = b, else a
UNIQUE constraint    → usually permits multiple NULLs
```

---

## 7. Set operations and dedupe

```sql
UNION          -- distinct, sorts/hashes → slower
UNION ALL      -- keeps duplicates, cheaper ⭐ default choice unless you need dedupe
INTERSECT · EXCEPT / MINUS

-- delete duplicates keeping the lowest id ⭐ classic question
DELETE FROM t WHERE id NOT IN (SELECT MIN(id) FROM t GROUP BY email);
-- or
WITH d AS (SELECT id, ROW_NUMBER() OVER (PARTITION BY email ORDER BY id) rn FROM t)
DELETE FROM t WHERE id IN (SELECT id FROM d WHERE rn > 1);
```

---

## 8. The classic questions — have these ready ⭐⭐

```sql
-- Nth highest salary (no window functions allowed)
SELECT MIN(salary) FROM (SELECT DISTINCT salary FROM emp ORDER BY salary DESC LIMIT N) t;

-- Department-wise highest paid employee
SELECT dept, name, salary FROM (
  SELECT e.*, RANK() OVER (PARTITION BY dept ORDER BY salary DESC) r FROM emp e) t WHERE r = 1;

-- Consecutive: numbers appearing at least 3 times in a row
SELECT DISTINCT a.num FROM logs a JOIN logs b ON a.id = b.id - 1
                                  JOIN logs c ON a.id = c.id - 2
WHERE a.num = b.num AND b.num = c.num;

-- Employees who never made a sale (anti-join)
SELECT e.name FROM emp e LEFT JOIN sale s ON e.id = s.emp_id WHERE s.id IS NULL;

-- Pivot without PIVOT
SELECT dept,
  SUM(CASE WHEN gender='M' THEN 1 ELSE 0 END) AS males,
  SUM(CASE WHEN gender='F' THEN 1 ELSE 0 END) AS females
FROM emp GROUP BY dept;

-- Median (engine-independent)
SELECT AVG(salary) FROM (
  SELECT salary, ROW_NUMBER() OVER (ORDER BY salary) rn, COUNT(*) OVER () c FROM emp) t
WHERE rn IN (FLOOR((c+1)/2), CEIL((c+1)/2));

-- Gaps in a sequence of dates / ids
SELECT id + 1 AS missing_from FROM t WHERE id + 1 NOT IN (SELECT id FROM t);
```

---

## 9. Indexes, keys and performance ⭐

| Term | Meaning |
|---|---|
| Clustered index | Table rows stored in index order; **one per table** (InnoDB: the PK) |
| Non-clustered / secondary | Separate B⁺-tree holding key → row locator |
| Covering index | Contains every column the query needs → no table lookup ⭐ |
| Composite index | `(a, b)` helps `WHERE a=?`, `WHERE a=? AND b=?`, `ORDER BY a,b`; **not** `WHERE b=?` alone ⭐ leftmost-prefix rule |
| Why B⁺-tree not hash | Range scans and ordered traversal; hash gives only equality |
| Index kills | `WHERE YEAR(d) = 2024`, `WHERE col LIKE '%x'`, implicit type casts — all prevent index use ⚠️ |
| Selectivity | Low-cardinality columns (a boolean flag) rarely benefit from an index |
| Index cost | Slows `INSERT/UPDATE/DELETE`, consumes space. Not free |
| `EXPLAIN` | Read it: full table scan vs index scan vs index seek; row estimates |

### Query tuning checklist
```
□ EXPLAIN first — do not guess
□ Is there an index on every JOIN column and every selective WHERE column?
□ SELECT only the columns you need (enables covering indexes)
□ Avoid functions on indexed columns in predicates
□ EXISTS over IN for large correlated subqueries; UNION ALL over UNION
□ Filter early — push predicates into the CTE/subquery
□ Beware the N+1 pattern from application code
```

---

## 10. Transactions and isolation ⭐⭐

**ACID:** Atomicity · Consistency · Isolation · Durability.

| Isolation level | Dirty read | Non-repeatable read | Phantom read |
|---|---|---|---|
| READ UNCOMMITTED | ✅ possible | ✅ | ✅ |
| READ COMMITTED | ❌ | ✅ | ✅ |
| REPEATABLE READ | ❌ | ❌ | ✅ (MySQL InnoDB blocks it via gap locks) |
| SERIALIZABLE | ❌ | ❌ | ❌ |

```
Dirty read            : read another transaction's uncommitted change
Non-repeatable read   : same row read twice gives different values
Phantom read          : same RANGE query returns a different set of rows
Default: PostgreSQL / Oracle / SQL Server = READ COMMITTED; MySQL InnoDB = REPEATABLE READ ⭐
```

**Normal forms:** 1NF atomic values · 2NF no partial dependency on part of a composite key ·
3NF no transitive dependency · BCNF every determinant is a superkey.
Denormalise deliberately for read-heavy workloads, and say so.

---

## Recall questions
1. State the logical execution order, then explain why `WHERE COUNT(*) > 3` fails.
2. A predicate on the right table: in `ON` vs in `WHERE` of a LEFT JOIN. What differs?
3. `RANK` vs `DENSE_RANK` vs `ROW_NUMBER` on the values 10, 20, 20, 30.
4. Why is `NOT IN` dangerous with NULLs, and what do you use instead?
5. Why can a window function not be used in `WHERE`?
6. You have an index on `(a, b)`. Which of `WHERE a=1`, `WHERE b=2`, `WHERE a=1 AND b=2` use it?
7. Write the 2nd-highest-salary-per-department query from memory.
8. Which isolation level permits phantom reads but not dirty reads?
