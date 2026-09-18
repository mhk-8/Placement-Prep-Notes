# Java — Gotchas

> Java's compiler catches type errors, not semantic ones. Everything here compiles cleanly and then misbehaves.

---

## Equality

- **`==` on objects compares references.** `s1 == s2` for strings is true only when they are the same object. Always `.equals()`.
- **`Integer` caching:** boxed values from **−128 to 127** are shared, so `==` is true in that range and false outside it. `Integer a = 127, b = 127;` → `a == b` is true; at 128 it is false. Never compare boxed numbers with `==`.
- String literals are interned, and so are compile-time constant expressions: `"ab" + "c"` is the same object as `"abc"`, but a runtime concatenation is not.
- `Arrays.equals(a, b)` compares contents; `a == b` compares references. For nested arrays use `Arrays.deepEquals`.
- Overriding `equals` without `hashCode` breaks every hash-based collection **silently** — the lookup hashes to a different bucket and never reaches the comparison.

## Numbers

- **`int` overflow is silent.** `100000 * 100000` wraps. Cast before multiplying: `(long) a * b`.
- A `long` literal past int range needs the `L` suffix: `1_000_000_000_000L`.
- Integer division truncates toward zero: `-7 / 2` is −3, and `-7 % 2` is −1. (Python gives −4 and 1.)
- `a - b` in a comparator overflows for large-magnitude values and returns the wrong sign. Use `Integer.compare`.
- `Math.abs(Integer.MIN_VALUE)` returns `Integer.MIN_VALUE` — it overflows rather than throwing.
- `0.1 + 0.2 != 0.3`; use `BigDecimal` for money and an epsilon for comparisons.
- `Integer.MAX_VALUE` as an infinity overflows on `INF + weight`. Use `Integer.MAX_VALUE / 2` or a `long`.

## Autoboxing

- Unboxing `null` throws a **NullPointerException**: `int x = map.get(missingKey);` where `get` returns `null`.
- The conditional operator applies **binary numeric promotion**, so `true ? Integer.valueOf(1) : Double.valueOf(2.0)` yields `1.0`, not `1`.
- Boxing in a hot loop allocates an object per operation — prefer `int[]` over `List<Integer>` when performance matters.
- `List<Integer>` cannot be `List<int>`; generics do not accept primitives.

## Collections

- **`list.remove(int)` removes by INDEX; `list.remove(Object)` removes by VALUE.** For a `List<Integer>`, `list.remove(1)` deletes position 1. Use `list.remove(Integer.valueOf(1))` for the value.
- `Arrays.asList(a)` returns a **fixed-size** view — `add` and `remove` throw `UnsupportedOperationException`. Wrap it: `new ArrayList<>(Arrays.asList(a))`.
- `List.of(...)` and `Map.of(...)` are **immutable**; any mutation throws.
- `Arrays.asList(intArray)` produces a `List<int[]>` of **one element**, not a list of ints. Use `Arrays.stream(a).boxed().collect(...)`.
- Modifying a collection while iterating it throws `ConcurrentModificationException`. Use `Iterator.remove()` or `removeIf`.
- `HashMap` iteration order is unspecified and can change between runs. Use `LinkedHashMap` for insertion order or `TreeMap` for sorted.
- `PriorityQueue` iteration is **not** in sorted order — only `poll()` is ordered.
- `PriorityQueue.remove(Object)` is O(n).
- `subList` returns a **view**; structural changes to the parent invalidate it.

## Strings

- **`s += c` in a loop is O(n²).** Use `StringBuilder`.
- `split` takes a **regex**: `split(".")` splits on every character; you want `split("\\.")`. Same for `|`, `+`, `*`, `(`, `)`.
- `substring(a, b)` has an **exclusive** end index — the opposite convention from C++'s `substr(pos, len)`.
- `s.charAt(i) - '0'` converts a digit; `s.charAt(i)` alone is a `char` code.
- `char + int` promotes to `int`: `System.out.println('a' + 1)` prints 98. Cast back with `(char)`.
- `"" + 'a' + 1` is `"a1"`, but `'a' + 1 + ""` is `"98"` — evaluation is left to right.
- `s.replace` returns a new string; strings are immutable, so ignoring the return value does nothing.

## Arrays

- `a.length` (field) for arrays, `s.length()` for strings, `c.size()` for collections.
- 2-D arrays can be jagged; `g[i].length` may differ per row.
- `Arrays.sort` on **primitives** is dual-pivot quicksort: not stable, and O(n²) in the adversarial worst case. On **objects** it is Timsort: stable and guaranteed O(n log n). Box to `Integer[]` if the worst case worries you.
- `Arrays.copyOfRange(a, from, to)` has an exclusive `to`.
- `Arrays.fill` on a 2-D array fills only the row references unless you loop.

## Miscellaneous

- A `return` inside `finally` **replaces** the pending return value and swallows pending exceptions. Never do it.
- Java is **pass by value** — but for objects the *reference* is copied, so mutating the referred object is visible to the caller while reassigning the parameter is not.
- `static` fields persist across test cases in a single run; reset them in `solve()`.
- `Scanner` is several times slower than `BufferedReader` and will TLE on large input.
- `System.out.println` flushes on every call; batch into a `StringBuilder`.
- Integer keys in a `HashMap` are boxed, so a large `HashMap<Integer,Integer>` costs far more memory than an `int[]`.

## The five that cost the most marks

1. `int` overflow on a product, silently
2. `list.remove(1)` removing the index rather than the value
3. `s += c` in a loop causing a TLE
4. `Scanner` instead of `BufferedReader` on large input
5. `==` instead of `.equals` on strings or boxed numbers
