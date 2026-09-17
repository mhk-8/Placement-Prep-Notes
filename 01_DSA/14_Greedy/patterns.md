# Greedy — Templates

## 1. Interval scheduling — maximum non-overlapping
```python
def max_non_overlapping(intervals):
    intervals.sort(key=lambda x: x[1])         # by END time
    count, last_end = 0, float('-inf')
    for s, e in intervals:
        if s >= last_end:                       # compatible
            count += 1; last_end = e
    return count

# Minimum removals = len(intervals) - max_non_overlapping(intervals)
```

## 2. Minimum arrows to burst balloons
```python
def find_min_arrows(points):
    points.sort(key=lambda x: x[1])
    arrows, end = 0, float('-inf')
    for s, e in points:
        if s > end:                             # strictly: touching counts as hit
            arrows += 1; end = e
    return arrows
```

## 3. Jump Game / Jump Game II
```python
def can_jump(nums):
    reach = 0
    for i, x in enumerate(nums):
        if i > reach: return False
        reach = max(reach, i + x)
    return True

def min_jumps(nums):                            # BFS by levels
    jumps = cur_end = farthest = 0
    for i in range(len(nums) - 1):
        farthest = max(farthest, i + nums[i])
        if i == cur_end:                        # end of this level
            jumps += 1; cur_end = farthest
    return jumps
```

## 4. Gas Station
```python
def can_complete_circuit(gas, cost):
    if sum(gas) < sum(cost): return -1
    start = tank = 0
    for i in range(len(gas)):
        tank += gas[i] - cost[i]
        if tank < 0:                            # cannot start anywhere in [start, i]
            start = i + 1; tank = 0
    return start
```

## 5. Partition Labels
```python
def partition_labels(s):
    last = {ch: i for i, ch in enumerate(s)}
    out, start, end = [], 0, 0
    for i, ch in enumerate(s):
        end = max(end, last[ch])
        if i == end:                            # every char in the block ends here
            out.append(end - start + 1); start = i + 1
    return out
```

## 6. Candy — two passes
```python
def candy(ratings):
    n = len(ratings)
    c = [1] * n
    for i in range(1, n):                       # left to right
        if ratings[i] > ratings[i-1]: c[i] = c[i-1] + 1
    for i in range(n - 2, -1, -1):              # right to left
        if ratings[i] > ratings[i+1]: c[i] = max(c[i], c[i+1] + 1)
    return sum(c)
```

## 7. Meeting Rooms II — two ways
```python
import heapq
def min_meeting_rooms(intervals):               # heap of end times
    intervals.sort()
    h = []
    for s, e in intervals:
        if h and h[0] <= s: heapq.heappop(h)    # a room freed up
        heapq.heappush(h, e)
    return len(h)

def min_meeting_rooms_sweep(intervals):         # sweep line
    starts = sorted(s for s, _ in intervals)
    ends   = sorted(e for _, e in intervals)
    rooms = best = j = 0
    for s in starts:
        while ends[j] <= s: j += 1; rooms -= 1
        rooms += 1; best = max(best, rooms)
    return best
```

## 8. Fractional knapsack
```python
def fractional_knapsack(items, W):              # items: (value, weight)
    items.sort(key=lambda x: x[0]/x[1], reverse=True)
    total = 0.0
    for v, wt in items:
        if W >= wt: total += v; W -= wt
        else:       total += v * W / wt; break
    return total
```

## 9. Huffman coding
```python
import heapq
def huffman_cost(freqs):
    h = list(freqs); heapq.heapify(h)
    total = 0
    while len(h) > 1:
        a = heapq.heappop(h); b = heapq.heappop(h)
        total += a + b
        heapq.heappush(h, a + b)
    return total
```

## 10. Reorganize String (greedy + heap)
```python
from collections import Counter
def reorganize(s):
    cnt = Counter(s)
    if max(cnt.values()) > (len(s) + 1) // 2: return ""
    h = [(-c, ch) for ch, c in cnt.items()]; heapq.heapify(h)
    out, prev = [], None
    while h:
        c, ch = heapq.heappop(h)
        out.append(ch)
        if prev: heapq.heappush(h, prev)        # release the previous char
        prev = (c + 1, ch) if c + 1 else None
    return ''.join(out)
```

## C++ notes
- `sort(v.begin(), v.end(), [](auto& a, auto& b){ return a[1] < b[1]; });` for end-time sorting.
- `priority_queue<int, vector<int>, greater<int>>` for a min-heap of end times.
