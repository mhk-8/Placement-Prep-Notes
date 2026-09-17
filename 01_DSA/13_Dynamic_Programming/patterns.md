# Dynamic Programming — Templates

## 1. Memoised recursion — the OA default
```python
from functools import lru_cache
@lru_cache(maxsize=None)
def f(i, j):
    if base_condition: return base_value
    return combine(f(i - 1, j), f(i, j - 1))
```
For unhashable state, use an explicit dict. Remember `sys.setrecursionlimit`.

## 2. 1-D DP
```python
# Climbing Stairs
a = b = 1
for _ in range(n - 1): a, b = b, a + b
return b

# House Robber
prev = cur = 0
for x in nums:
    prev, cur = cur, max(cur, prev + x)
return cur

# House Robber II (circular)
return max(rob_linear(nums[1:]), rob_linear(nums[:-1])) if len(nums) > 1 else nums[0]

# Word Break
dp = [False] * (len(s) + 1); dp[0] = True
words = set(word_dict)
for i in range(1, len(s) + 1):
    for j in range(i):
        if dp[j] and s[j:i] in words: dp[i] = True; break
return dp[-1]
```

## 3. Knapsack family
```python
# 0/1 knapsack — capacity DESCENDING
dp = [0] * (W + 1)
for wt, val in items:
    for c in range(W, wt - 1, -1):
        dp[c] = max(dp[c], dp[c - wt] + val)
return dp[W]

# Unbounded knapsack — capacity ASCENDING
for wt, val in items:
    for c in range(wt, W + 1):
        dp[c] = max(dp[c], dp[c - wt] + val)

# Subset sum / Partition Equal Subset Sum
total = sum(nums)
if total % 2: return False
target = total // 2
dp = [False] * (target + 1); dp[0] = True
for x in nums:
    for c in range(target, x - 1, -1):
        dp[c] = dp[c] or dp[c - x]
return dp[target]

# Coin Change — minimum coins (unbounded)
dp = [float('inf')] * (amount + 1); dp[0] = 0
for coin in coins:
    for c in range(coin, amount + 1):
        dp[c] = min(dp[c], dp[c - coin] + 1)
return -1 if dp[amount] == float('inf') else dp[amount]

# Coin Change II — count COMBINATIONS (coins OUTER)
dp = [0] * (amount + 1); dp[0] = 1
for coin in coins:
    for c in range(coin, amount + 1):
        dp[c] += dp[c - coin]

# Combination Sum IV — count PERMUTATIONS (target OUTER)
dp = [0] * (target + 1); dp[0] = 1
for c in range(1, target + 1):
    for x in nums:
        if x <= c: dp[c] += dp[c - x]
```

## 4. 2-D grid DP
```python
# Minimum Path Sum
dp = [[0]*C for _ in range(R)]
dp[0][0] = grid[0][0]
for r in range(R):
    for c in range(C):
        if r == 0 and c == 0: continue
        best = float('inf')
        if r: best = min(best, dp[r-1][c])
        if c: best = min(best, dp[r][c-1])
        dp[r][c] = grid[r][c] + best

# Maximal Square
best = 0
dp = [[0]*(C+1) for _ in range(R+1)]
for r in range(1, R+1):
    for c in range(1, C+1):
        if matrix[r-1][c-1] == '1':
            dp[r][c] = 1 + min(dp[r-1][c], dp[r][c-1], dp[r-1][c-1])
            best = max(best, dp[r][c])
return best * best
```

## 5. String DP
```python
# Longest Common Subsequence
dp = [[0]*(m+1) for _ in range(n+1)]
for i in range(1, n+1):
    for j in range(1, m+1):
        if a[i-1] == b[j-1]: dp[i][j] = dp[i-1][j-1] + 1
        else:                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
return dp[n][m]

# Edit Distance
dp = [[0]*(m+1) for _ in range(n+1)]
for i in range(n+1): dp[i][0] = i
for j in range(m+1): dp[0][j] = j
for i in range(1, n+1):
    for j in range(1, m+1):
        if a[i-1] == b[j-1]: dp[i][j] = dp[i-1][j-1]
        else: dp[i][j] = 1 + min(dp[i-1][j],      # delete
                                 dp[i][j-1],      # insert
                                 dp[i-1][j-1])    # replace

# Longest Palindromic Subsequence = LCS(s, reversed(s))
```

## 6. Interval DP
```python
# Burst Balloons — think of the balloon burst LAST
a = [1] + nums + [1]
n = len(a)
dp = [[0]*n for _ in range(n)]
for length in range(2, n):                    # by increasing interval length
    for l in range(0, n - length):
        r = l + length
        for k in range(l + 1, r):             # k is burst LAST in (l, r)
            dp[l][r] = max(dp[l][r],
                           dp[l][k] + a[l]*a[k]*a[r] + dp[k][r])
return dp[0][n-1]
```

## 7. LIS
```python
# O(n^2)
dp = [1] * n
for i in range(n):
    for j in range(i):
        if a[j] < a[i]: dp[i] = max(dp[i], dp[j] + 1)
return max(dp)

# O(n log n)
import bisect
tails = []
for x in a:
    i = bisect.bisect_left(tails, x)     # bisect_right for non-decreasing
    if i == len(tails): tails.append(x)
    else:               tails[i] = x
return len(tails)                         # tails is NOT an actual LIS
```

## 8. State-machine DP — stocks
```python
# With cooldown
hold = float('-inf'); sold = 0; rest = 0
for p in prices:
    prev_sold = sold
    sold = hold + p
    hold = max(hold, rest - p)
    rest = max(rest, prev_sold)
return max(sold, rest)

# At most k transactions
buy  = [float('-inf')] * (k + 1)
sell = [0] * (k + 1)
for p in prices:
    for t in range(1, k + 1):
        buy[t]  = max(buy[t],  sell[t-1] - p)
        sell[t] = max(sell[t], buy[t] + p)
return sell[k]
```

## 9. Bitmask DP — TSP
```python
INF = float('inf')
dp = [[INF]*n for _ in range(1 << n)]
dp[1][0] = 0                                  # start at 0, only 0 visited
for mask in range(1 << n):
    for u in range(n):
        if dp[mask][u] == INF or not (mask >> u & 1): continue
        for v in range(n):
            if mask >> v & 1: continue
            nm = mask | (1 << v)
            dp[nm][v] = min(dp[nm][v], dp[mask][u] + cost[u][v])
return min(dp[(1 << n) - 1][u] + cost[u][0] for u in range(n))
```

## 10. Tree DP
```python
# House Robber III
def rob(root):
    def go(n):                                # returns (rob_this, skip_this)
        if not n: return (0, 0)
        L, R = go(n.left), go(n.right)
        return (n.val + L[1] + R[1], max(L) + max(R))
    return max(go(root))
```

## C++ notes
- `vector<vector<int>> dp(n+1, vector<int>(m+1, 0));`
- `memset(dp, -1, sizeof dp)` works only for 0 and −1 on int arrays.
- Prefer iterative DP over deep memoised recursion for n ≥ 10⁵.
- Watch overflow on counting DP — use `long long` and take the modulus every step.
