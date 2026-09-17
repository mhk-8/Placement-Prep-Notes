# Greedy — Concepts

## 1. Core idea in 3 lines
A greedy algorithm makes the locally best choice at each step and never reconsiders. When it works the code is five lines; when it does not, it produces a confidently wrong answer that passes the sample tests. The entire skill is **knowing which case you are in**, which means having a proof — or a counterexample — within thirty seconds.

---

## 2. When greedy is valid

Two properties must hold:

1. **Greedy choice property** — a globally optimal solution can be built by making the locally optimal choice at each step.
2. **Optimal substructure** — after the greedy choice, what remains is the same problem on a smaller input.

The second is shared with DP. The first is what separates them: greedy commits, DP explores.

### Proof techniques

**Exchange argument** (the one to use in interviews): take any optimal solution that differs from the greedy one at the first differing choice; show you can swap in the greedy choice without making the solution worse; therefore an optimal solution exists that agrees with greedy at that step; induct.

Example for interval scheduling: if an optimal schedule's first meeting ends later than the greedy's earliest-ending meeting, replace it with the greedy choice — it frees at least as much time and conflicts with nothing else, so the solution stays optimal and no smaller.

**Staying ahead:** show that after k steps the greedy solution is at least as good as any other on the relevant measure.

**Cut/matroid arguments:** the formal backing for MST and scheduling problems.

---

## 3. The canonical greedy problems

| Problem | Greedy rule | Why it works |
|---|---|---|
| Activity selection / max non-overlapping intervals | Sort by **end** time, take the earliest-ending compatible | Finishing earliest leaves the most room |
| Minimum arrows to burst balloons | Sort by end, shoot at the first end | Same argument |
| Non-overlapping intervals (min removals) | Sort by end, count kept | Complement of the above |
| Fractional knapsack | Sort by value/weight ratio | Divisibility means no commitment is ever wasted |
| Huffman coding | Repeatedly merge the two least frequent | Least frequent symbols should be deepest |
| Jump Game | Track the furthest reachable index | Reachability is monotone |
| Jump Game II | Extend the level boundary — a BFS in disguise | Each "level" is a jump count |
| Gas Station | If total gas ≥ total cost a solution exists; start after the point of minimum running balance | A single pass suffices |
| Partition Labels | Extend the partition to the last occurrence of every char seen | Forced boundaries |
| Task Scheduler | Schedule the most frequent task first | The most frequent task dictates the idle structure |
| Candy | Two passes, left to right then right to left | Each constraint direction handled once |
| Meeting Rooms II | Sort starts and ends; sweep | Counting concurrency |

**The two sorts to never confuse:** sort by **end time** for "maximum number of non-overlapping intervals"; sort by **start time** for "merge overlapping intervals". Getting this backwards is the single most common greedy error.

---

## 4. Greedy vs DP — deciding fast

| Question | Greedy | DP |
|---|---|---|
| Can you prove the exchange argument quickly? | ✅ | ❌ |
| Does a local choice ever need revisiting? | no | yes |
| Complexity | usually O(n log n) from the sort | usually O(n²) or more |
| Risk profile | wrong silently | slow but correct |

**Under time pressure, a correct O(n²) DP beats an unproven O(n log n) greedy.** If you are not sure, write the DP. In an interview, say exactly that: "I think a greedy on end times works; let me sanity-check it against a case where a long interval starts early — yes, that is why sorting by start fails."

**The classic counterexample to keep ready:** coin change with denominations {1, 3, 4} and amount 6. Greedy takes 4+1+1 = 3 coins; the optimum is 3+3 = 2 coins. This is why coin change is DP, while the same problem with canonical currency systems happens to be greedy-safe.

---

## 5. Greedy plus a data structure

Greedy often needs "the best remaining option" repeatedly, which is a heap:
- Task Scheduler, Reorganize String — max-heap by frequency
- Meeting Rooms II — min-heap of end times
- IPO — two heaps
- Minimum Cost to Connect Sticks — min-heap
- Dijkstra and Prim are greedy algorithms with a heap

Or sorting plus a single sweep, which is the intervals family (`16`).

---

## 6. Recall questions

1. What two properties must hold for greedy to be correct?
2. Describe the exchange argument, using interval scheduling.
3. When do you sort by end time versus start time?
4. Give the standard counterexample showing coin change is not greedy.
5. Why is fractional knapsack greedy while 0/1 knapsack is not?
6. Explain the Gas Station greedy and why one pass suffices.
7. Why is Jump Game II really a BFS?
8. Why does Candy need two passes?
9. How do you decide between greedy and DP in an OA?
10. Name three greedy algorithms that need a heap.
