# Patterns Playbook — How to Use This Folder

> **This is the OA-day folder.** On the night before a test you read this and `09_Cheatsheets/night-before.md`, and nothing else.

## What lives here

| File | Purpose | When you read it |
|---|---|---|
| `concepts.md` | The triage procedure — how to spend the first five minutes | Before every mock and every real OA |
| `patterns.md` | 22 triggers, each with a one-line template summary | Night before; between OA questions |
| `problems-solved.md` | Your personal weak-pattern ledger | Every Sunday |
| `pitfalls.md` | Your own recurring mistakes, promoted from the mistake log | Before every submission |
| `flashcards.md` | Rapid recognition drill: statement → pattern, 60 of them | Daily, 5 minutes |

The rest of `01_DSA` is where you *learn*. This folder is where you *recall under pressure*, and the two need different formats.

**Build it incrementally.** The generic content is here; the parts that will actually save you are `pitfalls.md` and the weak-pattern ledger, and only you can write those. Add one line after every mock.

---

## The first five minutes of an OA

1. **Read every problem before writing any code.** Not skimming — reading, including the constraints. Two minutes.
2. **Rank them** by (confidence × marks) ÷ estimated effort. Write the order down.
3. **For each, note the constraint → complexity mapping** (`pattern-index.md` §1). n ≤ 20 means exponential; n ≤ 10⁵ means O(n log n).
4. **Name the pattern** for the one you will start with. If you cannot name it in 60 seconds, start with a different problem and come back.
5. **Then** open the editor.

Candidates who start coding at second 30 lose more marks than candidates who start at minute 5, every time.

---

## The per-problem procedure

1. **Restate the problem in your own words**, in one sentence. This single habit eliminates the `misread` failure mode, which accounts for a surprising share of lost marks.
2. **Read the constraints** → intended complexity.
3. **Find the trigger** in `patterns.md` → name the pattern.
4. **State the brute force** and its cost. This is your fallback submission.
5. **Write the approach in three comment lines** before any code.
6. **Code the template**, adapted.
7. **Run the pre-submit checklist** (`templates.md` §13).
8. **Submit.** Partial credit is real on most platforms — a brute force that passes small cases beats an unfinished optimum.

---

## The time rule

For a 90-minute, 3-problem OA:

| Minutes | What |
|---|---|
| 0–5 | Read all, rank, plan |
| 5–30 | Easiest problem |
| 30–60 | Second problem |
| 60–80 | Hardest — a brute force still scores |
| 80–90 | Fix failing edge cases; never start something new |

**Abandon rule:** if a problem has consumed 30% of total time with no working submission, leave it. The most common way a strong candidate fails an OA is sinking 50 minutes into one question and never reading the third.

---

## When you are stuck

In order, and each should take under two minutes:

1. **Re-read the constraints.** They usually name the intended complexity.
2. **Write out a tiny example by hand** — n = 3 or 4 — and solve it manually. Watch what your own brain does; that is often the algorithm.
3. **What would brute force do, and what does it repeat?** Repeated work → memoise (DP) or remember (hash map).
4. **Is the input sorted, or would sorting help?** → two pointers, binary search, greedy.
5. **Is there a monotone predicate?** → binary search on the answer.
6. **Is this a graph in disguise?** Grids, dependencies, transformations, equations.
7. **Scan `patterns.md` §2 top to bottom.** Twenty seconds.

If none of that lands within ten minutes, write the brute force, submit it, and move on.

---

## Recognising the disguises

| Looks like | Actually is |
|---|---|
| A grid | A graph (each cell is a vertex with 4 neighbours) |
| Word transformations | BFS on an implicit graph |
| Prerequisites, build order, rankings | Topological sort |
| "Minimum possible maximum" | Binary search on the answer |
| "Are x and y in the same group" over time | Union-Find |
| A running "best so far" over a stream | Heap, or monotonic deque |
| "First element to the left/right bigger than me" | Monotonic stack |
| Equations relating quantities | Weighted graph / DSU |
| Choices with future consequences | DP |
| "Enumerate all …" with n ≤ 20 | Backtracking or bitmask |
