# Greedy — Flashcards

## Questions

1. What two properties must hold for a greedy algorithm to be correct?
2. Describe the exchange argument using interval scheduling.
3. When do you sort by end time, and when by start time?
4. Give the standard counterexample showing greedy coin change fails.
5. Why is fractional knapsack greedy-safe while 0/1 knapsack is not?
6. Explain the Gas Station greedy, including the feasibility check.
7. Why is Jump Game II a BFS in disguise?
8. Why does Candy require two passes?
9. What is the greedy rule for Partition Labels?
10. What is the rule for Task Scheduler, and what is the lower bound on the answer?
11. Name three greedy algorithms that require a heap.
12. How do you decide between greedy and DP under time pressure?
13. Why are greedy failures more dangerous than DP failures?
14. What is the greedy rule for Reorganize String, and when is it impossible?
15. Are Dijkstra and Prim greedy algorithms? Justify.

---

## Answers

1. The greedy choice property (a locally optimal choice is consistent with some global optimum) and optimal substructure (the remainder is the same problem, smaller).
2. Take an optimal solution differing from greedy at the first choice; swapping in the earliest-ending interval frees at least as much time and conflicts with nothing more, so the solution stays optimal. Induct on the remaining steps.
3. End time for "maximum number of non-overlapping intervals" and its complements; start time for merging overlapping intervals.
4. Denominations {1, 3, 4} and amount 6: greedy gives 4+1+1 (three coins), optimum is 3+3 (two).
5. Fractional items can be split, so taking the best ratio never wastes capacity. With indivisible items, a high-ratio item can consume space a better combination needed.
6. If total gas < total cost, no solution exists. Otherwise sweep once, resetting the start index to `i+1` whenever the running tank goes negative — no station in the failed span can be a valid start.
7. Each "level" is the set of indices reachable with the same number of jumps; extending `cur_end` to `farthest` is exactly expanding the BFS frontier by one layer.
8. Each child must exceed its lower-rated left neighbour *and* its lower-rated right neighbour. One pass can only enforce one direction; the second pass takes the maximum of the two requirements.
9. Extend the current block's end to the maximum last-occurrence of every character seen so far; close the block when the index reaches that end.
10. Schedule the most frequent task first and fill idle slots with others. The answer is at least `len(tasks)`, and at least `(maxFreq−1)·(n+1) + countOfMaxFreqTasks`.
11. Task Scheduler / Reorganize String (max-heap by frequency), Meeting Rooms II (min-heap of end times), and Dijkstra or Prim.
12. If the exchange argument comes in about thirty seconds, go greedy; otherwise write the DP. A correct slower solution beats an elegant wrong one.
13. A wrong DP is usually too slow or obviously wrong; a wrong greedy is short, fast and returns a plausible answer that passes the samples.
14. Repeatedly emit the most frequent remaining character that is not the one just used, holding the previous character back for one step. Impossible when the maximum frequency exceeds ⌈n/2⌉.
15. Yes. Dijkstra greedily finalises the nearest unvisited vertex, justified by non-negative weights; Prim greedily adds the cheapest crossing edge, justified by the cut property.
