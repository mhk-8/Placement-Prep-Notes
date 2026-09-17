# Intervals & Sweep Line — Templates

## 1. Merge Intervals — sort by START
```python
def merge(intervals):
    intervals.sort(key=lambda x: x[0])
    out = []
    for s, e in intervals:
        if out and s <= out[-1][1]:            # overlaps the last kept
            out[-1][1] = max(out[-1][1], e)    # max() matters: nested intervals
        else:
            out.append([s, e])
    return out
```

## 2. Insert Interval — O(n), input already sorted
```python
def insert(intervals, new):
    out, i, n = [], 0, len(intervals)
    while i < n and intervals[i][1] < new[0]:      # strictly before
        out.append(intervals[i]); i += 1
    while i < n and intervals[i][0] <= new[1]:     # overlapping
        new = [min(new[0], intervals[i][0]), max(new[1], intervals[i][1])]
        i += 1
    out.append(new)
    out.extend(intervals[i:])
    return out
```

## 3. Maximum non-overlapping — sort by END
```python
def max_non_overlapping(intervals):
    intervals.sort(key=lambda x: x[1])
    count, last_end = 0, float('-inf')
    for s, e in intervals:
        if s >= last_end:
            count += 1; last_end = e
    return count

def erase_overlap_intervals(intervals):
    return len(intervals) - max_non_overlapping(intervals)
```

## 4. Minimum arrows (touching counts as covered)
```python
def find_min_arrow_shots(points):
    points.sort(key=lambda x: x[1])
    arrows, end = 0, float('-inf')
    for s, e in points:
        if s > end:                             # STRICT: touching is a hit
            arrows += 1; end = e
    return arrows
```

## 5. Sweep line — maximum concurrency
```python
def max_concurrent(intervals):
    events = []
    for s, e in intervals:
        events.append((s, 1))
        events.append((e, -1))
    events.sort()          # at equal coords, -1 sorts before +1 → touching is OK
    cur = best = 0
    for _, delta in events:
        cur += delta
        best = max(best, cur)
    return best
```
If touching **should** count as overlapping, sort with `key=lambda x: (x[0], -x[1])` so `+1` comes first.

## 6. Meeting Rooms II — three ways
```python
import heapq

def min_rooms_heap(intervals):
    intervals.sort()
    h = []
    for s, e in intervals:
        if h and h[0] <= s: heapq.heappop(h)     # a room has freed up
        heapq.heappush(h, e)
    return len(h)

def min_rooms_two_arrays(intervals):
    starts = sorted(s for s, _ in intervals)
    ends   = sorted(e for _, e in intervals)
    rooms = best = j = 0
    for s in starts:
        while j < len(ends) and ends[j] <= s: j += 1; rooms -= 1
        rooms += 1; best = max(best, rooms)
    return best
```

## 7. Interval List Intersections — two pointers
```python
def interval_intersection(A, B):
    i = j = 0; out = []
    while i < len(A) and j < len(B):
        lo = max(A[i][0], B[j][0])
        hi = min(A[i][1], B[j][1])
        if lo <= hi: out.append([lo, hi])
        if A[i][1] < B[j][1]: i += 1             # advance the one ending first
        else:                 j += 1
    return out
```

## 8. Employee Free Time
```python
def employee_free_time(schedules):
    all_iv = sorted([iv for person in schedules for iv in person])
    merged = merge(all_iv)
    return [[merged[i][1], merged[i+1][0]] for i in range(len(merged) - 1)]
```

## 9. Difference array — dense, bounded coordinates
```python
def car_pooling(trips, capacity):
    diff = [0] * 1001
    for num, s, e in trips:
        diff[s] += num
        diff[e] -= num                            # passengers leave at e
    run = 0
    for d in diff:
        run += d
        if run > capacity: return False
    return True
```

## 10. Coordinate compression
```python
coords = sorted({c for s, e in intervals for c in (s, e)})
idx = {c: i for i, c in enumerate(coords)}
# now sweep or difference-array over 0..len(coords)-1
```

## 11. My Calendar I — booking with no overlap
```python
import bisect
class MyCalendar:
    def __init__(self): self.starts = []; self.ends = []
    def book(self, s, e):
        i = bisect.bisect_right(self.starts, s)
        if i > 0 and self.ends[i-1] > s: return False     # overlaps the previous
        if i < len(self.starts) and self.starts[i] < e: return False
        self.starts.insert(i, s); self.ends.insert(i, e)
        return True
```

## C++ notes
- `sort(v.begin(), v.end())` on `vector<vector<int>>` sorts lexicographically — start first, which is what merging wants.
- For end-time sorting: `sort(v.begin(), v.end(), [](auto& a, auto& b){ return a[1] < b[1]; });`
- `map<int,int>` as a sweep accumulator is ordered automatically: `mp[s]++; mp[e]--;` then iterate.
