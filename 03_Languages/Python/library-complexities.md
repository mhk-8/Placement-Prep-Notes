# Python — Data Structures and Complexities

> The lookup table. Python hides its costs behind pleasant syntax, so knowing these is what separates a correct solution from a TLE.

---

## 1. list

| Operation | Complexity | Note |
|---|---|---|
| `a[i]` | O(1) | |
| `a.append(x)` | O(1) amortised | doubling |
| `a.pop()` | O(1) | from the end |
| **`a.pop(0)`** | **O(n)** | shifts everything — use `deque` |
| **`a.insert(i, x)`** | **O(n)** | |
| `a.remove(x)` | O(n) | first occurrence |
| `x in a` | **O(n)** | linear scan — use a set |
| `a.index(x)` | O(n) | |
| `len(a)` | O(1) | stored |
| `a[i:j]` | O(j − i) | **slicing copies** |
| `a.sort()` | O(n log n) | Timsort, **stable** |
| `min`/`max`/`sum` | O(n) | |
| `a + b` | O(n + m) | builds a new list |

**The two that cause most TLEs:** `a.pop(0)` in a BFS (use `collections.deque`) and `x in a` inside a loop (use a `set`).

---

## 2. dict and set

| Operation | Average | Worst |
|---|---|---|
| `d[k]`, `d[k] = v`, `k in d` | O(1) | O(n) |
| `d.get(k, default)` | O(1) | O(n) |
| `del d[k]` | O(1) | O(n) |
| `s.add`, `s.discard`, `x in s` | O(1) | O(n) |
| `s1 & s2` | O(min(len)) | |
| `s1 | s2` | O(len(s1) + len(s2)) | |
| iteration | O(n) | insertion order for dict |

Keys must be **hashable**: `int`, `str`, `tuple`, `frozenset` — not `list`, `dict` or `set`.

---

## 3. collections

| Type | Purpose | Key complexities |
|---|---|---|
| `deque` | double-ended queue | `append`/`appendleft`/`pop`/`popleft` **O(1)**; indexing in the middle O(n) |
| `Counter` | frequency map | construction O(n); `most_common(k)` O(n log k) |
| `defaultdict` | auto-creating dict | same as dict; **creates the key on read** |
| `OrderedDict` | ordered dict | `move_to_end` O(1), `popitem(last=False)` O(1) — useful for LRU |

```python
from collections import deque
q = deque([1, 2, 3])
q.append(4); q.appendleft(0)
q.popleft(); q.pop()
q = deque(maxlen=k)        # a fixed-size sliding window, auto-evicting
```

**Use `deque` for every BFS.** `list.pop(0)` makes a BFS O(V²).

---

## 4. heapq — a min-heap over a plain list

| Call | Complexity |
|---|---|
| `heappush` | O(log n) |
| `heappop` | O(log n) |
| `h[0]` (peek) | O(1) |
| `heapify(a)` | **O(n)** |
| `heappushpop` / `heapreplace` | O(log n), one sift |
| `nlargest(k, a)` / `nsmallest(k, a)` | O(n log k) |

```python
import heapq
h = []
heapq.heappush(h, (priority, tiebreak, payload))
heapq.heappush(h, -x)          # MAX-heap: negate on push and on pop
```

**`heapq` is min-only.** Negate for a max-heap, and always negate back on pop. Push tuples with a unique tiebreaker so Python never has to compare the payload objects.

---

## 5. bisect — binary search on a sorted list

| Call | Complexity | Equivalent |
|---|---|---|
| `bisect_left(a, x)` | O(log n) | C++ `lower_bound` |
| `bisect_right(a, x)` | O(log n) | C++ `upper_bound` |
| `insort(a, x)` | **O(n)** | search is O(log n), the insert shift is O(n) |

```python
count_of_x = bisect_right(a, x) - bisect_left(a, x)
first_ge   = bisect_left(a, x)
```

`insort` in a loop is O(n²). If you need an ordered structure with fast insertion, use a heap, or a `SortedList` where available.

---

## 6. string

| Operation | Complexity |
|---|---|
| `s[i]` | O(1) |
| `s + t` | O(n + m) — builds a new string |
| **`s += c` in a loop** | **O(n²) total** |
| `''.join(parts)` | O(total length) |
| `s[i:j]` | O(j − i) |
| `s.split()` | O(n) |
| `sub in s` | O(n·m) worst |
| `s.replace` | O(n) |

**The single most common Python TLE is `s += c` inside a loop.** Build a list and join.

---

## 7. Cost multipliers to keep in mind

Python is roughly **30–100× slower** than C++ for the same algorithm. Rules of thumb:

| n and complexity | Verdict |
|---|---|
| 10⁵ with O(n log n) | fine |
| 10⁶ with O(n) | fine with fast I/O |
| 10⁶ with O(n log n) | **borderline** |
| 10⁷ anything | **TLE** |
| Tight nested loops | avoid — push work into library calls |

**Speed-ups that work:**
- `sys.stdin.readline`; build output and write once
- comprehensions and `map` instead of explicit loops (the loop runs in C)
- `set`/`dict` lookups instead of `in list`
- `deque` instead of `list.pop(0)`
- avoid attribute lookups in the innermost loop (`app = a.append` then call `app(x)`)
- `@lru_cache` on a recursive function instead of a hand-rolled dict
- numpy for numeric bulk work, where it is available

---

## 8. Choosing a structure

| Requirement | Choice |
|---|---|
| Indexed access, append at the end | `list` |
| Insert/remove at both ends | `deque` |
| Membership testing | `set` |
| Counting | `Counter` |
| Grouping | `defaultdict(list)` |
| Repeated smallest/largest | `heapq` |
| Binary search on sorted data | `bisect` |
| Ordered map with range queries | no stdlib option — sort, or use `bisect` on a sorted list |

**The gap worth knowing:** Python's standard library has no balanced BST, so there is no direct `std::map` / `TreeMap` equivalent. Say this explicitly if an interviewer asks for O(log n) ordered operations — then reach for a heap, a sorted list with `bisect`, or a different formulation.
