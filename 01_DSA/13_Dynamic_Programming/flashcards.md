# Dynamic Programming — Flashcards

## Questions

1. What two properties must a problem have for DP to apply?
2. List the five steps of the method in order.
3. Why is memoised recursion the safer choice under OA time pressure?
4. Give the 0/1 knapsack recurrence and its space-optimised loop direction.
5. Why must the 0/1 capacity loop run descending?
6. Why does unbounded knapsack run ascending?
7. In coin counting, which loop order gives combinations and which gives permutations?
8. Give the LCS recurrence.
9. Give the edit distance recurrence and name the three operations.
10. How do you obtain the longest palindromic subsequence from LCS?
11. What is the key insight in Burst Balloons?
12. Describe the O(n log n) LIS and what `tails[k]` means.
13. Why is `tails` not itself a valid LIS?
14. When is bitmask DP appropriate and what is the TSP complexity?
15. How do you decide between greedy and DP?

---

## Answers

1. Optimal substructure (the optimum is composed of subproblem optima) and overlapping subproblems (the same subproblem is needed repeatedly).
2. Define the state; write the transition; fix the base cases; determine the computation order; locate the answer.
3. It mirrors the recurrence one-to-one, so a correct recurrence gives correct code, and the computation order is handled automatically by the call stack.
4. `dp[c] = max(dp[c], dp[c−w] + v)`, iterating capacity from W down to w.
5. Descending means `dp[c−w]` still holds the value from *before* this item was considered, so the item is used at most once.
6. Ascending means `dp[c−w]` may already include the current item, which is exactly the reuse that unbounded knapsack permits.
7. Coins in the outer loop counts combinations (order irrelevant); target in the outer loop counts permutations.
8. If `a[i−1] == b[j−1]` then `dp[i][j] = dp[i−1][j−1] + 1`, else `dp[i][j] = max(dp[i−1][j], dp[i][j−1])`.
9. Equal characters: `dp[i][j] = dp[i−1][j−1]`. Otherwise `1 + min(dp[i−1][j], dp[i][j−1], dp[i−1][j−1])` — delete, insert, replace.
10. It is the LCS of the string with its own reverse.
11. Choose which balloon is burst **last** in an interval rather than first; that keeps the two sides independent, since the boundaries `a[l]` and `a[r]` remain intact.
12. Maintain `tails`, where `tails[k]` is the smallest possible tail value of any increasing subsequence of length k+1; binary search each new element's insertion point and either extend or overwrite. The answer is `len(tails)`.
13. Its entries come from different subsequences at different times; only its length is meaningful. Reconstructing an actual LIS requires storing predecessor indices.
14. When n ≤ 20, so 2ⁿ states are affordable. TSP is O(2ⁿ · n²) time and O(2ⁿ · n) space.
15. If you can justify the local choice with an exchange argument in about 30 seconds, use greedy. Otherwise write the DP — a correct O(n²) beats an elegant wrong O(n).
