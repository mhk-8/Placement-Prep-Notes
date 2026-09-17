# My Recurring Mistakes

> **This file is the most valuable one in `01_DSA`, and it starts almost empty on purpose.**
> The generic traps below are the starting set. The lines that will actually save you marks are the ones you add after each mock, promoted from `10_Mistake_Log_and_Revision/recurring-patterns.md` once a mistake has appeared three times.

---

## Pre-submit checklist

Run every time. Aimed at the `careless bug` failure mode, which is usually the largest single category.

- [ ] Empty input · single element · two elements
- [ ] All identical · all negative · already sorted · reverse sorted
- [ ] Integer overflow — does any intermediate exceed 2³¹?
- [ ] Off-by-one in every loop bound and every `mid` update
- [ ] Indices vs values — stacks and deques hold **indices**
- [ ] Mutating a collection while iterating it
- [ ] Appended a reference where a copy was needed (`path[:]`)
- [ ] `return` inside the loop that belongs after it
- [ ] Dry-run the given example by hand, line by line

---

## Generic traps, by pattern

**Sliding window** — shrinking in the wrong direction (longest shrinks while *invalid*, shortest while *valid*).
**Prefix + hash map** — forgetting `seen[0] = 1`.
**Binary search** — `lo = mid` without rounding `mid` up, causing an infinite loop.
**Binary search on answer** — a lower bound that is too low (`lo = max(w)`, not 1, for ship capacity).
**Monotonic stack** — storing values instead of indices; computing the width before the pop.
**Monotonic deque** — `<` instead of `<=` on the back pop.
**Heap** — using a max-heap for "k largest"; forgetting the tie-breaker in Python tuples.
**Backtracking** — `res.append(path)` instead of `path[:]`; missing the un-choose.
**BFS** — marking visited at dequeue instead of enqueue.
**Directed cycle detection** — a single `visited` set instead of grey/black.
**Dijkstra** — negative weights; missing the stale-entry skip.
**DP 0/1 knapsack** — ascending capacity loop, silently allowing reuse.
**DP coin counting** — loop order swapped, counting permutations instead of combinations.
**Greedy intervals** — sorting by start when the greedy needs end.
**Merge intervals** — `out[-1][1] = e` instead of `max(out[-1][1], e)`.
**Python** — `[[0]*C]*R` aliases the rows.
**C++** — `mid = (lo+hi)/2` overflow; `mp[key]` inserting on a membership test; a comparator returning `<=`.

---

## My own list

> Add a line only after the same mistake has cost you marks **three times**. Date it. Write the *rule*, not the anecdote.

| Date added | The mistake | The rule I now follow | Times since |
|---|---|---|---|
| | | | |
| | | | |
| | | | |
| | | | |
| | | | |
| | | | |
| | | | |
| | | | |

---

## My failure-mode split

> From `00_Start_Here/Trackers/mock-scores.md`. Update at each phase gate. It tells you what to *do*, not just what went wrong.

| Failure mode | W1–4 | W5–8 | W9–10 | The fix if this dominates |
|---|---|---|---|---|
| Concept gap | | | | Targeted study of the weak patterns |
| Careless bug | | | | This checklist, every submission |
| Time mismanagement | | | | Enforce caps; read all questions first |
| Misread the question | | | | Restate in your own words before coding |
| Panic | | | | More timed simulations, not more theory |

Four of those five are fixed by discipline, not by more content. If your split is 70% careless bugs, another month of DP will not move your score.
