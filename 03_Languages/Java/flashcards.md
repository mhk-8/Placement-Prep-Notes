# Java — Flashcards

## Questions

1. Why is `Scanner` unsuitable for an OA, and what replaces it?
2. Why batch output into a `StringBuilder`?
3. What does `list.remove(1)` do on a `List<Integer>`, and how do you remove by value?
4. What is Java's `Integer` cache range, and what bug does it cause?
5. Why does `int x = 100000 * 100000;` not hold 10¹⁰, and what is the fix?
6. When is `s1 == s2` true for strings?
7. What is `true ? Integer.valueOf(1) : Double.valueOf(2.0)` and why?
8. What does `'a' + 1` print, and what does `"" + 'a' + 1` print?
9. Is `PriorityQueue` a min- or max-heap? What about C++?
10. Why is `s += c` in a loop O(n²), and why is `a + b + c` fine?
11. Is `Arrays.sort` stable for primitives? For objects? What is the worst case of each?
12. Is Java pass by value or pass by reference?
13. What happens on `int x = map.get(missingKey)`?
14. What does `Arrays.asList(...)` return, and what throws on it?
15. What does `"a.b.c".split(".")` return, and why?
16. Does a `return` in `finally` change the returned value? Does an assignment to a local?
17. State the equals/hashCode contract and the symptom of breaking it.
18. Why is `a - b` a bad comparator body?
19. What does `TreeMap` give you that Python's standard library cannot?
20. Give three ways Java collections silently cost more than the primitive equivalent.

---

## Answers

1. It parses with regular expressions and synchronises per call, which is several times slower and can TLE on 10⁶ tokens. Use `BufferedReader` with `StringTokenizer`.
2. `System.out.println` flushes on every call; appending to a `StringBuilder` and printing once turns thousands of flushes into one.
3. It calls `remove(int index)` — Java prefers the exact primitive overload — deleting position 1. Use `list.remove(Integer.valueOf(1))` for the value.
4. −128 to 127. Boxed values in that range are shared objects, so `==` is true there and false outside, which makes comparisons written with `==` pass in testing and fail in production.
5. Both operands are `int`, so the product is computed in 32 bits and wraps before the assignment. Write `100000L * 100000`.
6. Only when they are the same object — which happens for string literals and compile-time constant expressions (both interned), and not for `new String(...)` or runtime concatenation. Always use `.equals`.
7. `1.0`. The conditional operator applies binary numeric promotion, so the `Integer` and `Double` branches unify to `double` and the chosen 1 is widened.
8. `'a' + 1` prints **98** (char promoted to int). `"" + 'a' + 1` prints **a1** (left-to-right: the empty string forces concatenation first).
9. Java's `PriorityQueue` is a **min**-heap; C++'s `priority_queue` is a **max**-heap. In Java use `Collections.reverseOrder()` for a max-heap.
10. Strings are immutable, so each `+=` builds a new `StringBuilder`, copies everything so far and calls `toString()` — O(n²) overall. A single `a + b + c` expression is fused by the compiler into one `StringBuilder`, so it is O(n).
11. Primitives: dual-pivot quicksort — **not** stable, O(n²) adversarial worst case. Objects: Timsort — stable, guaranteed O(n log n).
12. **Pass by value** — but for objects the value passed is the reference, so mutating the object is visible to the caller while reassigning the parameter is not.
13. A `NullPointerException`, because `get` returns `null` and unboxing it to a primitive calls `intValue()` on null. Use `getOrDefault`.
14. A **fixed-size view backed by the array**: `set` works, but `add` and `remove` throw `UnsupportedOperationException`. Wrap with `new ArrayList<>(...)`.
15. An empty array — `split` takes a **regex**, and `.` matches every character, so all fields are empty and trailing empties are discarded. Use `split("\\.")`.
16. A `return` in `finally` **does** replace the pending value (and swallows pending exceptions). Merely assigning to a local does **not**, because the return value was already copied.
17. Equal objects must have equal hash codes. Violating it makes hash-based lookups fail silently for objects that compare equal, because they land in different buckets.
18. It overflows for large-magnitude values and returns the wrong sign, corrupting the sort. Use `Integer.compare(a, b)`.
19. An ordered map with O(log n) `floorKey`, `ceilingKey`, `higherKey`, `lowerKey` and `subMap` — Python has no balanced BST in its standard library.
20. Autoboxing allocates an `Integer` per operation in hot loops; `ArrayList<Integer>` stores pointers rather than contiguous values, costing cache locality; and `HashMap<Integer,Integer>` boxes both key and value, using far more memory than an `int[]`.
