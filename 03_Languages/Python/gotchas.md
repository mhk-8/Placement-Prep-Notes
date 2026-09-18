# Python — Gotchas

> Python has no compiler to catch you. Every item here produces a silently wrong answer or a TLE.

---

## Aliasing and mutability

- **`[[0] * m] * n` creates n references to the *same* row.** Writing `grid[0][0] = 1` sets that column in every row. Use `[[0] * m for _ in range(n)]`. This is the most destructive Python bug in competitive programming, because the code looks right and the output is nonsense.
- `b = a` for a list is an **alias**, not a copy. Use `a[:]`, `list(a)` or `copy.copy(a)` for a shallow copy, `copy.deepcopy(a)` for nested structures.
- **Mutable default arguments** are created once at function definition: `def f(acc=[])` accumulates across calls. Use `def f(acc=None): acc = acc or []`.
- Mutating a list while iterating it skips elements. Iterate over a copy, or build a new list.
- `dict.values()` and `list` slices of nested lists still share the inner objects — a shallow copy is shallow all the way down.

## Numbers

- `//` floors toward **−∞**: `-7 // 2 == -4`, where C++ gives −3. `%` follows the divisor's sign: `-7 % 2 == 1`.
- `/` is **always** float division, even for two ints. `5 / 2` is 2.5, and for large ints it loses precision.
- `int(n ** 0.5)` can be off by one through floating-point error. Use `math.isqrt(n)`.
- `0.1 + 0.2 != 0.3` — compare floats with `math.isclose` or an epsilon.
- `round()` uses banker's rounding: `round(0.5)` is 0 and `round(2.5)` is 2.
- `True == 1` and `False == 0`, so `sum([True, True])` is 2 — occasionally useful, occasionally a surprise.
- No overflow, but very large ints are slow; take a modulus as you go.

## Performance traps

- **`s += c` in a loop is O(n²)** — strings are immutable. Build a list and `''.join(...)`.
- **`list.pop(0)` is O(n)** — a BFS written with it is O(V²). Use `collections.deque`.
- **`x in list` is O(n)** — inside a loop that is O(n²). Use a `set`.
- **Slicing copies:** `s[i:]` inside a loop is O(n²).
- `list.insert(0, x)` is O(n); `bisect.insort` in a loop is O(n²).
- Attribute lookups in the innermost loop are not free: hoist `app = result.append`.
- Recursion in Python is slow *and* depth-limited; convert hot recursive code to iteration.

## Scoping and closures

- **Late binding in closures:** `fs = [lambda: i for i in range(3)]` gives three functions that all return 2. Capture explicitly: `lambda i=i: i`.
- Assigning to a name inside a function makes it local for the whole function, so reading it before the assignment raises `UnboundLocalError`. Use `nonlocal` or `global` deliberately.
- Comprehensions have their own scope in Python 3, so the loop variable does not leak.

## Collections

- `defaultdict` **creates the key on read**: `if dd[k]:` inserts `k`. Use `dd.get(k)` when you must not mutate.
- `dict.keys()` and `.items()` are **views**, not lists — mutating the dict while iterating a view raises.
- `set` is unordered; do not rely on iteration order (unlike `dict`, which preserves insertion order since 3.7).
- Lists, dicts and sets are **unhashable** — convert to `tuple` or `frozenset` to use them as keys.
- `sort()` returns `None` and mutates; `sorted()` returns a new list. `a = a.sort()` sets `a` to `None`.
- `heapq` is min-only; forgetting to negate back after popping from a negated max-heap gives sign-flipped answers.
- `heappush(h, (val, obj))` raises `TypeError` when two `val`s tie and Python falls through to comparing the objects. Add a unique counter.

## Strings

- `input()` and `sys.stdin.readline()` both keep the trailing newline when reading text — `.strip()` it.
- `s.find(sub)` returns −1 when absent; `s.index(sub)` raises. Know which you called.
- `str.replace` returns a new string; strings are never modified in place.
- `"10" < "9"` is True — lexicographic comparison, not numeric.
- `list(s)` splits into characters, which is usually what you want for in-place editing.

## Recursion

- The default recursion limit is **1000**. `sys.setrecursionlimit(300000)` raises the interpreter's check but not the OS stack, so very deep recursion can still crash — prefer an iterative version beyond ~10⁵ depth.
- `@lru_cache` keys on the arguments, so they must be hashable — pass tuples, not lists.
- `lru_cache` persists **across test cases**; call `f.cache_clear()` between them or answers bleed.

## Miscellaneous

- `is` compares identity, `==` compares value. `a is b` for small ints happens to be True because of interning, which makes the bug hide until the values grow.
- `and`/`or` return an **operand**, not a bool: `0 or []` is `[]`.
- A trailing comma makes a tuple: `x = 1,` is `(1,)`.
- Integer division on a negative in a hash computation gives a different bucket than C++ would — normalise with `% m` explicitly.

## The five that cost the most marks

1. `[[0] * m] * n` aliasing the rows
2. `s += c` in a loop causing a TLE
3. `list.pop(0)` in a BFS
4. `x in list` instead of `x in set`
5. `lru_cache` not cleared between test cases
