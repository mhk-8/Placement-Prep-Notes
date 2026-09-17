# Problem Tracker

> One row per problem attempted. Filled during the **daily close-out**, not from memory a week later.
> Purpose: reveal which patterns are slow, which are fragile, and what is due for re-solving. It is a diagnostic instrument — curating it to look good makes it useless.

## Column meanings

| Column | Meaning |
|---|---|
| **Date** | Attempt date |
| **Problem** | Name + source (LC 239, GFG, company OA) |
| **Pattern** | From `01_DSA/99_Patterns_Playbook` — the *one* pattern that unlocked it |
| **Diff** | E / M / H |
| **Time** | Minutes actually spent |
| **Status** | `✅` solved unaided · `💡` solved after a hint · `📖` read the editorial · `❌` failed |
| **Root cause** | Only if not ✅: `concept` / `bug` / `time` / `misread` |
| **R1 / R2 / R3** | Re-solve dates (D+3, D+7, D+21) — tick when re-solved cold |
| **Note** | One line. The insight, not a summary |

**"Unaided" means unaided.** No tags, no hints, no glance at the editorial's first line.

## Re-solve policy

- `✅` under the time cap → no re-solve needed; the pattern goes into the topic tracker instead.
- `✅` over the time cap → re-solve at D+7.
- `💡` or `📖` → re-solve at **D+3, D+7, D+21**, cold, from scratch. A problem you read the solution to is not learned until you have reproduced it without it.
- `❌` → re-solve at D+1 after studying the pattern, then D+3, D+7, D+21.

A problem only leaves this tracker when you have solved it unaided, under the cap, at least 14 days after first seeing it.

---

## Log

| Date | Problem | Pattern | Diff | Time | Status | Root cause | R1 | R2 | R3 | Note |
|---|---|---|---|---|---|---|---|---|---|---|
| 2026-09-20 | *example:* LC 239 Sliding Window Maximum | Monotonic deque | M | 41 | 📖 | concept | ☐ | ☐ | ☐ | Didn't see that the deque holds *indices*, not values |
| | | | | | | | ☐ | ☐ | ☐ | |
| | | | | | | | ☐ | ☐ | ☐ | |
| | | | | | | | ☐ | ☐ | ☐ | |
| | | | | | | | ☐ | ☐ | ☐ | |
| | | | | | | | ☐ | ☐ | ☐ | |
| | | | | | | | ☐ | ☐ | ☐ | |
| | | | | | | | ☐ | ☐ | ☐ | |
| | | | | | | | ☐ | ☐ | ☐ | |
| | | | | | | | ☐ | ☐ | ☐ | |

> Add rows freely. Start a new month-heading (`## October`) when the table gets long; do not split into separate files.

---

## Weekly roll-up

> Filled every Sunday. This is the part you actually act on.

| Week | Attempted | ✅ unaided | 💡 hint | 📖 editorial | ❌ | Unaided % | Median time (M) | Weakest pattern | Action for next week |
|---|---|---|---|---|---|---|---|---|---|
| W1 | | | | | | | | | |
| W2 | | | | | | | | | |
| W3 | | | | | | | | | |
| W4 | | | | | | | | | |
| W5 | | | | | | | | | |
| W6 | | | | | | | | | |
| W7 | | | | | | | | | |
| W8 | | | | | | | | | |
| W9 | | | | | | | | | |
| W10 | | | | | | | | | |

### Benchmarks to steer by

| Phase | Target unaided % on mediums | Target median time on a medium |
|---|---|---|
| End of Phase A (W4) | 40% | ≤ 40 min |
| End of Phase B (W8) | 65% | ≤ 30 min |
| End of Phase C (W10) | 75%+ | ≤ 25 min |

If unaided % is flat across three weeks, the problem is not volume — it is that you are reading solutions too early. Tighten rule 4 in `rules.md`.

---

## Pattern coverage

> Tick when you have solved **5+ problems** in a pattern, of which at least 3 unaided.

☐ Prefix sums ☐ Hashing ☐ Two pointers ☐ Sliding window ☐ Binary search ☐ BS on answer
☐ Sorting + greedy ☐ Backtracking ☐ Linked list ☐ Monotonic stack ☐ Monotonic deque
☐ Tree traversal ☐ Tree DFS/recursion ☐ BFS/level order ☐ BST ☐ Heap / top-K
☐ Trie ☐ Graph BFS/DFS ☐ Topological sort ☐ Union-Find ☐ Dijkstra ☐ MST
☐ 1-D DP ☐ Knapsack ☐ Grid DP ☐ String DP ☐ Interval DP ☐ Bitmask DP
☐ Greedy/exchange ☐ Bit manipulation ☐ Intervals/sweep ☐ Fenwick/segment tree
