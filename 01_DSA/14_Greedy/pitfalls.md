# Greedy — Pitfalls

## The fundamental risk
- **Assuming a greedy works because it passes the samples.** Greedy failures are silent: the code is short, runs fast and returns a plausible wrong answer. Always construct one adversarial case by hand.
- Not having a counterexample ready when the interviewer asks "why does that work?". Being unable to justify it reads worse than choosing DP.

## Sorting key
- Sorting by **start** for "maximum non-overlapping intervals" — a single long early interval then blocks everything. Sort by **end**.
- Sorting by **end** for "merge overlapping intervals" — merging requires start order.
- Sorting by the wrong component of a tuple, or forgetting that Python sorts tuples lexicographically and the tie-break matters.
- Forgetting to sort at all.

## Boundary semantics
- Treating touching intervals `[1,2]` and `[2,3]` as overlapping when the problem says they are not (or the reverse). Ask, or state your assumption.
- `>` versus `>=` in the compatibility test — this single character decides the arrow-bursting answer.

## Specific problems
- Gas Station: returning the index without first checking `sum(gas) >= sum(cost)`.
- Jump Game: not checking `i > reach` before using `nums[i]`, so you index past an unreachable position.
- Candy: doing a single pass, which satisfies only one direction of the constraint.
- Partition Labels: extending the block on the current character only, instead of on the maximum last-occurrence seen so far.
- Task Scheduler: forgetting that the answer is at least `len(tasks)` when there are enough distinct tasks to fill the idle slots.

## Greedy where DP is required
- Coin change with arbitrary denominations — {1,3,4} for 6 gives 3 coins greedily, 2 optimally.
- 0/1 knapsack by value/weight ratio — fine for the fractional version, wrong for the 0/1 version.
- Longest increasing subsequence by always taking the next larger element.
- Any problem where a choice forecloses a better future option you cannot see yet.

## Complexity
- Claiming O(n) when the sort makes it O(n log n).
- Using a heap where sorting once is enough, or vice versa.
