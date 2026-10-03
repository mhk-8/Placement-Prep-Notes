
# 09 — Cheatsheets

> **One page each.** If it does not fit on one page, it is a note, not a cheatsheet.
> These exist for one job: **recall under time pressure.** They are not for learning a topic
> the first time — that is what `01_DSA` through `07_Interviews` are for.

---

## 1. The rule ⭐

> In the final two weeks you read **only** this folder and `10_Mistake_Log_and_Revision`.
> **No new topics.** Anything you cannot do by then, you will not learn in time; convert that
> time into reliability on what you already know.

The night before any test or interview, you read exactly one file: **`night-before.md`**.

---

## 2. What is here

| File | Covers | Read before |
|---|---|---|
| **`night-before.md`** ⭐⭐⭐ | The 2-page distillation: logistics, constraint table, pattern triggers, code reflexes, OA strategy, interview protocol, your pitch, core-CS and ML rapid fire | **Every** OA and interview |
| `complexity-table.md` | Constraint→complexity map, every DS operation, sorting, graphs, DP, strings, number theory, amortised analysis | Any coding round |
| `dsa-patterns-one-pager.md` ⭐ | Trigger → pattern → template. Blind-writable templates. DP recipe. Greedy proof rules. The traps | Any coding round |
| `cpp-stl.md` | Containers + complexities, algorithms, comparators, C++ semantics, output-prediction traps | C++ OA, Adobe, Qualcomm, NVIDIA |
| `python-stdlib.md` | Fast I/O, collections, heapq, bisect, itertools/functools, language semantics, NumPy minimum | ML rounds, scripting |
| `java-collections.md` | Collection hierarchy, HashMap internals, idioms, core-Java answers, streams | Oracle, Amazon, Java-only OAs |
| `sql-one-pager.md` ⭐ | Logical execution order, joins, window functions, NULL semantics, the classic queries, indexing | Arcesium, Goldman, Oracle, Flipkart |
| `os-one-pager.md` | Process/thread, scheduling, synchronisation, deadlock, memory, paging, cache hierarchy | Every core-CS round |
| `dbms-one-pager.md` | ACID, isolation levels, concurrency control, normalisation, indexing, query processing, CAP | Every core-CS round |
| `cn-one-pager.md` | Layers, TCP vs UDP, handshake, congestion control, subnetting, URL walkthrough, HTTP | Every core-CS round |
| `oop-one-pager.md` | Four pillars, SOLID, relationships, design patterns, **machine-coding playbook** | Flipkart/Walmart, Sprinklr, any LLD |
| `system-design-numbers.md` | Latency numbers, QPS/storage estimation, capacity rules, building blocks, trade-offs, GPU numbers | Any HLD round |
| `ml-metrics.md` | Confusion matrix, threshold-free metrics, regression, ranking, NLP/CV, validation and **leakage** | Every ML round |
| `ml-algorithms-table.md` | Master comparison table, bias-variance, regularisation, optimisers, architectures, derivations | Every ML round |
| **`gpu-cuda-one-pager.md`** ⭐ | Execution model, memory hierarchy, coalescing/divergence/occupancy/bank conflicts, kernel patterns, roofline, your project soundbites | **NVIDIA, Qualcomm, Samsung, ML-infra** |
| `formulas-aptitude.md` | Every aptitude formula and shortcut, nothing else | Aptitude OAs |

---

## 3. How to use them

```
BEFORE an OA        : night-before.md §2-§5, then formulas-aptitude.md or the relevant language sheet
BEFORE an interview : night-before.md in full, then the company folder in 08_Company_Wise
WEEKLY in revision  : one sheet per day, cover the right column and recite the left
AFTER a mistake     : patch the sheet, and log the mistake in 10_Mistake_Log_and_Revision ⭐
```

**Every sheet ends with Recall questions.** Use those, not re-reading. Re-reading feels
productive and is nearly worthless; retrieval is what builds recall. ⭐

> **These are living files.** When you get something wrong in a mock or a real round, the fix
> goes in here the same day. A cheatsheet that has not changed in a month is not being used.

---

## 4. Which sheet matters most for which target

| Target | Sheets in priority order |
|---|---|
| **NVIDIA, Qualcomm, Samsung R&D** | `gpu-cuda-one-pager` ⭐ · `cpp-stl` · `os-one-pager` · `dsa-patterns` |
| **Amazon, Microsoft, Google** | `dsa-patterns` ⭐ · `complexity-table` · `os`/`dbms`/`cn` · `system-design-numbers` |
| **Arcesium, Goldman Sachs** | `sql-one-pager` ⭐ · `dsa-patterns` · `ml-metrics` (statistics) · `dbms` |
| **Flipkart, Walmart, Sprinklr** | `oop-one-pager` ⭐ (machine coding) · `dsa-patterns` · `java-collections` |
| **Oracle** | `java-collections` ⭐ · `dbms` · `sql-one-pager` |
| **Adobe** | `cpp-stl` ⭐ (output prediction) · `dsa-patterns` · `oop-one-pager` |
| **ML / ML-infra roles** | `ml-metrics` ⭐ · `ml-algorithms-table` · `gpu-cuda-one-pager` · `python-stdlib` |
| **Any aptitude OA** | `formulas-aptitude` ⭐ |

---

## 5. Honest caveat ⚠️

These sheets were drafted comprehensively rather than built up from your own study, which is the
opposite of the ideal. Treat them as a **strong first draft to correct**, not as finished work:

```
□ Delete anything you already know cold — it is wasting space on the page
□ Add the things YOU keep forgetting, in your own words ⭐
□ Fill the blanks in night-before.md §7 (your pitch, your numbers) — those cannot be pre-written
□ Fill the project numbers in gpu-cuda-one-pager.md §9
```

A cheatsheet in someone else's words is a reference. A cheatsheet in your own words is recall.
Convert these over the next two weeks.
