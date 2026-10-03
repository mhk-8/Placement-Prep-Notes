
# Python Standard Library — One Pager

> **Use:** your ML-interview and quick-prototyping language. Fast to write, slow to run —
> know where the C-implemented shortcuts are.

---

## 1. Fast I/O and the boilerplate ⭐

```python
import sys
input = sys.stdin.readline                       # ⭐ 10x faster than builtin input() in a loop
data = sys.stdin.buffer.read().split()           # fastest: read everything, then index
n = int(input())
a = list(map(int, input().split()))
sys.stdout.write("\n".join(map(str, out)) + "\n")  # ⭐ one write, not n prints
sys.setrecursionlimit(300000)                    # ⚠️ default is 1000 — DFS will die without this
```

> ⚠️ **Python is ~10-50x slower than C++.** At `n = 10⁵` with an O(n log n) solution you are fine;
> at `n = 10⁶` with anything non-vectorised you are not. Use C++ for hard algorithmic OAs.

---

## 2. Built-in types and their complexities

| Operation | list | dict / set | deque | heapq (list) |
|---|---|---|---|---|
| index / lookup | O(1) | O(1) avg | O(1) ends, O(n) middle | O(1) `[0]` |
| append / add | O(1) am. | O(1) avg | O(1) both ends | O(log n) push |
| pop end / pop | O(1) | O(1) | O(1) both ends | O(log n) pop |
| insert / pop(0) | **O(n)** ⚠️ | — | O(1) `appendleft` | — |
| `in` | **O(n)** ⚠️ | O(1) | O(n) | O(n) |
| sort | O(n log n) | — | — | heapify **O(n)** |

> ⭐ **The single most common Python performance bug:** `x in my_list` inside a loop, or
> `list.pop(0)`. Use a `set` and a `collections.deque`.

---

## 3. `collections` ⭐⭐

```python
from collections import Counter, defaultdict, deque, OrderedDict, namedtuple

Counter("aabbbc")                  # Counter({'b':3,'a':2,'c':1})
Counter(a).most_common(k)           # k most frequent — O(n log k) ⭐
c1 - c2, c1 + c2, c1 & c2           # multiset subtract / add / intersect
defaultdict(list)                   # graph: g[u].append(v) with no key check
defaultdict(int)                    # frequency counting
dq = deque(); dq.append(x); dq.appendleft(x); dq.pop(); dq.popleft()   # all O(1) ⭐
deque(maxlen=k)                     # fixed sliding window, auto-evicts
Point = namedtuple("Point", "x y")
```

## 4. `heapq` (min-heap only) ⭐

```python
import heapq
heapq.heappush(h, x);  x = heapq.heappop(h);  h[0]            # peek min
heapq.heapify(lst)                                            # in place, O(n) ⭐
heapq.nlargest(k, it) / nsmallest(k, it)                      # O(n log k)
heapq.heappushpop(h, x);  heapq.heapreplace(h, x)
# MAX-heap: push negatives ⭐
heapq.heappush(h, -x); top = -h[0]
# tuples: compared lexicographically → (priority, tiebreak, payload)
heapq.heappush(h, (dist, node))
```

## 5. `bisect` — binary search on a sorted list

```python
import bisect
bisect.bisect_left(a, x)    # first index with a[i] >= x
bisect.bisect_right(a, x)   # first index with a[i] >  x   (== count of <= x ⭐)
bisect.insort(a, x)         # insert keeping order — O(n) because of the shift ⚠️
```

## 6. `itertools` and `functools`

```python
from itertools import permutations, combinations, product, accumulate, groupby, chain
permutations(a, r) · combinations(a, r) · combinations_with_replacement(a, r)
product(range(3), repeat=n)                  # n nested loops / base-3 enumeration ⭐
list(accumulate(a))                          # prefix sums ⭐
accumulate(a, max)                           # running maximum
groupby(sorted(a, key=f), key=f)             # ⚠️ must be sorted by the same key first
chain.from_iterable(nested)

from functools import lru_cache, cache, reduce, cmp_to_key
@cache                                       # ⭐ memoised DP in one line (3.9+); lru_cache(None) before
def f(i, j): ...
sorted(a, key=cmp_to_key(my_cmp))            # 3-way comparator → key
reduce(lambda x,y: x*y % M, a, 1)
```

## 7. `math` and numbers

```python
import math
math.gcd(a,b) · math.lcm(a,b) · math.isqrt(n)        # ⭐ isqrt is exact integer sqrt, no float error
math.comb(n,k) · math.perm(n,k) · math.factorial(n)
math.inf · -math.inf · math.ceil · math.floor · math.log2
pow(a, b, m)                                          # ⭐ modular exponentiation, C-speed
divmod(a, b)
a // b   # floors toward -inf: -7 // 2 == -4   ⚠️ differs from C++ truncation (-3)
a % b    # sign follows the DIVISOR in Python: -7 % 3 == 2   ⚠️ C++ gives -1
int("ff", 16) · bin(x) · hex(x) · x.bit_length() · x.bit_count()   # bit_count 3.10+
from fractions import Fraction; from decimal import Decimal
```
> ⭐ **Python ints are arbitrary precision** — no overflow. This is a real advantage in
> combinatorics problems, and the reason `pow(a,b,m)` exists.

## 8. Strings

```python
s.split() · s.split(",") · ",".join(lst) · s.strip() · s.replace(a,b)
s.startswith/endswith · s.find (−1) · s.index (raises) · s.count
s.isdigit/isalpha/isalnum · s.lower/upper/title · s.zfill(5)
f"{x:.3f}  {x:>10}  {x:,}  {x:08b}"
ord('a') == 97 · chr(97) == 'a' · "".join(sorted(s))          # anagram key ⭐
s[::-1]                                                        # reverse
# ⚠️ strings are immutable — building with += in a loop is O(n²). Collect into a list and join.
```

## 9. Sorting idioms ⭐

```python
sorted(a, key=lambda p: (-p[1], p[0]))     # 2nd desc, then 1st asc ⭐ the standard trick
a.sort(reverse=True)                        # in place
sorted(words, key=len)
# Timsort is STABLE → sort by secondary key first, then primary, for multi-key without tuples
max(items, key=lambda x: x.score)
```

## 10. Comprehensions and unpacking

```python
[f(x) for x in a if p(x)]      {k: v for k, v in pairs}      {x for x in a}
(x for x in a)                 # generator — lazy, O(1) memory ⭐
[[0]*m for _ in range(n)]      # ⚠️ NEVER [[0]*m]*n — all rows alias the same list
a, *rest = lst                 # extended unpacking
for i, x in enumerate(a, 1)    # 1-indexed
for x, y in zip(a, b)          # zip(*matrix) transposes ⭐
```

---

## 11. Language semantics interviewers probe ⭐⭐

| Concept | One-line answer |
|---|---|
| `list` vs `tuple` | Mutable vs immutable; tuples are hashable → usable as dict keys |
| Mutable default argument | `def f(x=[])` shares one list across calls ⚠️ classic bug |
| `is` vs `==` | Identity vs equality; small-int and short-string interning makes `is` *look* right |
| Shallow vs deep copy | `list(a)` / `a[:]` copies one level; `copy.deepcopy` recurses |
| GIL | One thread executes Python bytecode at a time → threads help I/O, not CPU ⭐ |
| Threads vs `multiprocessing` | Use processes for CPU-bound work; they bypass the GIL |
| Generator vs list | Lazy, O(1) memory, single pass |
| Decorator | A function returning a wrapped function; `@f` is `g = f(g)` |
| Closure | Inner function capturing enclosing scope; `nonlocal` to rebind |
| `*args` / `**kwargs` | Positional tuple / keyword dict |
| Duck typing | Behaviour over declared type |
| MRO | C3 linearisation; `Class.__mro__` |
| `__slots__` | Removes `__dict__` → less memory per instance ⭐ |
| Context manager | `__enter__` / `__exit__`; `with` guarantees cleanup |
| LEGB scoping | Local → Enclosing → Global → Builtins |
| `@staticmethod` vs `@classmethod` | No implicit arg vs receives `cls` |
| Everything is an object | Functions and classes are first-class values |

---

## 12. NumPy minimum (for ML rounds) ⭐

```python
import numpy as np
a = np.array(lst); np.zeros((n,m)); np.eye(n); np.arange(n); np.linspace(0,1,5)
a.shape · a.reshape(-1, 3) · a.T · a.astype(np.float32)
a @ b            # matmul    a * b   # elementwise ⚠️ know the difference cold
a.sum(axis=0) · a.mean(axis=1) · a.argmax(axis=1) · np.where(cond, x, y)
BROADCASTING: shapes align from the RIGHT; dimension must be equal or 1 ⭐ a frequent question
np.random.seed(0)        # reproducibility — mention this unprompted in ML interviews
```

---

## Recall questions
1. Why is `list.pop(0)` a bug in a loop, and what replaces it?
2. What does `-7 % 3` evaluate to in Python, and in C++? Why do they differ?
3. Write a max-heap using `heapq`.
4. What is wrong with `[[0]*m]*n`?
5. Explain the GIL in one sentence and say what it means for CPU-bound work.
6. How do you sort by one key descending and another ascending in a single call?
7. What does `bisect_right` return, and what quantity does it equal?
