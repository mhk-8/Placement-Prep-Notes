# Dynamic Programming — Concepts

## 1. Core idea in 3 lines
DP applies when a problem has **optimal substructure** (the optimum is built from optima of subproblems) and **overlapping subproblems** (the same subproblem recurs). You then compute each subproblem once and reuse it, turning exponential recursion into polynomial work. Everything difficult about DP is choosing the state; the rest is mechanical.

---

## 2. The five-step method

Do these in order, on paper, before typing.

1. **Define the state.** One sentence: "`dp[i][j]` = the maximum value obtainable using the first i items with capacity j." If you cannot say it cleanly, the rest will not work.
2. **Write the transition.** How does a state depend on smaller states? This is the recurrence.
3. **Base cases.** The smallest states, answered directly.
4. **Order of computation.** Each state must be computed after everything it depends on. (Memoised recursion handles this for you — one reason it is the safer choice under time pressure.)
5. **Where is the answer?** `dp[n][W]`, or the max over a row, or a variable tracked during the fill. Not always the last cell.

**Say the state definition aloud in an interview.** Interviewers grade this more than the code.

---

## 3. Memoisation vs tabulation

| | Memoisation (top-down) | Tabulation (bottom-up) |
|---|---|---|
| Shape | Recursion + cache | Loops over a table |
| Order | Automatic | You must get it right |
| Computes | Only reachable states | All states |
| Overhead | Call stack, recursion depth risk | None |
| Space optimisation | Hard | Easy (rolling rows) |

**Under OA time pressure, write memoised recursion.** It mirrors the recurrence directly, so if the recurrence is right the code is right. Convert to tabulation only when you need the space optimisation or recursion depth is a problem.

---

## 4. The families

### 1-D DP — state is one index
Climbing Stairs (`dp[i] = dp[i-1] + dp[i-2]`), House Robber (`dp[i] = max(dp[i-1], dp[i-2] + a[i])`), Decode Ways, Min Cost Climbing Stairs, Word Break (`dp[i]` = is the prefix of length i breakable), Jump Game.

**House Robber II** (circular) is the standard trick: run the linear version twice, once excluding the first house and once excluding the last.

### Knapsack — state is (index, remaining capacity)
The most important family. Four variants, distinguished by two questions: can an item be reused, and are we optimising or counting?

| Variant | Loop order (1-D) | Recurrence |
|---|---|---|
| **0/1** — each item once | capacity **descending** | `dp[c] = max(dp[c], dp[c−w] + v)` |
| **Unbounded** — unlimited reuse | capacity **ascending** | `dp[c] = max(dp[c], dp[c−w] + v)` |
| **Subset sum / partition** | descending | `dp[c] |= dp[c−w]` |
| **Counting (Coin Change II)** | coins outer, amount inner | `dp[c] += dp[c−coin]` |

The **loop direction is the whole distinction** between 0/1 and unbounded in the space-optimised form: descending means `dp[c−w]` still holds the previous item's row (item used at most once); ascending means it already includes the current item (reuse allowed).

For **counting combinations** (order does not matter) the coin loop must be outside; swapping the loops counts *permutations* instead. Coin Change II versus Combination Sum IV is exactly this distinction, and it is a favourite trap.

### 2-D grid DP
Unique Paths, Minimum Path Sum, Maximal Square (`dp[i][j] = 1 + min(up, left, diag)` when the cell is 1), Dungeon Game (fill **backwards** from the destination, because the constraint is on the running minimum).

### String DP — state is (i, j) over two strings
LCS (`dp[i][j] = dp[i-1][j-1] + 1` if characters match, else `max(dp[i-1][j], dp[i][j-1])`), Edit Distance (three operations → `1 + min(insert, delete, replace)`), Distinct Subsequences, Regular Expression / Wildcard Matching, Interleaving String.

**Palindromic subsequence** = LCS of the string with its reverse. **Longest palindromic substring** is different — it is expand-around-centre or `dp[i][j] = dp[i+1][j-1] and s[i]==s[j]`, filled by increasing length.

### Interval DP — state is (left, right), filled by increasing length
Matrix Chain Multiplication, Burst Balloons (the trick: think of the balloon burst **last** in the interval, not first — that keeps the subproblems independent), Minimum Cost to Merge Stones, Palindrome Partitioning II.

### Bitmask DP — state includes a subset
When n ≤ 20: `dp[mask][i]` = best cost having visited the set `mask`, currently at i. Travelling Salesman, assignment problems, Partition to K Equal Sum Subsets. O(2ⁿ · n²) for TSP.

### State-machine DP
Best Time to Buy and Sell Stock with cooldown / fee / at most k transactions. State = (day, transactions used, holding or not). Writing the states as a small diagram first makes the transitions obvious.

### DP on trees and DAGs
`dp[node]` computed from children in postorder (House Robber III, Binary Tree Maximum Path Sum). On a DAG, relax in topological order — that is DP with the ordering handed to you.

### LIS
O(n²) DP (`dp[i] = 1 + max(dp[j])` over `j < i` with `a[j] < a[i]`), or **O(n log n)** with patience sorting: maintain `tails`, where `tails[k]` is the smallest possible tail of an increasing subsequence of length k+1, and binary search the insertion point. `len(tails)` is the answer — but `tails` itself is **not** a valid LIS.

---

## 5. Space optimisation

If `dp[i]` depends only on `dp[i-1]`, keep two rows (or one, iterating in the right direction). O(n·m) → O(min(n, m)). Do it only after the 2-D version is correct — optimising first is how people produce confidently wrong code.

---

## 6. Recognising DP in an OA

**Strong signals:** "number of ways", "minimum/maximum cost", "can you reach/make exactly", "longest/shortest subsequence", choices at each step with future consequences, and a greedy that you can find a counterexample to.

**Signals it is *not* DP:** the greedy is provable; the problem wants all the actual arrangements (backtracking); the subproblems do not overlap (plain divide and conquer).

**Constraints as a hint:** n ≤ 100 with a 2-D feel → O(n²) or O(n³) DP. n ≤ 20 → bitmask DP. n ≤ 10⁵ with a 1-D feel → O(n) or O(n log n) DP.

---

## 7. Recall questions

1. What are the two conditions that make DP applicable?
2. State the five steps in order.
3. Why prefer memoisation under time pressure?
4. Give the 0/1 knapsack recurrence and the space-optimised loop direction — and explain why that direction.
5. Why does unbounded knapsack iterate capacity ascending?
6. Why does the loop order decide combinations versus permutations in coin counting?
7. Give the LCS and edit distance recurrences.
8. How do you get the longest palindromic subsequence from LCS?
9. What is the key insight in Burst Balloons?
10. Explain the O(n log n) LIS and why `tails` is not an LIS.
