# Dynamic Programming — Pitfalls

## State definition
- A state that does not capture everything the future depends on (missing a dimension such as "transactions used" or "currently holding"). The DP then silently computes a different problem.
- A state so large it does not fit: check `number of states × work per state` against the constraints before coding.
- Confusing `dp[i]` meaning "answer for the first i elements" with "answer for subarrays ending at i". Both are valid; mixing them within one solution is not.

## Loop direction and order
- **0/1 knapsack with an ascending capacity loop** — items get reused, silently turning it into unbounded knapsack.
- **Unbounded knapsack with a descending loop** — items cannot be reused.
- **Coin Change II with the amount loop outside** — counts permutations instead of combinations (`1+2` and `2+1` counted separately).
- Interval DP not iterating by increasing length, so a subproblem is read before it is computed.
- 2-D DP where `dp[i][j]` depends on `dp[i][j+1]` but the loop runs left to right.

## Base cases
- Forgetting `dp[0] = 1` in counting problems (there is exactly one way to make 0).
- Initialising a minimisation DP to 0 rather than infinity, so everything looks free.
- Off-by-one between 0-indexed strings and 1-indexed DP tables — pick the 1-indexed table with `a[i-1]` and stay consistent.
- Edit distance without the first row and column initialised to `i` and `j`.

## Space optimisation
- Optimising before the 2-D version works. Get it correct, then compress.
- Compressing to one row when the recurrence needs `dp[i-1][j-1]`, which is already overwritten. Either keep a saved diagonal value or use two rows.
- Reusing a stale row from the previous item.

## Overflow and modulo
- Counting DP overflowing 32 bits. Use 64-bit, or take the modulus at every addition (not only at the end).
- Using `float('inf')` in Python and then adding to it — fine in Python, but `INT_MAX + 1` overflows in C++/Java. Use a large sentinel like `1e9`.

## Recursion
- Memoising on a mutable argument (a list), which is unhashable or hashes by identity.
- `@lru_cache` on a method with `self` — the cache then holds the instance alive and keys on it.
- Depth exceeding the recursion limit for n ≥ 10⁵.
- Forgetting to memoise at all, producing an exponential "DP".

## Recognition
- Using greedy where DP is required, because the greedy looked plausible. If you cannot prove the exchange argument in 30 seconds, write the DP.
- Using DP where the problem wants the actual arrangements enumerated — that is backtracking.
- Not noticing that LIS-shaped problems admit an O(n log n) solution when n = 10⁵ makes O(n²) infeasible.
- Confusing longest palindromic *substring* (contiguous) with *subsequence* (not contiguous).
