# Python — Flashcards

## Questions

1. Give the fast-I/O preamble, and the one thing to remember about `sys.stdin.readline`.
2. Why is `[[0] * m] * n` wrong, and what replaces it?
3. What does `b = a` do for a list, and how do you actually copy one?
4. Shallow vs deep copy — give the failure case.
5. What are `-7 // 2` and `-7 % 2`, and how do C++ and Java differ?
6. Why is `s += c` in a loop O(n²), and what replaces it?
7. What is the complexity of `a.pop(0)`, `a.insert(0, x)`, `a.append(x)` and `x in a`?
8. What does a `defaultdict` do on a **read** of a missing key?
9. What does `list.sort()` return?
10. What goes wrong with mutable default arguments?
11. Explain the late-binding closure trap and its fix.
12. What does `round(0.5)` return, and why?
13. Is `heapq` a min- or max-heap, and how do you get the other?
14. Why can `heappush(h, (val, obj))` raise a TypeError?
15. What is Python's default recursion limit, and why is raising it not a complete fix?
16. What must you do with `lru_cache` between test cases?
17. Which types are hashable and which are not?
18. What do `and` and `or` return?
19. What ordered data structure does Python's standard library lack?
20. Name five ways to make Python code meaningfully faster without changing the algorithm.

---

## Answers

1. `import sys; input = sys.stdin.readline; sys.setrecursionlimit(300000)`. It keeps the trailing newline, so `.strip()` string reads (numeric conversions ignore whitespace anyway).
2. It stores n references to one inner list, so writing to one row writes to all. Use `[[0] * m for _ in range(n)]`.
3. It binds a second name to the same object. Copy with `a[:]`, `list(a)` or `copy.copy(a)` — and `copy.deepcopy(a)` for nested structures.
4. A shallow copy shares the inner objects, so mutating `a[0][0]` is visible through the copy; a deep copy recreates the whole graph.
5. −4 and 1 — Python floors toward −∞ and the remainder takes the divisor's sign. C++ and Java truncate toward zero, giving −3 and −1.
6. Strings are immutable, so each concatenation copies everything accumulated so far: 1+2+…+n. Collect into a list and `''.join(parts)`.
7. `pop(0)` O(n); `insert(0, x)` O(n); `append(x)` O(1) amortised; `x in a` O(n).
8. It **creates** the key with the default value. Use `d.get(k, default)` to read without mutating.
9. `None` — it sorts in place. `sorted(a)` returns a new list. `a = a.sort()` silently sets `a` to `None`.
10. The default object is created once at function-definition time and shared across all calls, so a mutable default accumulates state. Use `None` as the sentinel.
11. A closure captures the variable, not its value, so lambdas built in a loop all see the final value. Bind it as a default argument: `lambda i=i: i`.
12. 0 — Python uses banker's rounding (round-half-to-even), so 0.5 → 0, 1.5 → 2, 2.5 → 2.
13. Min-heap only. Negate on push and negate back on pop for a max-heap.
14. When two priorities tie, the comparison falls through to the second tuple element, and arbitrary objects define no ordering. Insert a unique counter as a tiebreaker.
15. 1000. `setrecursionlimit` raises the interpreter's guard but not the OS stack, so very deep recursion can still crash the process — go iterative beyond roughly 10⁵ frames.
16. Call `f.cache_clear()`, or results from a previous test case bleed into the next one.
17. Hashable: `int`, `str`, `tuple`, `frozenset`, and objects defining `__hash__`. Not hashable: `list`, `dict`, `set`, and any class that defines `__eq__` without `__hash__`.
18. An **operand**, not a boolean: `0 or []` returns `[]`, and `[] or 0` returns `0`.
19. A balanced BST — there is no `std::map` / `TreeMap` equivalent, so ordered O(log n) operations need a heap, `bisect` on a sorted list, or a reformulation.
20. `sys.stdin.readline` plus a single batched write; comprehensions and `map` instead of explicit loops; `set`/`dict` lookups instead of `in list`; `deque` instead of `list.pop(0)`; hoisting attribute lookups out of the innermost loop (and `lru_cache` instead of a hand-rolled memo dict).
