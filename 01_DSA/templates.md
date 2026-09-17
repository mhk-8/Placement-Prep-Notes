# Master Templates

> Type these from memory until they are automatic. In an OA you should not be *deriving* a binary-search boundary; you should be adapting a template you already own.
> Python first (shortest to read), C++ where the idiom differs meaningfully.

---

## 0. OA starter file

```python
import sys
from collections import defaultdict, Counter, deque
import heapq, bisect, math
input = sys.stdin.readline          # fast input
sys.setrecursionlimit(300000)       # deep recursion
```

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
int main(){ ios_base::sync_with_stdio(false); cin.tie(nullptr);
    // never use endl inside a loop — it flushes
}
```

---

## 1. Binary search — the three forms

**Form A: exact match**
```python
def search(a, target):
    lo, hi = 0, len(a) - 1
    while lo <= hi:
        mid = lo + (hi - lo) // 2      # avoids overflow in C++/Java
        if a[mid] == target: return mid
        if a[mid] < target: lo = mid + 1
        else: hi = mid - 1
    return -1
```

**Form B: first index satisfying a monotone predicate** (lower_bound). This is the form you should default to — it never has an off-by-one.
```python
def first_true(lo, hi, pred):          # pred: False...False True...True
    while lo < hi:
        mid = lo + (hi - lo) // 2
        if pred(mid): hi = mid
        else:         lo = mid + 1
    return lo                           # == hi; may be out of range if none
```

**Form C: binary search on the answer** — "minimum possible maximum"
```python
def min_feasible(lo, hi, feasible):    # feasible is monotone: F..F,T..T
    while lo < hi:
        mid = lo + (hi - lo) // 2
        if feasible(mid): hi = mid
        else:             lo = mid + 1
    return lo
```
*C++:* `lower_bound(a.begin(), a.end(), x) - a.begin()`, `upper_bound(...)`.

---

## 2. Sliding window — variable size

```python
def longest_valid(a):
    left = 0; best = 0
    state = {}                          # window state (Counter, sum, ...)
    for right, x in enumerate(a):
        add(state, x)                   # extend
        while not ok(state):            # shrink while invalid
            remove(state, a[left]); left += 1
        best = max(best, right - left + 1)
    return best
```

**Exactly-K via at-most-K:** `exactly(K) = at_most(K) - at_most(K-1)`.

**Fixed size k:**
```python
s = sum(a[:k]); best = s
for i in range(k, len(a)):
    s += a[i] - a[i-k]
    best = max(best, s)
```

---

## 3. Two pointers (sorted array)

```python
i, j = 0, len(a) - 1
while i < j:
    cur = a[i] + a[j]
    if cur == target: return (i, j)
    if cur < target:  i += 1
    else:             j -= 1
```

---

## 4. Prefix sums

```python
pre = [0]*(len(a)+1)
for i, x in enumerate(a): pre[i+1] = pre[i] + x
# sum of a[l..r] inclusive:
rng = pre[r+1] - pre[l]
```

**Subarray sum equals k (with negatives) — prefix + hash map:**
```python
count = 0; run = 0
seen = defaultdict(int); seen[0] = 1    # empty prefix
for x in a:
    run += x
    count += seen[run - k]
    seen[run] += 1
```

---

## 5. Monotonic stack — next greater element

```python
def next_greater(a):
    res = [-1]*len(a)
    st = []                             # stores indices, values decreasing
    for i, x in enumerate(a):
        while st and a[st[-1]] < x:
            res[st.pop()] = x
        st.append(i)
    return res
```
Flip `<` to `>` for next smaller; iterate in reverse for *previous* greater/smaller.

**Monotonic deque — sliding window maximum:**
```python
dq = deque(); out = []                  # dq holds indices, values decreasing
for i, x in enumerate(a):
    while dq and a[dq[-1]] <= x: dq.pop()
    dq.append(i)
    if dq[0] <= i - k: dq.popleft()
    if i >= k-1: out.append(a[dq[0]])
```

---

## 6. Backtracking

```python
def backtrack(path, choices):
    if is_solution(path):
        res.append(path[:])             # copy!
        return
    for c in choices:
        if not valid(path, c): continue
        path.append(c)                  # choose
        backtrack(path, next_choices(c))
        path.pop()                      # un-choose
```

**Subsets:**
```python
def subsets(a):
    res = []
    def go(i, cur):
        if i == len(a): res.append(cur[:]); return
        go(i+1, cur)                    # exclude
        cur.append(a[i]); go(i+1, cur); cur.pop()   # include
    go(0, [])
    return res
```

**Skipping duplicates** (array sorted first): `if i > start and a[i] == a[i-1]: continue`

---

## 7. Tree traversals

```python
def inorder(root):                      # recursive
    if not root: return
    inorder(root.left); visit(root); inorder(root.right)

def inorder_iter(root):                 # iterative
    st, cur = [], root
    while st or cur:
        while cur: st.append(cur); cur = cur.left
        cur = st.pop(); visit(cur); cur = cur.right

def level_order(root):
    if not root: return []
    q, out = deque([root]), []
    while q:
        level = []
        for _ in range(len(q)):         # freeze the level size
            n = q.popleft(); level.append(n.val)
            if n.left:  q.append(n.left)
            if n.right: q.append(n.right)
        out.append(level)
    return out
```

---

## 8. Graphs

**BFS (unweighted shortest path):**
```python
dist = {src: 0}; q = deque([src])
while q:
    u = q.popleft()
    for v in adj[u]:
        if v not in dist:
            dist[v] = dist[u] + 1
            q.append(v)
```

**DFS iterative:**
```python
st, seen = [src], {src}
while st:
    u = st.pop()
    for v in adj[u]:
        if v not in seen: seen.add(v); st.append(v)
```

**Topological sort (Kahn):**
```python
indeg = [0]*n
for u in range(n):
    for v in adj[u]: indeg[v] += 1
q = deque(u for u in range(n) if indeg[u] == 0)
order = []
while q:
    u = q.popleft(); order.append(u)
    for v in adj[u]:
        indeg[v] -= 1
        if indeg[v] == 0: q.append(v)
if len(order) < n: ...                  # cycle exists
```

**Dijkstra:**
```python
dist = [inf]*n; dist[src] = 0
pq = [(0, src)]
while pq:
    d, u = heapq.heappop(pq)
    if d > dist[u]: continue            # stale entry
    for v, wt in adj[u]:
        if d + wt < dist[v]:
            dist[v] = d + wt
            heapq.heappush(pq, (dist[v], v))
```

**Union-Find:**
```python
par = list(range(n)); rank = [0]*n
def find(x):
    while par[x] != x:
        par[x] = par[par[x]]            # path halving
        x = par[x]
    return x
def union(a, b):
    ra, rb = find(a), find(b)
    if ra == rb: return False
    if rank[ra] < rank[rb]: ra, rb = rb, ra
    par[rb] = ra
    if rank[ra] == rank[rb]: rank[ra] += 1
    return True
```

**Grid BFS:**
```python
DIRS = ((1,0), (-1,0), (0,1), (0,-1))
for dr, dc in DIRS:
    nr, nc = r+dr, c+dc
    if 0 <= nr < R and 0 <= nc < C and grid[nr][nc] == '.':
        ...
```

---

## 9. Dynamic programming

**Memoised recursion (the OA default — fastest to get right):**
```python
from functools import lru_cache
@lru_cache(maxsize=None)
def f(i, j):
    if base_case: return base_value
    return best(f(i-1, j), f(i, j-1) + cost)
```

**0/1 knapsack, space-optimised:**
```python
dp = [0]*(W+1)
for wt, val in items:
    for c in range(W, wt-1, -1):        # REVERSE — each item once
        dp[c] = max(dp[c], dp[c-wt] + val)
```
Unbounded knapsack: same loop **forwards**.

**LIS in O(n log n):**
```python
tails = []
for x in a:
    i = bisect.bisect_left(tails, x)    # bisect_right for non-decreasing
    if i == len(tails): tails.append(x)
    else: tails[i] = x
return len(tails)                        # tails is NOT the actual LIS
```

**2-D grid DP:**
```python
dp = [[0]*C for _ in range(R)]
dp[0][0] = grid[0][0]
for r in range(R):
    for c in range(C):
        if r or c:
            best = inf
            if r: best = min(best, dp[r-1][c])
            if c: best = min(best, dp[r][c-1])
            dp[r][c] = grid[r][c] + best
```

---

## 10. Heaps

```python
h = []
heapq.heappush(h, (key, item))
key, item = heapq.heappop(h)            # MIN-heap
# max-heap: push -key, or push (-key, item)
heapq.heapify(arr)                      # O(n), in place
heapq.nlargest(k, arr)                  # O(n log k)

# top-k with a bounded min-heap
for x in a:
    heapq.heappush(h, x)
    if len(h) > k: heapq.heappop(h)     # h[0] is the k-th largest
```
*C++:* `priority_queue<int> pq;` is a **max**-heap. Min-heap: `priority_queue<int, vector<int>, greater<int>> pq;`

---

## 11. Trie

```python
class Trie:
    def __init__(self):
        self.root = {}
    def insert(self, word):
        node = self.root
        for ch in word: node = node.setdefault(ch, {})
        node['#'] = True                # end marker
    def search(self, word, prefix=False):
        node = self.root
        for ch in word:
            if ch not in node: return False
            node = node[ch]
        return True if prefix else '#' in node
```

---

## 12. Fast modular arithmetic

```python
MOD = 10**9 + 7
pow(base, exp, MOD)                     # fast modpow, built in
inv = pow(x, MOD-2, MOD)                # modular inverse, MOD prime (Fermat)

fact = [1]*(N+1)
for i in range(1, N+1): fact[i] = fact[i-1]*i % MOD
def nCr(n, r):
    if r < 0 or r > n: return 0
    return fact[n] * pow(fact[r]*fact[n-r] % MOD, MOD-2, MOD) % MOD
```

---

## 13. Pre-submit checklist

Run this before every submission. It is aimed squarely at the `careless bug` failure mode.

- [ ] Empty input, single element, two elements
- [ ] All identical values · all negative values · already sorted · reverse sorted
- [ ] Integer overflow — does any intermediate exceed 2³¹? (use `long long` in C++/Java)
- [ ] Off-by-one in every loop bound and every `mid` update
- [ ] Are indices and values being confused anywhere? (stacks and deques usually hold **indices**)
- [ ] Did I mutate a collection while iterating it?
- [ ] Did I append a reference instead of a copy? (`path[:]` in backtracking)
- [ ] Is the return inside the loop when it should be after it?
- [ ] Dry-run on the provided example, by hand, line by line
