# Python — Syntax Reference

> The syntax and idioms you must produce without thinking. Python's advantage is typing speed; that advantage only exists if the idioms are automatic.

---

## 1. The starter file

```python
import sys
from collections import defaultdict, Counter, deque, OrderedDict
import heapq, bisect, math, itertools
from functools import lru_cache, cmp_to_key

input = sys.stdin.readline          # fast input — note: keeps the '\n'
sys.setrecursionlimit(300000)

def solve():
    n = int(input())
    a = list(map(int, input().split()))
    # ...

for _ in range(int(input())):
    solve()
```

`sys.stdin.readline` is several times faster than the builtin `input()` because it skips prompt handling and per-call flushing. It **keeps the trailing newline**, so use `.strip()` when reading strings (numbers are fine — `int()` ignores whitespace).

For very heavy output, build a list and `sys.stdout.write('\n'.join(parts))` rather than calling `print` in a loop.

---

## 2. Types and numbers

```python
int          # arbitrary precision — NO overflow, but big ints are slow
float        # 64-bit double
bool         # a subclass of int: True == 1
str          # immutable
bytes        # immutable byte sequence

10 / 3       # 3.333...  true division, always float
10 // 3      # 3         floor division
-7 // 2      # -4        floors toward -inf  (C++ gives -3)
-7 % 2       # 1         result takes the sign of the DIVISOR (C++ gives -1)
divmod(7, 2) # (3, 1)
2 ** 100     # exact — arbitrary precision
pow(2, 100, MOD)   # fast modular exponentiation, O(log e)
float('inf'), float('-inf')
```

**No overflow is Python's biggest safety advantage and its biggest speed cost.** A loop building a 10⁶-digit integer will be slow even though it is correct.

---

## 3. Sequences and slicing

```python
a = [1, 2, 3, 4, 5]
a[0], a[-1]          # 1, 5
a[1:4]               # [2,3,4]     — start inclusive, end EXCLUSIVE
a[:3], a[2:], a[:]   # prefix, suffix, shallow copy
a[::-1]              # reversed copy
a[::2]               # every second element
a[1:4] = [9]         # slice assignment changes the length

a.append(x); a.pop(); a.pop(0)        # pop(0) is O(n) — use deque
a.insert(i, x)                        # O(n)
a.extend(b); a += b
a.remove(x)                           # removes the FIRST occurrence, O(n)
a.index(x)                            # first index, raises ValueError if absent
a.count(x); a.sort(); a.reverse()
sorted(a); reversed(a)                # return new / an iterator
```

**Slicing copies**, so `s[i:]` inside a loop turns O(n) into O(n²). Use indices instead.

---

## 4. Comprehensions

```python
[x * 2 for x in a]
[x for x in a if x > 0]
[x if x > 0 else 0 for x in a]        # note: the conditional goes BEFORE `for`
[[0] * m for _ in range(n)]           # n x m grid — see gotchas for why not [[0]*m]*n
{x: x**2 for x in range(5)}
{x % 3 for x in a}                    # set comprehension
(x * 2 for x in a)                    # GENERATOR — lazy, no list built
```

Comprehensions are meaningfully faster than an append loop, because the loop runs in C rather than in the interpreter.

---

## 5. Strings

```python
s = "hello world"
s.split()            # on any whitespace
s.split(',')         # on a delimiter
','.join(parts)      # the ONLY correct way to build a string in a loop
s.strip(); s.lstrip(); s.rstrip()
s.replace(a, b); s.find(sub)      # find returns -1 if absent
s.startswith(p); s.endswith(q)
s.upper(); s.lower(); s.isdigit(); s.isalpha()
s.count(sub)
ord('a'), chr(97)                 # 97, 'a'
f"{x} and {y:.2f}"                # f-strings
list(s)                           # to a mutable list of chars
```

**Strings are immutable**, so `s += c` in a loop copies the whole string each time: O(n²). Collect into a list and `''.join(...)`.

---

## 6. Dicts and sets

```python
d = {}
d[k] = v
d.get(k, default)                 # read WITHOUT inserting
d.setdefault(k, []).append(x)
d.pop(k, None)
k in d                            # O(1) average
d.keys(); d.values(); d.items()
for k, v in d.items(): ...

from collections import defaultdict, Counter
dd = defaultdict(list)            # dd[k] creates [] on first access
cnt = Counter(a)
cnt.most_common(k)                # top-k by frequency
cnt2 = cnt1 + cnt3                # Counters add elementwise

s = set(a)
s.add(x); s.discard(x)            # discard does NOT raise if absent
s1 | s2, s1 & s2, s1 - s2, s1 ^ s2
frozenset(a)                      # hashable — usable as a dict key
```

Dicts preserve insertion order (3.7+), which is convenient but should be stated explicitly if an interview answer depends on it.

---

## 7. Tuples and unpacking

```python
t = (1, 2, 3)                     # immutable, hashable → usable as a dict key
a, b = b, a                       # swap
x, *rest = [1, 2, 3, 4]           # x = 1, rest = [2,3,4]
for i, v in enumerate(a): ...
for x, y in zip(a, b): ...
for i, (x, y) in enumerate(zip(a, b)): ...
```

`zip` stops at the shorter sequence; `itertools.zip_longest` pads instead.

---

## 8. Functions

```python
def f(a, b=0, *args, **kwargs):
    return a + b

lambda x: x * 2

sorted(a, key=lambda x: (x[1], -x[0]))     # by second asc, then first desc
sorted(a, key=len, reverse=True)
max(a, key=lambda x: x[1])

from functools import cmp_to_key
def cmp(x, y):
    return -1 if x + y > y + x else (1 if x + y < y + x else 0)
sorted(strs, key=cmp_to_key(cmp))
```

`key=` is evaluated once per element (fast); `cmp_to_key` is O(n log n) comparisons through Python-level calls (slow) — use it only when the order genuinely cannot be expressed as a key.

---

## 9. Control flow

```python
for i in range(n): ...
for i in range(n-1, -1, -1): ...          # descending
while cond: ...

for x in a:
    if bad: break
else:
    print("loop finished without break")   # the for/else clause

try:
    ...
except ValueError as e:
    ...
finally:
    ...

with open('f.txt') as fh:                  # context manager, auto-closes
    data = fh.read()
```

---

## 10. Classes

```python
class Node:
    __slots__ = ('val', 'next')            # saves memory, blocks attribute creation
    def __init__(self, val=0, nxt=None):
        self.val, self.next = val, nxt
    def __repr__(self):  return f"Node({self.val})"
    def __lt__(self, o): return self.val < o.val      # enables sorting and heapq
    def __eq__(self, o): return self.val == o.val
    def __hash__(self):  return hash(self.val)        # needed if __eq__ is defined
```

Defining `__eq__` without `__hash__` makes the class **unhashable**, so it can no longer be a dict key or set member — Python's version of Java's equals/hashCode contract.

---

## 11. Useful builtins and modules

```python
sum(a), min(a), max(a), abs(x), round(x, 2)
any(cond for x in a), all(cond for x in a)
sorted(a, key=..., reverse=True)
len(a), range(a, b, step)
map(int, s.split()), filter(pred, a)

math.gcd(a,b), math.lcm(a,b), math.isqrt(n), math.comb(n,r), math.factorial(n)
math.inf, math.ceil, math.floor, math.log2

itertools.permutations(a), combinations(a, r), product(a, repeat=n)
itertools.accumulate(a)          # prefix sums
itertools.groupby(sorted(a))

bisect.bisect_left(a, x)         # lower_bound
bisect.bisect_right(a, x)        # upper_bound
bisect.insort(a, x)              # insert keeping sorted — O(n) for the shift

heapq.heappush/heappop/heapify/nlargest/nsmallest
```

`math.isqrt(n)` is exact integer square root — prefer it to `int(n ** 0.5)`, which can be off by one through floating-point rounding.
