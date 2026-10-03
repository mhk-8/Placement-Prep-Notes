
# Oracle — Prep Plan, Question Bank and Debriefs

---

## 1. Topic priorities ⭐⭐

| Priority | Topic | Source | Status |
|---|---|---|---|
| **P1** | **SQL query writing** ⭐⭐⭐ — joins, group by, window functions, Nth highest | `../../06_Online_Assessments/05_MCQ_Core_CS_Banks/DBMS.md` | ☐ |
| **P1** | **DBMS theory** — normalisation, ACID, isolation, indexing, transactions, 2PL | same + `../../02_Core_CS/04_DBMS/` | ☐ |
| **P1** | DSA Medium — arrays, strings, hashing, trees, graphs | `../../01_DSA/` | ☐ |
| **P2** | **The Java decision** ⚠️ — framing, or the sprint | `00-process-and-oa.md` §4 | ☐ |
| **P2** | OS fundamentals | `../../06_Online_Assessments/05_MCQ_Core_CS_Banks/Operating_Systems.md` | ☐ |
| **P2** | DP ⚠️ | `../../01_DSA/` | ☐ |
| **P3** | Aptitude | `../../06_Online_Assessments/02_Aptitude_and_Quant/` | ☐ |
| **P3** | Query optimisation concepts ⭐ (for Server Tech) | see §3 | ☐ |

---

## 2. The two-week plan ⭐⭐⭐

| Date | Task | Done |
|---|---|---|
| D-14 | Research protocol; **ask WHICH TEAM** ⭐; decide the Java approach (Option A or B) | ☐ |
| D-13 | **SQL: joins, group by/having, the logical evaluation order** | ☐ |
| D-12 | **SQL: window functions — ROW_NUMBER, RANK, DENSE_RANK, LAG/LEAD, running totals** ⭐ | ☐ |
| D-11 | **SQL: Nth highest salary three ways; find/delete duplicates; self joins** | ☐ |
| D-10 | DBMS theory: normalisation worked example; isolation-level table from memory | ☐ |
| D-9 | DBMS: indexing — clustered vs non-clustered, B+ tree, when an index is NOT used ⭐ | ☐ |
| D-8 | DSA: 3 Medium problems | ☐ |
| D-7 | Transactions, deadlock, 2-phase locking; DELETE vs TRUNCATE vs DROP | ☐ |
| D-6 | DSA: 3 more. DP ⚠️ | ☐ |
| D-5 | Java: either rehearse the honest framing aloud, or day 1 of the sprint | ☐ |
| D-4 | **Mock: SQL query writing + 1 coding problem** ⭐ | ☐ |
| D-3 | OS MCQ bank; query optimisation concepts if targeting Server Tech | ☐ |
| D-2 | Points-to project: the static-analysis ↔ query-optimisation parallel, rehearsed ⭐; 2 questions | ☐ |
| D-1 | Pre-interview routine only. Nothing new ⚠️ | ☐ |

---

## 3. Seeded question bank

### SQL ⭐⭐⭐ — expect to WRITE queries, not just answer MCQs
```
□ Nth highest salary — three solutions, and why DENSE_RANK differs from RANK on ties  ⭐⭐
□ Second highest salary without LIMIT
□ Employees earning more than their manager (self join)
□ Department-wise highest earner (window function or correlated subquery)
□ Find duplicates; delete duplicates keeping the lowest id
□ Running total / cumulative sum (SUM OVER with a frame)
□ Month-over-month growth (LAG)
□ Top N per group  ⭐ the classic window-function question
□ Employees with no matching rows in another table (LEFT JOIN ... IS NULL, or NOT EXISTS)
□ Pivot with conditional aggregation (SUM(CASE WHEN ...))
□ The logical evaluation order, and its two consequences:
    - WHERE cannot use an aggregate (use HAVING)
    - ORDER BY can use a SELECT alias; WHERE usually cannot
□ EXISTS vs IN vs JOIN — semantics with NULLs, and performance
□ All join types and what each produces when values are NULL
```

### DBMS theory ⭐⭐
```
□ Normalisation 1NF → 2NF → 3NF → BCNF with a worked decomposition
□ 3NF vs BCNF: BCNF is always lossless but may not preserve dependencies  ⭐
□ ACID, and the mechanism behind each (undo log, constraints, locking/MVCC, write-ahead log)
□ Isolation levels and the anomaly each prevents — the full table
□ Clustered vs non-clustered index; one clustered index per table
□ B+ tree vs hash index; why B+ trees for databases (fan-out + linked leaves)  ⭐
□ When an index is NOT used — function on the column, leading wildcard, implicit conversion,
  large result fraction, leftmost prefix violated  ⭐⭐
□ Transactions, deadlock detection (wait-for graph), 2-phase locking, strict 2PL
□ Views, materialised views, triggers, stored procedures, cursors
□ DELETE vs TRUNCATE vs DROP — five dimensions
□ Keys: super, candidate, primary, alternate, foreign, composite, surrogate
```

### Query optimisation ⭐ (Server Technologies track)
```
□ What does an execution plan show? Nested loop vs hash join vs merge join
□ Cardinality estimation, and why bad estimates cause bad plans
□ Cost-based vs rule-based optimisation
□ Index selection; covering indexes
⭐ THE PARALLEL TO DRAW: "Query optimisation and static analysis are the same kind of problem —
  reasoning about a program's behaviour without running it, with a cost model deciding
  everything. That's what my M.Tech project does for pointer behaviour."
```

### Java ⚠️ (if the team is Java-heavy)
```
□ Collections: ArrayList vs LinkedList vs HashMap vs TreeMap vs HashSet — complexity each
□ HashMap internals: buckets, hashCode, collision handling, treeification beyond 8 entries
□ equals / hashCode contract  ⭐ and the HashMap bug when you override only equals
□ == vs equals; the String pool and why == sometimes "works"
□ Generics and type erasure; wildcards
□ Concurrency: synchronized, volatile, ExecutorService, ConcurrentHashMap, AtomicInteger
□ JVM memory model: heap/stack/metaspace; garbage collection generations
□ final / static / abstract; interfaces with default methods
□ Checked vs unchecked exceptions
□ String immutability; StringBuilder
```

### DSA (Medium)
```
Arrays, strings, hashing, trees, graphs, DP, heaps, intervals, LRU cache
```

### Project deep dive
```
- "Explain points-to analysis."  ⭐ then draw the query-optimisation parallel
- "How is this like what a query optimiser does?"  ⭐⭐ the question you want
- "How do you know your analysis is correct?"
- "Tell me about the IR project's indexing and ranking."
```

### Behavioural
```
- Why Oracle? Which team, and why?
- How strong is your Java, honestly?  ⚠️ have the framing ready
- Why is your M.Tech CGPA 7.5?
- Why Mechanical → CS?
```

---

## 5. Debriefs ⭐⭐⭐

Full template: `../../07_Interviews/06_Post_Interview_Debriefs/Debrief_Template.md`

### Round 1 — `<date>` — `<type>`
```
INTERVIEWER        :
STRUCTURE / TIMING :
QUESTIONS ASKED (verbatim):
  1.
  2.
  3.
CODING PROBLEM     :
  My approach     :
  Correct approach (write it out NOW):
WHAT I FUMBLED, AND THE RIGHT ANSWER:
  1.
  2.
TONE / STYLE FOR THE NEXT ROUND:
OUTCOME            :
THREE ACTIONS:
  1.
  2.
  3.
```

### Round 2 — `<date>` — `<type>`
```
(same structure)
```

### Round 3 — `<date>` — `<type>`
```
(same structure)
```

---

## 6. Questions I collected myself ⭐⭐⭐

| Date | Source (senior / my attempt) | Round | Question | Topic |
|---|---|---|---|---|
| | | | | |
| | | | | |
| | | | | |

---

## 7. Corrections to make to `00-process-and-oa.md`

```
□
□
```


---

## 8. The 30-second pre-interview refresh ⭐

```
□ SQL logical order: FROM → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT
□ WHERE cannot use an aggregate; ORDER BY can use a SELECT alias
□ Nth highest: correlated subquery / DENSE_RANK / LIMIT OFFSET. DENSE_RANK vs RANK on ties
□ Isolation: READ UNCOMMITTED → dirty; READ COMMITTED → non-repeatable; REPEATABLE READ → phantom
□ Index not used: function on column, leading %, type conversion, leftmost prefix violated
□ B+ tree: records only in leaves, leaves linked → high fan-out + fast range scans
□ My Java framing: "deep on Java SEMANTICS via static analysis; C++ and Python are my fastest"
□ My pitch: static analysis ↔ query optimisation — same problem shape
```
