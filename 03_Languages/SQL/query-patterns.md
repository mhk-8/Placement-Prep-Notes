# SQL — Query Patterns

> The dozen shapes that cover most OA questions. Recognise the shape, then fill in the columns.

---

## 0. Decide the output shape first

Before writing anything, answer three questions:

1. **How many rows should come back?** One overall → a plain aggregate. One per group → `GROUP BY`. One per input row → a **window function**.
2. **Which table drives the row count?** That one goes first in `FROM`; everything optional attaches with `LEFT JOIN`.
3. **Is the filter on rows or on groups?** Rows → `WHERE`. Groups → `HAVING`.

Answering these turns most SQL questions into transcription.

---

## 1. Nth highest / top-N per group

```sql
-- Nth highest DISTINCT value
SELECT salary FROM (
    SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM employee
) t WHERE rnk = 3;

-- top N per group, ties included
SELECT * FROM (
    SELECT *, DENSE_RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rnk
    FROM employee
) t WHERE rnk <= 2;
```

**Choosing the ranking function is the whole question.** "Nth highest **distinct**" → `DENSE_RANK`. "The Nth **row**" → `ROW_NUMBER`. "Top N **including ties**" → `RANK` or `DENSE_RANK`.

---

## 2. Group-wise maximum without window functions

```sql
SELECT e.*
FROM   employee e
JOIN  (SELECT dept_id, MAX(salary) AS ms FROM employee GROUP BY dept_id) m
  ON   e.dept_id = m.dept_id AND e.salary = m.ms;
```
Know both this and the window version, and be able to say why the window version is cheaper: one pass instead of a second aggregate scan plus a join.

---

## 3. Self join — rows compared to other rows

```sql
-- employees earning more than their manager
SELECT e.name
FROM   employee e JOIN employee m ON e.manager_id = m.emp_id
WHERE  e.salary > m.salary;

-- consecutive rows (pre-window-function style)
SELECT a.id
FROM   logs a JOIN logs b ON b.id = a.id + 1 AND a.num = b.num;
```

---

## 4. Anti-join — "exists in A but not in B"

```sql
SELECT d.name
FROM   dept d LEFT JOIN employee e ON d.id = e.dept_id
WHERE  e.id IS NULL;
```
**Do not use `NOT IN`** when the subquery can return NULL — the comparison becomes UNKNOWN and the query returns zero rows. `NOT EXISTS` is always safe.

---

## 5. Duplicates

```sql
-- find them
SELECT email, COUNT(*) FROM person GROUP BY email HAVING COUNT(*) > 1;

-- delete them, keeping the lowest id
DELETE FROM person
WHERE id NOT IN (SELECT MIN(id) FROM person GROUP BY email);

-- or with a window function
DELETE FROM person WHERE id IN (
    SELECT id FROM (
        SELECT id, ROW_NUMBER() OVER (PARTITION BY email ORDER BY id) AS rn
        FROM person
    ) t WHERE rn > 1
);
```

---

## 6. Running totals and moving windows

```sql
SELECT id, dt, amt,
       SUM(amt) OVER (PARTITION BY id ORDER BY dt
                      ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS running,
       AVG(amt) OVER (PARTITION BY id ORDER BY dt
                      ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS moving_avg_3
FROM sales;
```
**Always write the `ROWS` frame explicitly.** The default frame is `RANGE`, which lumps tied ORDER BY values together and makes a "running" total jump at every tie.

---

## 7. Period-over-period comparison

```sql
WITH monthly AS (
    SELECT DATE_TRUNC('month', dt) AS m, SUM(amt) AS total
    FROM sales GROUP BY 1
)
SELECT m, total,
       LAG(total)  OVER (ORDER BY m) AS prev,
       ROUND(100.0 * (total - LAG(total) OVER (ORDER BY m))
             / NULLIF(LAG(total) OVER (ORDER BY m), 0), 2) AS pct_change
FROM monthly;
```
`NULLIF(x, 0)` guards the division — an interviewer will look for it.

---

## 8. Gaps and islands (consecutive runs)

```sql
WITH g AS (
    SELECT user_id, dt,
           dt - (ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY dt))::int AS grp
    FROM  (SELECT DISTINCT user_id, dt FROM activity) d
)
SELECT user_id, MIN(dt) AS start_dt, COUNT(*) AS streak
FROM   g
GROUP  BY user_id, grp
HAVING COUNT(*) >= 3;
```
**The trick:** for consecutive dates, `date − row_number` is constant within a run. Memorise this — it answers every "N consecutive days/logins/wins" question.

---

## 9. Pivot with conditional aggregation

```sql
SELECT dept,
       COUNT(*) FILTER (WHERE status = 'active')   AS active,   -- Postgres
       SUM(CASE WHEN status = 'left' THEN 1 ELSE 0 END) AS left_cnt  -- portable
FROM   employee GROUP BY dept;
```

---

## 10. Median and percentiles

```sql
SELECT PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY salary) AS median
FROM employee;

-- portable, using row numbers from both ends
SELECT AVG(salary) FROM (
    SELECT salary,
           ROW_NUMBER() OVER (ORDER BY salary)      AS a,
           COUNT(*)     OVER ()                     AS n
    FROM employee
) t WHERE a IN ((n + 1) / 2, (n + 2) / 2);
```

---

## 11. Hierarchies with a recursive CTE

```sql
WITH RECURSIVE sub AS (
    SELECT id, name, manager_id, 1 AS lvl FROM employee WHERE id = 1
    UNION ALL
    SELECT e.id, e.name, e.manager_id, s.lvl + 1
    FROM   employee e JOIN sub s ON e.manager_id = s.id
)
SELECT * FROM sub;
```
An anchor member, `UNION ALL`, and a recursive member that joins back to the CTE. Always bound the depth on real data, or a cycle loops forever.

---

## 12. Deduplicating a join fan-out

```sql
-- WRONG: each order row is multiplied by its item count, inflating the total
SELECT o.id, SUM(o.total) FROM orders o JOIN items i ON i.order_id = o.id GROUP BY o.id;

-- RIGHT: aggregate the many-side first
SELECT o.id, o.total, x.item_count
FROM   orders o
LEFT   JOIN (SELECT order_id, COUNT(*) AS item_count FROM items GROUP BY order_id) x
  ON   x.order_id = o.id;
```
**Join fan-out is the most common silent bug in analytical SQL.** If a `SUM` looks too large, count the rows before and after the join.

---

## 13. Reading an unfamiliar query

1. Find the innermost `FROM` and work outward.
2. Identify the grain — what does one row represent at each stage?
3. Check every join for fan-out (is the join key unique on both sides?).
4. Check every `LEFT JOIN` for a `WHERE` on the right table that turns it inner.
5. Check NULL handling in `NOT IN`, `<>` and aggregates.

Those five checks catch most bugs in production SQL, and they are exactly what a "review this query" interview question is testing.
