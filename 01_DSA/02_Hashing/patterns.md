# Hashing — Templates

## 1. Seen set
```python
seen = set()
for x in a:
    if x in seen: return True
    seen.add(x)
return False
```

## 2. Frequency map
```python
from collections import Counter
cnt = Counter(a)
cnt.most_common(k)                       # top-k by frequency
```
For lowercase-only strings, prefer a fixed array:
```python
cnt = [0] * 26
for ch in s: cnt[ord(ch) - 97] += 1
```

## 3. Two Sum — complement lookup
```python
def two_sum(a, target):
    pos = {}
    for i, x in enumerate(a):
        if target - x in pos:            # check BEFORE inserting
            return [pos[target - x], i]
        pos[x] = i
    return []
```

## 4. Prefix sum + hash map — count subarrays summing to k
```python
from collections import defaultdict
def subarray_sum(a, k):
    seen = defaultdict(int); seen[0] = 1      # empty prefix
    run = cnt = 0
    for x in a:
        run += x
        cnt += seen[run - k]                   # count BEFORE inserting
        seen[run] += 1
    return cnt
```

**Longest subarray summing to k** — store the *first* index only:
```python
first = {0: -1}
run = best = 0
for i, x in enumerate(a):
    run += x
    if run - k in first: best = max(best, i - first[run - k])
    if run not in first: first[run] = i        # never overwrite
```

**Subarray sum divisible by k**
```python
seen = defaultdict(int); seen[0] = 1
run = cnt = 0
for x in a:
    run = (run + x) % k
    if run < 0: run += k                       # C++/Java negatives
    cnt += seen[run]
    seen[run] += 1
```

**Contiguous array — equal 0s and 1s**
```python
first = {0: -1}; run = best = 0
for i, x in enumerate(a):
    run += 1 if x == 1 else -1                 # map 0 → -1
    if run in first: best = max(best, i - first[run])
    else: first[run] = i
```

## 5. Group by canonical key
```python
from collections import defaultdict
groups = defaultdict(list)
for s in words:
    key = tuple(sorted(s))                     # or a 26-tuple of counts
    groups[key].append(s)
return list(groups.values())
```
O(n · L log L) with the sorted key; O(n · L) with the count key.

## 6. Longest Consecutive Sequence — O(n)
```python
def longest_consecutive(a):
    s = set(a); best = 0
    for x in s:
        if x - 1 in s: continue                # only start at run beginnings
        y = x
        while y + 1 in s: y += 1
        best = max(best, y - x + 1)
    return best
```
Each element is visited by the inner loop at most once across the whole run, so it is O(n) despite appearing quadratic.

## 7. 4Sum II — meet in the middle
```python
from collections import Counter
ab = Counter(x + y for x in A for y in B)
return sum(ab[-(z + w)] for z in C for w in D)     # O(n²)
```

## 8. Encoding composite keys
```python
key = r * C + c                    # grid cell → single int
key = (a, b)                       # tuple: hashable in Python
key = a * 1_000_003 + b            # manual pack for speed
```

## 9. C++ randomised hash (anti-hash defence)
```cpp
struct Hash {
    static uint64_t splitmix64(uint64_t x){
        x += 0x9e3779b97f4a7c15ULL;
        x = (x ^ (x >> 30)) * 0xbf58476d1ce4e5b9ULL;
        x = (x ^ (x >> 27)) * 0x94d049bb133111ebULL;
        return x ^ (x >> 31);
    }
    size_t operator()(uint64_t x) const {
        static const uint64_t S =
            chrono::steady_clock::now().time_since_epoch().count();
        return splitmix64(x + S);
    }
};
unordered_map<long long, int, Hash> mp;
```

## C++ notes
- `unordered_map` = hash table (average O(1)); `map` = red-black tree (O(log n), ordered, supports `lower_bound`).
- `mp[key]` **inserts** a default-constructed value if the key is absent. Use `mp.count(key)` or `mp.find(key) != mp.end()` to test membership without inserting — a common source of both bugs and memory blow-ups.
- `mp.reserve(n)` before a big insertion loop avoids repeated rehashing.
- `pair` is not hashable by default in `unordered_map`; either supply a hash or use `map`.
