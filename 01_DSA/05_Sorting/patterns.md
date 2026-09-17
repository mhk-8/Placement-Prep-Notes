# Sorting — Templates

## 1. Merge sort
```python
def merge_sort(a):
    if len(a) <= 1: return a
    mid = len(a) // 2
    L, R = merge_sort(a[:mid]), merge_sort(a[mid:])
    out, i, j = [], 0, 0
    while i < len(L) and j < len(R):
        if L[i] <= R[j]: out.append(L[i]); i += 1     # <= keeps it STABLE
        else:            out.append(R[j]); j += 1
    out.extend(L[i:]); out.extend(R[j:])
    return out
```

## 2. Counting inversions (merge-sort variant)
```python
def count_inversions(a):
    def sort_count(a):
        if len(a) <= 1: return a, 0
        mid = len(a) // 2
        L, cl = sort_count(a[:mid])
        R, cr = sort_count(a[mid:])
        out, i, j, inv = [], 0, 0, cl + cr
        while i < len(L) and j < len(R):
            if L[i] <= R[j]: out.append(L[i]); i += 1
            else:
                out.append(R[j]); j += 1
                inv += len(L) - i          # L[i:] are all > R[j]
        out.extend(L[i:]); out.extend(R[j:])
        return out, inv
    return sort_count(a)[1]
```

## 3. Quicksort (Lomuto partition)
```python
import random
def quicksort(a, lo, hi):
    if lo >= hi: return
    p = partition(a, lo, hi)
    quicksort(a, lo, p - 1)
    quicksort(a, p + 1, hi)

def partition(a, lo, hi):
    r = random.randint(lo, hi)                  # randomised pivot
    a[r], a[hi] = a[hi], a[r]
    pivot = a[hi]; i = lo
    for j in range(lo, hi):
        if a[j] < pivot:
            a[i], a[j] = a[j], a[i]; i += 1
    a[i], a[hi] = a[hi], a[i]
    return i
```

## 4. Quickselect — k-th smallest, O(n) average
```python
def quickselect(a, k):                          # k is 0-indexed
    lo, hi = 0, len(a) - 1
    while True:
        p = partition(a, lo, hi)
        if p == k: return a[p]
        if p < k:  lo = p + 1
        else:      hi = p - 1
```

## 5. Heapsort
```python
import heapq
def heapsort(a):
    heapq.heapify(a)                            # O(n)
    return [heapq.heappop(a) for _ in range(len(a))]
```

## 6. Counting sort
```python
def counting_sort(a, k):                        # values in [0, k]
    cnt = [0] * (k + 1)
    for x in a: cnt[x] += 1
    for i in range(1, k + 1): cnt[i] += cnt[i-1]     # prefix sums
    out = [0] * len(a)
    for x in reversed(a):                        # reversed keeps it STABLE
        cnt[x] -= 1
        out[cnt[x]] = x
    return out
```

## 7. Custom comparators
```python
# Simple key
a.sort(key=lambda x: (x[1], -x[0]))          # by second asc, then first desc

# Pairwise comparator (Largest Number)
from functools import cmp_to_key
def cmp(x, y):
    if x + y > y + x: return -1               # x first
    if x + y < y + x: return 1
    return 0
nums = sorted(map(str, nums), key=cmp_to_key(cmp))
return '0' if nums[0] == '0' else ''.join(nums)
```
```cpp
sort(a.begin(), a.end(), [](const auto& x, const auto& y){
    return x.second < y.second;               // STRICT less-than, never <=
});
```

## 8. Cyclic sort (values are 1..n)
```python
i = 0
while i < len(a):
    j = a[i] - 1                               # the index where a[i] belongs
    if a[i] != a[j]: a[i], a[j] = a[j], a[i]   # compare VALUES, not indices
    else: i += 1
# now any index with a[i] != i+1 is a missing/duplicate slot
```

## 9. Bucket sort for top-K frequency — O(n)
```python
from collections import Counter
def top_k_frequent(nums, k):
    cnt = Counter(nums)
    buckets = [[] for _ in range(len(nums) + 1)]
    for val, c in cnt.items(): buckets[c].append(val)
    out = []
    for c in range(len(buckets) - 1, 0, -1):
        for v in buckets[c]:
            out.append(v)
            if len(out) == k: return out
```

## C++ notes
- `sort(a.begin(), a.end())` = introsort, not stable. `stable_sort` = merge, O(n log² n) if memory is tight.
- `nth_element(a.begin(), a.begin()+k, a.end())` is quickselect, O(n) average — use it instead of a full sort for k-th element.
- `partial_sort(a.begin(), a.begin()+k, a.end())` gives the k smallest in order, O(n log k).
- A comparator must return **strict** less-than. Returning `<=` violates strict weak ordering and can segfault `std::sort`.
