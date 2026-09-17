# Heaps & Priority Queues — Templates

## 1. Basics (Python)
```python
import heapq
h = []
heapq.heappush(h, x)
x = heapq.heappop(h)              # smallest
top = h[0]                        # peek, O(1)
heapq.heapify(arr)                # O(n), in place
heapq.heappushpop(h, x)           # push then pop, cheaper than both
heapq.heapreplace(h, x)           # pop then push
heapq.nlargest(k, arr)            # O(n log k)
```

**Max-heap by negation**
```python
heapq.heappush(h, -x)
largest = -heapq.heappop(h)
```

**Tuples with a tiebreaker**
```python
counter = itertools.count()
heapq.heappush(h, (priority, next(counter), payload))   # never compares payload
```

## 2. Top-K / k-th largest — min-heap of size k
```python
def kth_largest(a, k):
    h = []
    for x in a:
        heapq.heappush(h, x)
        if len(h) > k: heapq.heappop(h)     # discard the smallest candidate
    return h[0]                              # k-th largest
```

**Kth Largest in a Stream**
```python
class KthLargest:
    def __init__(self, k, nums):
        self.k, self.h = k, list(nums)
        heapq.heapify(self.h)
        while len(self.h) > k: heapq.heappop(self.h)
    def add(self, val):
        heapq.heappush(self.h, val)
        if len(self.h) > self.k: heapq.heappop(self.h)
        return self.h[0]
```

## 3. Top K Frequent Elements
```python
from collections import Counter
def top_k_frequent(nums, k):
    return [v for v, _ in Counter(nums).most_common(k)]
# explicit heap version:
cnt = Counter(nums)
return heapq.nlargest(k, cnt.keys(), key=cnt.get)
```

## 4. K Closest Points to Origin
```python
def k_closest(points, k):
    h = []
    for x, y in points:
        d = x*x + y*y                       # no sqrt needed
        heapq.heappush(h, (-d, x, y))       # max-heap of size k
        if len(h) > k: heapq.heappop(h)
    return [[x, y] for _, x, y in h]
```

## 5. Merge k sorted arrays/lists
```python
def merge_k(arrays):
    h = [(arr[0], i, 0) for i, arr in enumerate(arrays) if arr]
    heapq.heapify(h)
    out = []
    while h:
        val, ai, idx = heapq.heappop(h)
        out.append(val)
        if idx + 1 < len(arrays[ai]):
            heapq.heappush(h, (arrays[ai][idx+1], ai, idx+1))
    return out
```

## 6. Two heaps — running median
```python
class MedianFinder:
    def __init__(self):
        self.lo = []        # max-heap (negated): the smaller half
        self.hi = []        # min-heap: the larger half
    def add_num(self, x):
        heapq.heappush(self.lo, -x)
        heapq.heappush(self.hi, -heapq.heappop(self.lo))   # move the largest over
        if len(self.hi) > len(self.lo):                    # rebalance
            heapq.heappush(self.lo, -heapq.heappop(self.hi))
    def find_median(self):
        if len(self.lo) > len(self.hi): return -self.lo[0]
        return (-self.lo[0] + self.hi[0]) / 2
```

## 7. Task Scheduler (greedy with a heap)
```python
from collections import Counter, deque
def least_interval(tasks, n):
    h = [-c for c in Counter(tasks).values()]
    heapq.heapify(h)
    time, cooling = 0, deque()                  # (available_time, count)
    while h or cooling:
        time += 1
        if cooling and cooling[0][0] == time:
            heapq.heappush(h, cooling.popleft()[1])
        if h:
            c = heapq.heappop(h) + 1            # one instance done
            if c: cooling.append((time + n + 1, c))
    return time
```

## 8. Lazy deletion (Dijkstra shape)
```python
while pq:
    d, u = heapq.heappop(pq)
    if d > dist[u]: continue                    # stale entry — skip
    ...
```

## 9. Manual sift-up / sift-down (for "implement a heap")
```python
def sift_up(a, i):
    while i > 0:
        p = (i - 1) // 2
        if a[i] >= a[p]: break
        a[i], a[p] = a[p], a[i]; i = p

def sift_down(a, i, n):
    while True:
        l, r, small = 2*i + 1, 2*i + 2, i
        if l < n and a[l] < a[small]: small = l
        if r < n and a[r] < a[small]: small = r
        if small == i: break
        a[i], a[small] = a[small], a[i]; i = small

def build_heap(a):                              # O(n)
    for i in range(len(a)//2 - 1, -1, -1):
        sift_down(a, i, len(a))
```

## C++ notes
```cpp
priority_queue<int> maxh;                                        // max-heap
priority_queue<int, vector<int>, greater<int>> minh;             // min-heap
priority_queue<pair<int,int>, vector<pair<int,int>>,
               greater<pair<int,int>>> pq;                       // min by first
// custom: return true when a has LOWER priority than b
auto cmp = [](const Node& a, const Node& b){ return a.cost > b.cost; };
priority_queue<Node, vector<Node>, decltype(cmp)> pq(cmp);       // min by cost
```
- `pq.top()` then `pq.pop()`; `pop()` returns void.
- `make_heap(v.begin(), v.end())` is O(n) on a vector.
