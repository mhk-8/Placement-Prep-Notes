# Python — Snippets

> Copy-ready blocks. Type each from memory until automatic.

---

## 1. Starter template

```python
import sys
from collections import defaultdict, Counter, deque
import heapq, bisect, math
from functools import lru_cache

input = sys.stdin.readline
sys.setrecursionlimit(300000)

def solve():
    n = int(input())
    a = list(map(int, input().split()))
    print(ans)

def main():
    t = 1
    # t = int(input())
    for _ in range(t):
        solve()

main()
```

---

## 2. Reading input

```python
n = int(input())
n, m = map(int, input().split())
a = list(map(int, input().split()))
s = input().strip()                       # strip! readline keeps the newline
grid = [input().strip() for _ in range(n)]
mat = [list(map(int, input().split())) for _ in range(n)]

data = sys.stdin.read().split()           # everything at once — fastest
idx = 0
def nxt(): 
    global idx
    idx += 1
    return data[idx-1]
```

**Fast output for many lines:**
```python
out = []
for ... : out.append(str(x))
sys.stdout.write('\n'.join(out) + '\n')
```

---

## 3. Sorting

```python
a.sort()                                          # in place, stable
b = sorted(a, reverse=True)

a.sort(key=lambda x: (x[1], -x[0]))               # second asc, first desc
a.sort(key=len)
max(a, key=lambda p: p[1])

idx = sorted(range(n), key=lambda i: a[i])        # indices sorted by value

from functools import cmp_to_key
def cmp(x, y):
    if x + y > y + x: return -1
    if x + y < y + x: return 1
    return 0
nums = sorted(map(str, nums), key=cmp_to_key(cmp))    # "Largest Number"
```

Negating a numeric key is the clean way to mix ascending and descending: `key=lambda x: (x.a, -x.b)`.

---

## 4. Binary search

```python
import bisect
i = bisect.bisect_left(a, x)              # first index with a[i] >= x
j = bisect.bisect_right(a, x)             # first index with a[i] >  x
count = j - i

# binary search on the answer
lo, hi = 1, 10**9
while lo < hi:
    mid = (lo + hi) // 2
    if feasible(mid): hi = mid
    else:             lo = mid + 1
# answer = lo
```

---

## 5. Heaps

```python
import heapq
h = []
heapq.heappush(h, x)
smallest = heapq.heappop(h)
peek = h[0]
heapq.heapify(a)                          # O(n), in place

# max-heap
heapq.heappush(h, -x)
largest = -heapq.heappop(h)

# top-k with a bounded min-heap: h[0] is the k-th largest
for x in a:
    heapq.heappush(h, x)
    if len(h) > k: heapq.heappop(h)

# tuples with a tiebreaker so payloads are never compared
import itertools
counter = itertools.count()
heapq.heappush(h, (priority, next(counter), payload))
```

---

## 6. Graphs

```python
from collections import defaultdict, deque
g = defaultdict(list)
for _ in range(m):
    u, v = map(int, input().split())
    g[u].append(v)
    g[v].append(u)                        # undirected only

# BFS
dist = {src: 0}
q = deque([src])
while q:
    u = q.popleft()
    for v in g[u]:
        if v not in dist:
            dist[v] = dist[u] + 1
            q.append(v)

# Dijkstra
import heapq
INF = float('inf')
d = [INF] * n; d[src] = 0
pq = [(0, src)]
while pq:
    cd, u = heapq.heappop(pq)
    if cd > d[u]: continue                # stale entry
    for v, w in adj[u]:
        if cd + w < d[v]:
            d[v] = cd + w
            heapq.heappush(pq, (d[v], v))
```

---

## 7. Union-Find

```python
class DSU:
    def __init__(self, n):
        self.p = list(range(n))
        self.sz = [1] * n
        self.count = n
    def find(self, x):
        while self.p[x] != x:
            self.p[x] = self.p[self.p[x]]      # path halving
            x = self.p[x]
        return x
    def union(self, a, b):
        ra, rb = self.find(a), self.find(b)
        if ra == rb: return False
        if self.sz[ra] < self.sz[rb]: ra, rb = rb, ra
        self.p[rb] = ra
        self.sz[ra] += self.sz[rb]
        self.count -= 1
        return True
```

---

## 8. Memoisation

```python
from functools import lru_cache

@lru_cache(maxsize=None)
def f(i, j):
    if i == 0 or j == 0: return 0
    return f(i-1, j-1) + 1 if a[i-1] == b[j-1] else max(f(i-1, j), f(i, j-1))

f.cache_clear()                            # reset between test cases!
```

Arguments must be **hashable** — pass tuples, never lists. For an unhashable state, memoise manually into a dict.

---

## 9. Grid traversal

```python
DIRS = ((1,0), (-1,0), (0,1), (0,-1))
# 8-directional: DIRS8 = [(dr,dc) for dr in (-1,0,1) for dc in (-1,0,1) if (dr,dc) != (0,0)]

R, C = len(grid), len(grid[0])
for dr, dc in DIRS:
    nr, nc = r + dr, c + dc
    if 0 <= nr < R and 0 <= nc < C:
        ...
```

Python's chained comparison `0 <= nr < R` is both idiomatic and faster than writing it as two conditions.

---

## 10. Strings

```python
cnt = Counter(s)
cnt = [0] * 26
for ch in s: cnt[ord(ch) - 97] += 1

''.join(sorted(s))                        # canonical anagram key
s == s[::-1]                              # palindrome check

parts = []
for ...: parts.append(piece)
result = ''.join(parts)                   # NEVER s += piece in a loop
```

---

## 11. Modular arithmetic

```python
MOD = 10**9 + 7
pow(base, exp, MOD)                       # built-in fast modpow
inv = pow(x, MOD - 2, MOD)                # Fermat, MOD prime

N = 200005
fact = [1] * N
for i in range(1, N): fact[i] = fact[i-1] * i % MOD
inv_fact = [1] * N
inv_fact[N-1] = pow(fact[N-1], MOD-2, MOD)
for i in range(N-1, 0, -1): inv_fact[i-1] = inv_fact[i] * i % MOD

def nCr(n, r):
    if r < 0 or r > n: return 0
    return fact[n] * inv_fact[r] % MOD * inv_fact[n-r] % MOD
```

---

## 12. itertools

```python
from itertools import permutations, combinations, product, accumulate, groupby

list(permutations([1,2,3]))               # 6 tuples
list(combinations([1,2,3], 2))            # 3 tuples
list(product([0,1], repeat=3))            # 8 tuples — all bitmasks
list(accumulate([1,2,3,4]))               # [1,3,6,10] prefix sums
list(accumulate(a, max))                  # running maximum

for key, group in groupby(sorted(a)):     # MUST be sorted first
    print(key, len(list(group)))
```

---

## 13. Subsets via bitmask

```python
n = len(a)
for mask in range(1 << n):
    sub = [a[i] for i in range(n) if mask >> i & 1]
    ...

bin(mask).count('1')                      # popcount
mask.bit_count()                          # Python 3.10+
```

---

## 14. Pre-submit checklist

- [ ] `input = sys.stdin.readline` present, and `.strip()` on string reads
- [ ] No `s += c` inside a loop
- [ ] No `list.pop(0)` in a BFS — use `deque`
- [ ] No `x in list` inside a loop — use a `set`
- [ ] No slicing inside a loop
- [ ] Grid built as `[[0]*m for _ in range(n)]`, never `[[0]*m]*n`
- [ ] `sys.setrecursionlimit` raised if recursing deeply
- [ ] `lru_cache` cleared between test cases
- [ ] Integer division `//` where you mean floor, remembering it floors toward −∞
- [ ] Mutable default arguments avoided
