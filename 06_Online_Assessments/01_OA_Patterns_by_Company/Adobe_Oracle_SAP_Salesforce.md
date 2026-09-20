
# Adobe, Oracle, SAP, Salesforce — Product-Company OAs

> **Accuracy note.** OA formats change every recruiting season, and companies run different
> papers for different campuses, roles and dates. Treat everything below as the *shape* of the
> test — what it measures and how to prepare — and verify the exact duration, section count and
> cutoff against this year's campus notice and last year's seniors before you sit the test.


This group sits between the mass recruiters and the Google/Amazon tier: **real DSA, plus a
substantial CS-fundamentals MCQ section**. The MCQ section is what catches candidates who prepared
only LeetCode.

---

## 1. The shared shape

```
Aptitude (light)  →  CS Fundamentals MCQ (heavy)  →  Coding (1-3 problems, Easy-Medium)
```

**Key insight ⭐⭐:** for these companies, your core-CS MCQ score often matters as much as your
coding score. `05_MCQ_Core_CS_Banks/` in this folder is the direct preparation for this section,
and `02_Core_CS/` in the vault is the depth behind it.

---

## 2. Adobe

| Item | Typical |
|---|---|
| Platform | HackerRank / Adobe's portal |
| Structure | Aptitude + **CS MCQ (OS, DBMS, OOP, C/C++ output)** + **2 coding problems** |
| Duration | ~90 minutes |
| Coding band | LeetCode Easy-Medium, occasionally Medium-Hard |
| Distinctive | ⭐ **C/C++ output-prediction MCQs** are an Adobe signature |

### C/C++ output prediction — what they actually ask
```
- pointer arithmetic and array-pointer duality:  *(arr + i)  vs  arr[i]
- sizeof on arrays vs pointers, and on structs (padding!)
- operator precedence and associativity:  i++ + ++i, a && b || c
- pre/post increment inside expressions and function arguments
- static variables retaining value across calls
- storage classes: auto, static, extern, register
- integer promotion and signed/unsigned comparison pitfalls
- dangling pointers, memory leaks, double free
- virtual functions, vtable dispatch, virtual destructors
- object slicing on assignment of a derived to a base by value
- constructor/destructor order, including with inheritance and members
- const correctness, const pointer vs pointer to const
- string literal modification (undefined behaviour)
- struct/union size differences
```

⚠️ Many of these are **undefined behaviour** in the standard (e.g. `i = i++ + ++i`). Adobe still
asks them with an "expected" answer from a common compiler. Learn the common answer *and* know it
is UB — that nuance is worth mentioning in the interview, not in the MCQ.

### Adobe coding topics
Arrays, strings, hash maps, trees, DP, and a recurring taste for **matrix and geometry-flavoured
problems** (image processing heritage): rotate a matrix, spiral traversal, flood fill, area
calculations, overlapping rectangles.

---

## 3. Oracle

| Item | Typical |
|---|---|
| Platform | HackerRank / Oracle's own |
| Structure | Aptitude + **Technical MCQ heavily weighted to DBMS and SQL** ⭐ + Coding |
| Duration | 90-120 minutes |
| Distinctive | ⭐⭐ **SQL query writing and DBMS theory carry real weight** |

### Oracle's SQL/DBMS emphasis ⭐⭐
Expect both MCQs *and* query-writing. Prepare:
```
- SELECT with WHERE / GROUP BY / HAVING / ORDER BY — and the exact order of evaluation
- All join types, and what each produces with NULLs
- Subqueries: correlated vs non-correlated; EXISTS vs IN vs JOIN performance
- Aggregate functions and the GROUP BY rule
- Window functions: ROW_NUMBER, RANK, DENSE_RANK, LAG/LEAD, running totals ⭐
- Nth highest salary — the canonical question, know three different solutions
- Normalisation: 1NF → 2NF → 3NF → BCNF, with the dependency that each removes
- Transactions and ACID; isolation levels and the anomaly each prevents
- Indexes: B-tree vs hash, clustered vs non-clustered, when an index is NOT used
- Keys: primary, candidate, super, foreign, composite
- Constraints, triggers, views (and updatable views), stored procedures
- PL/SQL basics for some roles
```

Also expect **Java** questions (collections, concurrency, JVM memory model, garbage collection) for
Oracle's Java-centric teams.

### Oracle coding topics
Standard DSA at Easy-Medium: arrays, strings, hash maps, sorting, trees, occasionally a graph.
Cleanliness and complexity annotation matter.

---

## 4. SAP

| Item | Typical |
|---|---|
| Platform | HackerRank / SAP's own |
| Structure | Aptitude + Technical MCQ + Coding; some tracks add a **design/scenario** section |
| Distinctive | Enterprise-software flavour: data modelling, ABAP for some roles, cloud/integration concepts |

Prepare: standard DSA, OOP and design principles, databases, and basic distributed-systems
vocabulary (APIs, microservices, message queues, idempotency). SAP interviews weight **design
thinking and clarity of communication** more than raw algorithmic speed.

---

## 5. Salesforce

| Item | Typical |
|---|---|
| Platform | HackerRank / Salesforce's own |
| Structure | Coding (2-3) + CS MCQ; some tracks include a take-home or a debugging exercise |
| Distinctive | Strong emphasis on **object-oriented design** and clean, testable code |

Prepare: DSA at Medium, OOP and SOLID, design patterns (see `04_System_Design/04_Design_Patterns/`),
databases, and API/web fundamentals (REST verbs, idempotency, status codes, auth flows).

---

## 6. The combined study list

| Priority | What | Where in the vault |
|---|---|---|
| **P1** | DBMS + SQL (especially for Oracle) | `05_MCQ_Core_CS_Banks/DBMS.md`, `02_Core_CS/04_DBMS/`, `03_Languages/SQL/` |
| **P1** | OOP concepts + output prediction | `05_MCQ_Core_CS_Banks/OOPs.md`, `03_Languages/CPP/gotchas.md` |
| **P1** | DSA Easy-Medium, 2 problems in 60 min | `01_DSA/` |
| **P2** | Operating systems | `05_MCQ_Core_CS_Banks/Operating_Systems.md` |
| **P2** | Computer networks | `05_MCQ_Core_CS_Banks/Computer_Networks.md` |
| **P2** | C/C++ output prediction | `03_Languages/CPP/gotchas.md` |
| **P3** | Aptitude | `02_Aptitude_and_Quant/` |
| **P3** | Design patterns and OOD | `04_System_Design/02_LLD_OOD/` |

---

## 7. Four-week plan

| Week | Focus |
|---|---|
| 1 | DBMS + SQL end to end, including window functions and the Nth-highest-salary family |
| 2 | OS + CN MCQ banks; C/C++ output prediction drills (30 snippets) |
| 3 | OOP, SOLID, design patterns; DSA Medium problems daily |
| 4 | Two full timed mocks (MCQ + coding together), logged in `06_Timed_Mock_Logs/` |

---

## 8. Test-day checklist

- [ ] MCQ section: do not overthink; 45-60 seconds per question, flag and move
- [ ] For output-prediction: trace on paper, do not eyeball
- [ ] SQL: read the schema carefully, watch for NULL behaviour and duplicate rows
- [ ] Coding: brute force first, then optimise; annotate complexity
- [ ] Leave five minutes to revisit flagged MCQs
