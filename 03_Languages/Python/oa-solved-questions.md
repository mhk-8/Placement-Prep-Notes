# Python — Solved OA Questions

> 16 questions in OA style, with every option explained. Python language questions cluster around **aliasing, integer semantics and hidden complexity** — the three places where readable code does something other than what it looks like.
> Cover the answers. **45 seconds** each.

---

**Q1.** What is printed?
```python
grid = [[0] * 3] * 2
grid[0][0] = 1
print(grid)
```
(a) `[[1, 0, 0], [0, 0, 0]]`  (b) `[[1, 0, 0], [1, 0, 0]]`  (c) `[[1, 1, 1], [1, 1, 1]]`  (d) Error

<details><summary>Answer</summary>

**(b) `[[1, 0, 0], [1, 0, 0]]`.**

`[x] * 2` copies the **reference** twice, so the outer list holds two pointers to the *same* inner list. Writing through one is visible through the other.

**The fix:** `[[0] * 3 for _ in range(2)]`, which evaluates the inner expression separately each time.

This is the most destructive Python bug in competitive programming, because the code looks correct and the wrongness only shows up in the output.
</details>

---

**Q2.** What is printed?
```python
def f(x, acc=[]):
    acc.append(x)
    return acc

print(f(1), f(2))
```
(a) `[1] [2]`  (b) `[1] [1, 2]`  (c) `[1, 2] [1, 2]`  (d) `[2] [2]`

<details><summary>Answer</summary>

**(c) `[1, 2] [1, 2]`.**

The default `[]` is created **once**, when the function is defined, and reused across calls. So `f(1)` makes it `[1]` and `f(2)` makes it `[1, 2]` — and since both calls return *the same object*, both printed values show the final state.

**The fix:** `def f(x, acc=None): acc = [] if acc is None else acc`.

(b) is the tempting answer because it assumes `print` snapshots its arguments. It does not — it holds references.
</details>

---

**Q3.** What is printed?
```python
print(-7 // 2, -7 % 2, 7 // -2, 7 % -2)
```
(a) `-3 -1 -3 1`  (b) `-4 1 -4 -1`  (c) `-4 -1 -4 1`  (d) `-3 1 -3 -1`

<details><summary>Answer</summary>

**(b) `-4 1 -4 -1`.**

Python **floors toward −∞**, and the remainder takes the sign of the **divisor** so that `(a // b) * b + a % b == a` holds.

**C++ and Java truncate toward zero**, giving `-3` and `-1` for the first two — option (a). This divergence silently breaks hash-bucket and cyclic-index logic ported between languages; normalise with `((x % m) + m) % m` when it matters.
</details>

---

**Q4.** What is printed?
```python
a = [1, 2, 3]
b = a
b.append(4)
print(len(a))
```
(a) 3  (b) 4  (c) 0  (d) Error

<details><summary>Answer</summary>

**(b) 4.**

`b = a` binds a second name to the **same list object** — Python has reference semantics for containers, unlike C++ where `auto b = a` copies.

**To copy:** `b = a[:]`, `b = list(a)` or `b = copy.copy(a)`. For nested structures you need `copy.deepcopy(a)`, since a shallow copy still shares the inner lists.
</details>

---

**Q5.** What is printed?
```python
fs = [lambda: i for i in range(3)]
print([f() for f in fs])
```
(a) `[0, 1, 2]`  (b) `[2, 2, 2]`  (c) `[0, 0, 0]`  (d) Error

<details><summary>Answer</summary>

**(b) `[2, 2, 2]`.**

Closures capture the **variable**, not its value at creation time. By the time any lambda runs, `i` has finished the loop at 2.

**The fix:** bind it as a default argument — `[lambda i=i: i for i in range(3)]` — which evaluates `i` at definition time.

This bites in real code whenever you build callbacks or comparator functions in a loop.
</details>

---

**Q6.** What is printed?
```python
print(0.1 + 0.2 == 0.3, round(0.5), round(1.5), round(2.5))
```
(a) `True 1 2 3`  (b) `False 0 2 2`  (c) `False 1 2 3`  (d) `True 0 2 2`

<details><summary>Answer</summary>

**(b) `False 0 2 2`.**

Two separate traps. Neither 0.1 nor 0.2 is exactly representable in binary, so the sum is 0.30000000000000004 — compare with `math.isclose` instead.

And Python uses **banker's rounding** (round-half-to-even), so 0.5 → 0, 1.5 → 2, 2.5 → 2. It reduces statistical bias when rounding many values, and it surprises everyone the first time.
</details>

---

**Q7.** What is printed?
```python
a = [3, 1, 2]
b = a.sort()
print(b)
```
(a) `[1, 2, 3]`  (b) `None`  (c) `[3, 1, 2]`  (d) Error

<details><summary>Answer</summary>

**(b) `None`.**

`list.sort()` sorts **in place** and returns `None`. `sorted(a)` returns a new sorted list.

The damaging version of this bug is `a = a.sort()`, which silently sets `a` to `None` — and the failure then appears several lines later at an unrelated `len(a)`.

The same convention applies to `reverse()`, `append()` and `extend()`: mutating methods return `None`.
</details>

---

**Q8.** What is printed?
```python
from collections import defaultdict
d = defaultdict(int)
if d[5] == 0:
    print(len(d))
```
(a) 0  (b) 1  (c) 5  (d) Nothing

<details><summary>Answer</summary>

**(b) 1.**

A `defaultdict` **creates the key on read**. Testing `d[5]` inserts `5 → 0`, so the dict now has one entry.

**Use `d.get(5, 0)`** when you must not mutate. This is the exact Python analogue of C++'s `mp[k]` insertion trap, and it silently inflates memory and corrupts `len()`-based logic on large inputs.
</details>

---

**Q9.** What is printed?
```python
print("10" < "9", [1, 2] < [1, 3])
```
(a) `True True`  (b) `False True`  (c) `True False`  (d) Error

<details><summary>Answer</summary>

**(a) `True True`.**

Strings compare **lexicographically**, so `"1" < "9"` decides it on the first character regardless of numeric value. Lists compare elementwise, also lexicographically, so `[1,2] < [1,3]` compares 2 against 3.

The practical consequence: sorting a list of numeric **strings** gives `["1", "10", "2"]`. Convert with `key=int` when numeric order is what you want.
</details>

---

**Q10.** What is the total time complexity of building a string of n characters with `s += c` in a loop?
(a) O(n)  (b) O(n log n)  (c) **O(n²)**  (d) O(1)

<details><summary>Answer</summary>

**(c) O(n²).**

Strings are immutable, so each `+=` allocates a new string and copies everything accumulated so far: 1 + 2 + … + n = O(n²).

**The fix:** collect into a list and `''.join(parts)`, which is O(total length).

This is the single most common cause of a Python TLE on an otherwise correct solution, and it is worth checking for reflexively before every submission.
</details>

---

**Q11.** What is printed?
```python
x = [1, 2, 3, 4]
for i in x:
    if i % 2 == 0:
        x.remove(i)
print(x)
```
(a) `[1, 3]`  (b) `[1, 3, 4]`  (c) `[1, 2, 3, 4]`  (d) Error

<details><summary>Answer</summary>

**(a) `[1, 3]`** — and it is correct **by accident**, not by design.

Trace it. The loop walks by internal index. Index 0 → 1, kept. Index 1 → 2, removed, leaving `[1, 3, 4]`. Index 2 → now `4` (because everything shifted left), removed, leaving `[1, 3]`. Index 3 is past the end, so the loop stops — **3 was never examined**.

Here the skipped element happened to be odd. Change the input to `[1, 2, 4, 5]` and the answer becomes wrong. **Never mutate a list while iterating it**: iterate over a copy (`for i in x[:]`) or build a new list with a comprehension.
</details>

---

**Q12.** What is printed?
```python
print(bool([]), bool([0]), 0 or [], [] or 0)
```
(a) `False False [] 0`  (b) `False True [] 0`  (c) `False True 0 []`  (d) `True True [] 0`

<details><summary>Answer</summary>

**(b) `False True [] 0`.**

An empty list is falsy, but a list *containing* a falsy value is truthy — only emptiness matters.

And `or`/`and` return an **operand**, not a boolean: `0 or []` evaluates `0` (falsy) and returns the second operand `[]`; `[] or 0` returns `0`.

That is why `x = arg or []` is a common default-value idiom, and also why it misfires when `0` or `""` is a legitimate value — use `if arg is None` instead.
</details>

---

**Q13.** `heapq` in Python provides
(a) a max-heap  (b) a min-heap  (c) either, by parameter  (d) a balanced BST

<details><summary>Answer</summary>

**(b) a min-heap** — and only a min-heap.

For a max-heap, negate on push and negate again on pop: `heapq.heappush(h, -x)` then `-heapq.heappop(h)`. Forgetting the second negation gives sign-flipped answers that pass the sample and fail everything else.

(d) matters too: **Python's standard library has no balanced BST**, so there is no `std::map` / `TreeMap` equivalent. Say that explicitly if asked for ordered O(log n) operations, then offer a heap or `bisect` on a sorted list.
</details>

---

**Q14.** What is Python's default recursion limit, and does raising it fully solve deep recursion?
(a) 1000; yes  (b) 1000; no — the OS stack can still overflow  (c) 10000; yes  (d) unlimited

<details><summary>Answer</summary>

**(b) 1000, and raising it is not a complete fix.**

`sys.setrecursionlimit(300000)` lifts the *interpreter's* guard, but each Python frame is heavy and the actual OS stack can still be exhausted, crashing the process rather than raising an exception.

**For depths beyond roughly 10⁵, convert to an iterative algorithm with an explicit stack.** A recursive DFS on a 10⁵-node path graph is the standard way to meet this.
</details>

---

**Q15.** What is printed?
```python
import copy
a = [[1, 2], [3, 4]]
b = copy.copy(a)
c = copy.deepcopy(a)
a[0][0] = 99
print(b[0][0], c[0][0])
```
(a) `1 1`  (b) `99 1`  (c) `99 99`  (d) `1 99`

<details><summary>Answer</summary>

**(b) `99 1`.**

`copy.copy` is **shallow**: it builds a new outer list whose elements are the *same* inner list objects, so the mutation is visible through `b`. `copy.deepcopy` recreates the whole object graph, so `c` is independent.

`a[:]`, `list(a)` and `a.copy()` are all shallow — which is exactly why `[[0]*m]*n` in Q1 misbehaves.
</details>

---

**Q16.** Which of these is **O(1)** on a Python list?
(a) `a.pop(0)`  (b) `a.insert(0, x)`  (c) `a.append(x)`  (d) `x in a`

<details><summary>Answer</summary>

**(c) `a.append(x)`** — amortised O(1) through doubling.

(a) and (b) are **O(n)**, because every remaining element shifts. (d) is **O(n)**, a linear scan.

**The two that cause real TLEs:** `a.pop(0)` in a BFS makes it O(V²) — use `collections.deque`, whose `popleft` is O(1). And `x in a` inside a loop is O(n²) — use a `set`, where membership is O(1) on average.
</details>

---

## Scoring

| Score /16 | Reading |
|---|---|
| 14+ | Python fluency is OA-ready |
| 10–13 | Re-read `gotchas.md`; drill the aliasing and complexity items |
| 6–9 | Work `syntax-reference.md` and `library-complexities.md` properly |
| < 6 | Consider whether Python should be your primary language (`language-choice.md`) |

**Diagnostic:** misses on Q1, Q4 and Q15 mean reference semantics have not landed — that one idea explains all three, and it causes more silent wrong answers in Python than anything else.
