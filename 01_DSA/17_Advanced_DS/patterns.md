# Advanced Data Structures — Templates

## 1. Fenwick tree (BIT) — point update, prefix sum
```python
class BIT:
    def __init__(self, n):
        self.n = n
        self.t = [0] * (n + 1)              # 1-INDEXED

    def update(self, i, delta):             # i is 1-based
        while i <= self.n:
            self.t[i] += delta
            i += i & -i                     # move to the parent block

    def prefix(self, i):                    # sum of [1..i]
        s = 0
        while i > 0:
            s += self.t[i]
            i -= i & -i
        return s

    def range_sum(self, l, r):              # inclusive, 1-based
        return self.prefix(r) - self.prefix(l - 1)
```

**Counting smaller elements to the right** (coordinate-compressed):
```python
def count_smaller(nums):
    ranks = {v: i + 1 for i, v in enumerate(sorted(set(nums)))}
    bit, out = BIT(len(ranks)), []
    for x in reversed(nums):
        out.append(bit.prefix(ranks[x] - 1))   # already-seen values smaller than x
        bit.update(ranks[x], 1)
    return out[::-1]
```

## 2. Segment tree — point update, range query
```python
class SegTree:
    def __init__(self, a, func=min, default=float('inf')):
        self.n, self.func, self.default = len(a), func, default
        self.t = [default] * (2 * self.n)
        self.t[self.n:] = a                                  # leaves
        for i in range(self.n - 1, 0, -1):                   # build upward
            self.t[i] = func(self.t[2*i], self.t[2*i + 1])

    def update(self, i, val):                                # 0-based index
        i += self.n
        self.t[i] = val
        i //= 2
        while i:
            self.t[i] = self.func(self.t[2*i], self.t[2*i + 1])
            i //= 2

    def query(self, l, r):                                   # [l, r)
        res_l = res_r = self.default
        l += self.n; r += self.n
        while l < r:
            if l & 1: res_l = self.func(res_l, self.t[l]); l += 1
            if r & 1: r -= 1; res_r = self.func(self.t[r], res_r)
            l //= 2; r //= 2
        return self.func(res_l, res_r)
```
This iterative form is shorter and faster than the recursive one, and it works for any associative `func`.

## 3. Segment tree with lazy propagation (range add, range sum)
```python
class LazySeg:
    def __init__(self, n):
        self.n = n
        self.t = [0] * (4 * n)
        self.lz = [0] * (4 * n)

    def _push(self, node, l, r):
        if self.lz[node]:
            self.t[node] += self.lz[node] * (r - l + 1)
            if l != r:
                self.lz[2*node]     += self.lz[node]
                self.lz[2*node + 1] += self.lz[node]
            self.lz[node] = 0

    def update(self, node, l, r, ql, qr, val):
        self._push(node, l, r)
        if qr < l or r < ql: return
        if ql <= l and r <= qr:
            self.lz[node] += val
            self._push(node, l, r)
            return
        m = (l + r) // 2
        self.update(2*node, l, m, ql, qr, val)
        self.update(2*node + 1, m + 1, r, ql, qr, val)
        self.t[node] = self.t[2*node] + self.t[2*node + 1]

    def query(self, node, l, r, ql, qr):
        self._push(node, l, r)
        if qr < l or r < ql: return 0
        if ql <= l and r <= qr: return self.t[node]
        m = (l + r) // 2
        return (self.query(2*node, l, m, ql, qr) +
                self.query(2*node + 1, m + 1, r, ql, qr))
```

## 4. Sparse table — static range minimum, O(1) query
```python
import math
class SparseTable:
    def __init__(self, a, func=min):
        self.func = func
        n = len(a); K = n.bit_length()
        self.st = [list(a)] + [[0]*n for _ in range(K)]
        for k in range(1, K):
            span = 1 << k
            for i in range(n - span + 1):
                self.st[k][i] = func(self.st[k-1][i],
                                     self.st[k-1][i + (span >> 1)])

    def query(self, l, r):                       # inclusive
        k = (r - l + 1).bit_length() - 1
        return self.func(self.st[k][l], self.st[k][r - (1 << k) + 1])
```

## 5. LFU Cache — O(1)
```python
from collections import defaultdict, OrderedDict
class LFUCache:
    def __init__(self, capacity):
        self.cap = capacity
        self.val = {}                       # key -> value
        self.freq = {}                      # key -> frequency
        self.buckets = defaultdict(OrderedDict)   # freq -> {key: None}, LRU order
        self.min_freq = 0

    def _touch(self, key):
        f = self.freq[key]
        del self.buckets[f][key]
        if not self.buckets[f]:
            del self.buckets[f]
            if self.min_freq == f: self.min_freq += 1
        self.freq[key] = f + 1
        self.buckets[f + 1][key] = None

    def get(self, key):
        if key not in self.val: return -1
        self._touch(key)
        return self.val[key]

    def put(self, key, value):
        if self.cap <= 0: return
        if key in self.val:
            self.val[key] = value; self._touch(key); return
        if len(self.val) >= self.cap:
            evict, _ = self.buckets[self.min_freq].popitem(last=False)   # LRU tie-break
            if not self.buckets[self.min_freq]: del self.buckets[self.min_freq]
            del self.val[evict]; del self.freq[evict]
        self.val[key] = value; self.freq[key] = 1
        self.buckets[1][key] = None
        self.min_freq = 1
```

## 6. Insert Delete GetRandom O(1)
```python
import random
class RandomizedSet:
    def __init__(self):
        self.a = []                          # values, for O(1) random access
        self.pos = {}                        # value -> index in a

    def insert(self, val):
        if val in self.pos: return False
        self.pos[val] = len(self.a); self.a.append(val)
        return True

    def remove(self, val):
        if val not in self.pos: return False
        i, last = self.pos[val], self.a[-1]
        self.a[i] = last; self.pos[last] = i   # swap with the last element
        self.a.pop(); del self.pos[val]
        return True

    def getRandom(self):
        return random.choice(self.a)
```

## 7. Time-Based Key-Value Store
```python
import bisect
from collections import defaultdict
class TimeMap:
    def __init__(self): self.store = defaultdict(list)     # key -> [(ts, val)]
    def set(self, key, value, timestamp):
        self.store[key].append((timestamp, value))          # timestamps increase
    def get(self, key, timestamp):
        arr = self.store[key]
        i = bisect.bisect_right(arr, (timestamp, chr(127)))
        return arr[i-1][1] if i else ""
```

## C++ notes
- `vector<long long> bit(n+1, 0);` — Fenwick sums overflow `int` quickly.
- Segment tree recursive form: `build(1, 0, n-1)`, children `2*node` and `2*node+1`, size `4*n`.
- `policy_based_data_structures` (`tree_order_statistics_node_update`) gives an order-statistic set in GCC — handy for rank queries, but not portable.
