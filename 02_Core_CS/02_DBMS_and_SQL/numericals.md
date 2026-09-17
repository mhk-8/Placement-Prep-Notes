# DBMS & SQL — Worked Problems

> Two kinds of "numerical" dominate DBMS OAs: **FD / normalisation** questions, which are mechanical once you own attribute closure, and **SQL query writing**, which is the single most directly-tested skill in this folder.

---

## Part 1 — Functional dependencies

### N1. Attribute closure

**Q.** R(A, B, C, D, E), F = {A→B, BC→D, E→C, D→A}. Compute A⁺, E⁺ and (AE)⁺.

**Procedure:** start with the attribute set; repeatedly scan F for any FD whose left side is already contained; add its right side; stop when nothing changes.

```
A+ : {A} → A→B → {A,B}. No FD has LHS inside {A,B}.          A+ = {A,B}
E+ : {E} → E→C → {E,C}. BC→D needs B. No more.               E+ = {C,E}
AE+: {A,E} → A→B → {A,B,E} → E→C → {A,B,C,E}
              → BC→D → {A,B,C,D,E}                            AE+ = all
```
Since (AE)⁺ = R, **AE is a super key**.

---

### N2. Finding all candidate keys

**Q.** Same R and F. Find every candidate key.

**Step 1 — classify the attributes.**

| Attribute | Appears on LHS | Appears on RHS |
|---|---|---|
| A | yes (A→B) | yes (D→A) |
| B | yes (BC→D) | yes (A→B) |
| C | yes (BC→D) | yes (E→C) |
| D | yes (D→A) | yes (BC→D) |
| E | yes (E→C) | **no** |

**E appears only on the left, so E is in every candidate key.**

**Step 2 — start from E and grow.**
```
E+  = {C,E}                    not a key
AE+ = all                      → AE is a key
BE+ : {B,E} → E→C → {B,C,E} → BC→D → {B,C,D,E} → D→A → all
                               → BE is a key
DE+ : {D,E} → D→A → {A,D,E} → A→B → {A,B,D,E} → E→C → all
                               → DE is a key
CE+ = {C,E}                    not a key
```
**Step 3 — check minimality.** E alone is not a key, and none of A, B, D alone is (each closure omits E). So all three are minimal.

**Candidate keys: AE, BE, DE.**
**Prime attributes: A, B, D, E. Non-prime: C.**

---

### N3. Determining the highest normal form

**Q.** Same R, F. What is the highest normal form R satisfies?

**BCNF?** Check every FD's left side for super-key status.
`A→B`: A⁺ = {A,B} ≠ R → **A is not a super key → BCNF violated.**

**3NF?** For each FD, either LHS is a super key or RHS is prime.
`A→B`: A not a super key, but B **is** prime ✔
`BC→D`: BC⁺ = {B,C,D,A} — missing E, so not a super key; D is prime ✔
`E→C`: E not a super key; **C is not prime** ✘ → **3NF violated.**

**2NF?** A partial dependency is a non-prime attribute determined by a *proper subset* of a candidate key. E is a proper subset of the key AE, and `E→C` with C non-prime → **partial dependency → 2NF violated.**

**Answer: R is in 1NF only.**

**The procedure to reuse:** check BCNF first (one condition), then 3NF, then 2NF — each failure tells you exactly which FD is the culprit.

---

### N4. 3NF but not BCNF — the canonical example

**Q.** R(A, B, C) with F = {AB→C, C→A}. Highest normal form?

Candidate keys: (AB)⁺ = {A,B,C} ✔ and (BC)⁺ = {B,C,A} ✔. So **AB and BC are both candidate keys**, and every attribute (A, B, C) is prime.

**3NF:** `AB→C` — AB is a super key ✔. `C→A` — C is not a super key (C⁺ = {C,A}), but A **is prime** ✔. → **in 3NF.**
**BCNF:** `C→A` with C not a super key → **violated.**

**Answer: 3NF but not BCNF.** Memorise this three-attribute example; it answers a whole family of questions.

---

### N5. Lossless join and dependency preservation

**Q.** R(A,B,C,D), F = {A→B, B→C, C→D}. Is the decomposition R1(A,B), R2(B,C,D) lossless? Dependency preserving?

**Lossless test:** a two-way decomposition is lossless iff the common attributes determine at least one fragment.
```
R1 ∩ R2 = {B}
B+ = {B, C, D} ⊇ R2(B,C,D)   ✔
```
**Lossless.**

**Dependency preservation:** every FD must be checkable within a single fragment.
```
A→B  : both in R1  ✔
B→C  : both in R2  ✔
C→D  : both in R2  ✔
```
**Dependency preserving.**

**Contrast:** decomposing into R1(A,B), R2(A,C,D) gives R1 ∩ R2 = {A}, and A⁺ = {A,B,C,D} ⊇ R1 — still lossless — but `B→C` is now split across fragments and is **not preserved**.

---

## Part 2 — SQL

Schema used throughout:

```sql
Employee(emp_id PK, name, salary, dept_id FK, manager_id FK→Employee, hire_date)
Department(dept_id PK, dept_name, location)
Sales(sale_id PK, emp_id FK, amount, sale_date)
```

---

### S1. Nth highest salary

**Q.** Find the 3rd highest **distinct** salary.

```sql
-- Portable, no window functions
SELECT DISTINCT salary
FROM   Employee e1
WHERE  3 - 1 = (SELECT COUNT(DISTINCT e2.salary)
                FROM   Employee e2
                WHERE  e2.salary > e1.salary);
```
```sql
-- Window-function version (preferred when available)
SELECT salary FROM (
    SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM   Employee
) t
WHERE rnk = 3;
```
**Why `DENSE_RANK` and not `RANK`:** ties should not consume rank positions when the question says *distinct* salary. Using `ROW_NUMBER` is wrong for the same reason. Reading "distinct" in the question and choosing the matching ranking function is the entire point of this problem.

---

### S2. Second highest, handling the empty case

```sql
SELECT MAX(salary) AS second_highest
FROM   Employee
WHERE  salary < (SELECT MAX(salary) FROM Employee);
```
`MAX` over an empty set returns NULL rather than no rows, which is usually what the problem wants. `LIMIT 1 OFFSET 1` returns **no row** instead of NULL — check which the question expects.

---

### S3. Employees earning more than their manager

```sql
SELECT e.name AS employee
FROM   Employee e
JOIN   Employee m ON e.manager_id = m.emp_id
WHERE  e.salary > m.salary;
```
A self-join with two aliases. Employees with no manager are correctly excluded by the inner join.

---

### S4. Highest-paid employee per department

```sql
SELECT dept_id, name, salary
FROM (
    SELECT dept_id, name, salary,
           RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rnk
    FROM   Employee
) t
WHERE rnk = 1;
```
`RANK` (not `ROW_NUMBER`) so that a department with two equally-top earners returns both. The pre-window-function form is a correlated subquery against `MAX(salary)` grouped by department — know both, and say why the window version is cheaper (one pass instead of one subquery per row).

---

### S5. Departments with more than 5 employees and average salary above 50000

```sql
SELECT   d.dept_name, COUNT(*) AS headcount, AVG(e.salary) AS avg_salary
FROM     Employee e
JOIN     Department d ON e.dept_id = d.dept_id
GROUP BY d.dept_id, d.dept_name
HAVING   COUNT(*) > 5 AND AVG(e.salary) > 50000;
```
Aggregate conditions belong in `HAVING`; row conditions belong in `WHERE`. Grouping by `d.dept_id` as well as the name protects against two departments sharing a name.

---

### S6. Find duplicate rows

```sql
SELECT   name, COUNT(*) AS cnt
FROM     Employee
GROUP BY name
HAVING   COUNT(*) > 1;
```
To **delete** duplicates while keeping the lowest id:
```sql
DELETE FROM Employee
WHERE emp_id NOT IN (SELECT MIN(emp_id) FROM Employee GROUP BY name);
```

---

### S7. Departments with no employees (anti-join)

```sql
SELECT d.dept_name
FROM   Department d
LEFT   JOIN Employee e ON d.dept_id = e.dept_id
WHERE  e.emp_id IS NULL;
```
```sql
-- Equivalent, and safe against NULLs
SELECT dept_name FROM Department d
WHERE NOT EXISTS (SELECT 1 FROM Employee e WHERE e.dept_id = d.dept_id);
```
**Do not** write `WHERE dept_id NOT IN (SELECT dept_id FROM Employee)` — if any employee has a NULL `dept_id`, the comparison becomes UNKNOWN and the query returns **zero rows**. This is the most commonly failed SQL OA question.

---

### S8. Running total of sales per employee

```sql
SELECT emp_id, sale_date, amount,
       SUM(amount) OVER (PARTITION BY emp_id
                         ORDER BY sale_date
                         ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS running_total
FROM   Sales;
```

---

### S9. Month-over-month growth

```sql
WITH monthly AS (
    SELECT DATE_TRUNC('month', sale_date) AS mth, SUM(amount) AS total
    FROM   Sales
    GROUP  BY 1
)
SELECT mth, total,
       LAG(total) OVER (ORDER BY mth) AS prev_total,
       ROUND(100.0 * (total - LAG(total) OVER (ORDER BY mth))
             / NULLIF(LAG(total) OVER (ORDER BY mth), 0), 2) AS pct_growth
FROM   monthly;
```
`NULLIF(x, 0)` guards against division by zero — an interviewer will look for it.

---

### S10. Consecutive records

**Q.** Find employees who made a sale on three or more consecutive days.

```sql
WITH d AS (
    SELECT DISTINCT emp_id, sale_date FROM Sales
),
grp AS (
    SELECT emp_id, sale_date,
           sale_date - (ROW_NUMBER() OVER (PARTITION BY emp_id ORDER BY sale_date))::int AS grp_key
    FROM   d
)
SELECT   emp_id, MIN(sale_date) AS start_date, COUNT(*) AS streak
FROM     grp
GROUP BY emp_id, grp_key
HAVING   COUNT(*) >= 3;
```
**The trick:** for consecutive dates, `date − row_number` is constant within a run. This "gaps and islands" idiom solves the whole family of consecutive-record questions and is worth memorising outright.

---

### S11. Pivot without a PIVOT clause

```sql
SELECT dept_id,
       SUM(CASE WHEN EXTRACT(YEAR FROM hire_date) = 2025 THEN 1 ELSE 0 END) AS hired_2025,
       SUM(CASE WHEN EXTRACT(YEAR FROM hire_date) = 2026 THEN 1 ELSE 0 END) AS hired_2026
FROM   Employee
GROUP  BY dept_id;
```
Conditional aggregation — `SUM(CASE WHEN … )` — is the portable pivot and appears constantly.

---

### S12. Reading a query's output shape first

Before writing any SQL, answer three questions:
1. **How many rows should come back?** One overall → a plain aggregate. One per group → `GROUP BY`. One per input row → a window function.
2. **Which table drives the row count?** That one goes first in the `FROM`, and everything optional joins to it with `LEFT JOIN`.
3. **Is the filter on rows or on groups?** Rows → `WHERE`. Groups → `HAVING`.

Answering these three turns most OA SQL questions into transcription.

---

## Practice set

1. R(A,B,C,D) with F = {AB→C, C→D, D→A}. Find all candidate keys and the highest normal form.
2. R(A,B,C,D,E) with F = {A→BC, CD→E, B→D, E→A}. Is A a candidate key?
3. Is R1(A,B), R2(A,C) a lossless decomposition of R(A,B,C) under F = {A→B, A→C}?
4. Write SQL for the department with the highest average salary.
5. Write SQL to find employees hired in the same month as their manager.
6. Write SQL for the top 2 earners per department, including ties.

<details><summary>Answers</summary>

1. (AB)⁺ = {A,B,C,D} ✔; (BC)⁺ = {B,C,D,A} ✔; (BD)⁺ = {B,D,A,C} ✔. **Candidate keys: AB, BC, BD.** Every attribute is prime, so 3NF holds; `C→D` has a non-super-key left side, so **3NF but not BCNF**.
2. A⁺ = {A,B,C} → B→D → {A,B,C,D} → CD→E → all. **Yes, A is a candidate key** (and minimal, since it is a single attribute).
3. R1 ∩ R2 = {A}, and A⁺ = {A,B,C} ⊇ both fragments. **Lossless.**
4. `SELECT dept_id FROM Employee GROUP BY dept_id ORDER BY AVG(salary) DESC LIMIT 1;` — for ties, rank the grouped result instead.
5. `SELECT e.name FROM Employee e JOIN Employee m ON e.manager_id = m.emp_id WHERE EXTRACT(MONTH FROM e.hire_date) = EXTRACT(MONTH FROM m.hire_date) AND EXTRACT(YEAR FROM e.hire_date) = EXTRACT(YEAR FROM m.hire_date);`
6. Wrap `DENSE_RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC)` in a subquery and filter `rnk <= 2` — `DENSE_RANK`, not `ROW_NUMBER`, so ties are included.

</details>
