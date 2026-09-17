# Binary Search — Templates

## 1. Form A — exact match
```python
def search(a, target):
    lo, hi = 0, len(a) - 1
    while lo <= hi:
        mid = lo + (hi - lo) // 2        # overflow-safe in C++/Java
        if a[mid] == target: return mid
        if a[mid] < target: lo = mid + 1
        else:               hi = mid - 1
    return -1
```

## 2. Form B — first true (the default)
```python
def first_true(lo, hi, pred):
    # pred is monotone: F...F T...T. Returns the first x in [lo, hi] with
    # pred(x), or hi if none (the caller must verify).
    while lo < hi:
        mid = lo + (hi - lo) // 2
        if pred(mid): hi = mid           # keep mid — it may be the answer
        else:         lo = mid + 1       # discard mid
    return lo
```

**lower_bound / upper_bound on an array**
```python
def lower_bound(a, x):                   # first i with a[i] >= x
    lo, hi = 0, len(a)
    while lo < hi:
        mid = (lo + hi) // 2
        if a[mid] >= x: hi = mid
        else:           lo = mid + 1
    return lo

def upper_bound(a, x):                   # first i with a[i] > x
    lo, hi = 0, len(a)
    while lo < hi:
        mid = (lo + hi) // 2
        if a[mid] > x: hi = mid
        else:          lo = mid + 1
    return lo

count_of_x    = upper_bound(a, x) - lower_bound(a, x)
first_occ     = lower_bound(a, x)        # valid iff < len(a) and a[first_occ] == x
last_occ      = upper_bound(a, x) - 1
```
Python's `bisect` does this already: `bisect_left` = lower_bound, `bisect_right` = upper_bound.

## 3. Form C — binary search on the answer
```python
def min_feasible(lo, hi, feasible):
    while lo < hi:
        mid = lo + (hi - lo) // 2
        if feasible(mid): hi = mid
        else:             lo = mid + 1
    return lo
```

**Koko Eating Bananas**
```python
def min_eating_speed(piles, h):
    def feasible(s):
        return sum((p + s - 1) // s for p in piles) <= h    # ceil division
    return min_feasible(1, max(piles), feasible)
```

**Capacity to Ship Packages Within D Days**
```python
def ship_within_days(w, days):
    def feasible(cap):
        d, cur = 1, 0
        for x in w:
            if cur + x > cap: d += 1; cur = 0
            cur += x
        return d <= days
    return min_feasible(max(w), sum(w), feasible)           # lo = max(w), not 1
```

**Split Array Largest Sum** — identical shape, with `feasible(s)` = "≤ m parts each summing to ≤ s".

For a **maximum** feasible answer ("maximise the minimum"), flip the update:
```python
while lo < hi:
    mid = lo + (hi - lo + 1) // 2        # NOTE: round UP to avoid an infinite loop
    if feasible(mid): lo = mid
    else:             hi = mid - 1
return lo
```

## 4. Search in Rotated Sorted Array
```python
def search_rotated(a, target):
    lo, hi = 0, len(a) - 1
    while lo <= hi:
        mid = lo + (hi - lo) // 2
        if a[mid] == target: return mid
        if a[lo] <= a[mid]:                       # left half is sorted
            if a[lo] <= target < a[mid]: hi = mid - 1
            else:                        lo = mid + 1
        else:                                     # right half is sorted
            if a[mid] < target <= a[hi]: lo = mid + 1
            else:                        hi = mid - 1
    return -1
```

## 5. Find Minimum in Rotated Sorted Array
```python
lo, hi = 0, len(a) - 1
while lo < hi:
    mid = (lo + hi) // 2
    if a[mid] > a[hi]: lo = mid + 1        # pivot strictly right of mid
    else:              hi = mid            # mid could be the minimum
return a[lo]
```

## 6. Find Peak Element
```python
lo, hi = 0, len(a) - 1
while lo < hi:
    mid = (lo + hi) // 2
    if a[mid] < a[mid + 1]: lo = mid + 1   # rising → a peak lies right
    else:                   hi = mid
return lo
```

## 7. Search a 2-D matrix (row-sorted, first of each row > last of previous)
```python
lo, hi = 0, R * C - 1
while lo <= hi:
    mid = (lo + hi) // 2
    v = mat[mid // C][mid % C]             # flatten the index
    if v == target: return True
    if v < target: lo = mid + 1
    else:          hi = mid - 1
return False
```

## 8. Binary search on reals
```python
lo, hi = 0.0, 1e9
for _ in range(100):
    mid = (lo + hi) / 2
    if feasible(mid): hi = mid
    else:             lo = mid
return lo
```

## C++ notes
- `lower_bound(a.begin(), a.end(), x) - a.begin()` and `upper_bound(...)` from `<algorithm>`; `binary_search(...)` returns only a bool.
- For a `set`/`map`, use the **member** `s.lower_bound(x)` — the free function is O(n) on node-based containers.
- `mid = lo + (hi - lo) / 2` — mandatory; `(lo + hi) / 2` overflows for large indices.
- `equal_range` returns both bounds in one call.
