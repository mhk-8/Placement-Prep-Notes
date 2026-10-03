
# Arcesium — Prep Plan, Question Bank and Debriefs

---

## 1. Topic priorities ⭐⭐

| Priority | Topic | Source | Status |
|---|---|---|---|
| **P1** | DSA Medium-Hard — arrays, hashing, trees, graphs, **DP** ⚠️ | `../../01_DSA/` | ☐ |
| **P1** | **SQL** — joins, group by, window functions, Nth highest | `../../06_Online_Assessments/05_MCQ_Core_CS_Banks/DBMS.md` | ☐ |
| **P1** | DBMS theory — normalisation, ACID, isolation levels, indexing | same | ☐ |
| **P2** | OS and networks fundamentals | `../../06_Online_Assessments/05_MCQ_Core_CS_Banks/` | ☐ |
| **P2** | Complexity analysis, stated precisely under questioning | `../../07_Interviews/01_Technical_Round_Prep/00-Interview_Protocol.md` | ☐ |
| **P2** | Probability basics — conditional, expected value, Bayes | `../../05_AI_ML/02_Probability_and_Statistics/` | ☐ |
| **P3** | Java fundamentals, **if** you decide to close that gap ⚠️ | see `00-process-and-oa.md` §5 | ☐ |

---

## 2. The two-week plan ⭐⭐⭐

| Date | Task | Done |
|---|---|---|
| D-14 | Research protocol; **check the CGPA cut-off** ⚠️; confirm the role and the Java weighting | ☐ |
| D-13 | DSA: 3 Medium problems. Hashing and arrays | ☐ |
| D-12 | SQL drills: joins, group by/having, window functions, Nth highest three ways ⭐ | ☐ |
| D-11 | DBMS theory: normalisation worked example, isolation-level table from memory | ☐ |
| D-10 | DSA: 3 problems. Trees | ☐ |
| D-9 | DP ⚠️: knapsack family, LIS, edit distance | ☐ |
| D-8 | OS MCQ bank; networks MCQ bank | ☐ |
| D-7 | DSA: 3 problems. Graphs | ☐ |
| D-6 | Probability: conditional, Bayes with natural frequencies, expected value with linearity | ☐ |
| D-5 | IR project: 5-minute version, **statistical-validation emphasis** ⭐ | ☐ |
| D-4 | **Mock: DSA + fundamentals, with the interviewer told to challenge correct answers** ⭐⭐ | ☐ |
| D-3 | DP: 3 more problems. More DSA volume | ☐ |
| D-2 | Read about post-trade; prepare 2 questions; rehearse the CGPA answer | ☐ |
| D-1 | Pre-interview routine only. Nothing new ⚠️ | ☐ |

⭐ **The D-4 mock is the Arcesium-specific preparation.** Tell your mock partner to say "are you
sure?" when you are right. Practise re-verifying calmly instead of folding.

---

## 3. Seeded question bank

### DSA (Medium to Medium-Hard)
```
- Arrays with a constraint twist; prefix sums; subarray sum = k (prefix + hash map)
- Sliding window — longest substring variants, min window
- Hashing — group anagrams, top-K with tie-breaking, first unique
- Trees — validate BST, LCA, diameter, level order, serialise
- Graphs — topological sort, cycle detection, Dijkstra, union-find
- DP — knapsack family, LIS, edit distance, coin change, stock problems ⚠️
- Heaps — running median (two heaps), merge K sorted, top-K
- Intervals — merge, meeting rooms, minimum platforms
- Design — LRU cache, rate limiter, min stack
```

### SQL ⭐⭐ (expect query writing, not just MCQs)
```
- Nth highest salary — know three solutions (correlated subquery, DENSE_RANK, LIMIT/OFFSET)
  ⚠️ and know why DENSE_RANK differs from RANK on ties
- Group by with having; the order of logical evaluation
  (FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY)
- Window functions: ROW_NUMBER, RANK, DENSE_RANK, LAG/LEAD, running totals
- Self joins — employee/manager hierarchies
- Find duplicates; delete duplicates keeping one
- All join types and what each produces with NULLs
- Why an index would NOT be used (function on column, leading wildcard, leftmost prefix)
```

### DBMS theory
```
- Normalisation 1NF → BCNF, with a worked decomposition
- ACID; isolation levels and the anomaly each prevents
- Clustered vs non-clustered index; B+ tree vs hash
- Transactions, deadlock detection, 2-phase locking
- DELETE vs TRUNCATE vs DROP
```

### OS and networks
```
OS      : process vs thread, deadlock, scheduling, virtual memory, thrashing
Networks: TCP vs UDP, three-way handshake, HTTP status classes, what happens when you type a URL
```

### Probability (light but present)
```
- Conditional probability and Bayes (the medical-test / factory-defect family)
- Expected value; linearity of expectation
- Simple combinatorics
- "At least one" via the complement
```

### Project deep dive
```
- "Walk me through the IR project."  ⭐ lead here
- "Why a Wilcoxon signed-rank test rather than a t-test?"  ⭐⭐ the question you want
- "What does Cohen's d of 0.57 tell you that the p-value doesn't?"
- "Why did BM25 beat the VSM baseline?"
- "How do you know your points-to analysis is correct?"
- "How would you rebuild the IR system today?" (hybrid retrieval + cross-encoder reranker)
```

### Behavioural / fit
```
- Why Arcesium? Why fintech?
- Why is your M.Tech CGPA 7.5?  ⚠️
- Why Mechanical → CS?
- Tell me about a time you found an error in your own work  ⭐ strong fit for their culture
- A time you disagreed with someone technically
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
□ "Are you sure?" → re-verify CALMLY, confirm with reasoning or find the error. Do not fold.
□ SQL logical order: FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY
  (so WHERE cannot use an aggregate, and ORDER BY can use a SELECT alias)
□ Nth highest salary: three ways; DENSE_RANK vs RANK on ties
□ Isolation levels: READ UNCOMMITTED → dirty; READ COMMITTED → non-repeatable;
  REPEATABLE READ → phantom; SERIALIZABLE → none
□ My lead project here is the IR ENGINE, for the statistical rigour
□ "p says the effect is real; d says whether it's worth anything"
```
