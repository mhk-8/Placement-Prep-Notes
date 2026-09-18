# C++ — Flashcards

## Questions

1. Give the three lines of the fast-I/O preamble and say what each does.
2. Why is `endl` a problem inside a loop?
3. `long long c = a * b;` with two `int`s — what actually happens?
4. What does `v.size()` return, and why does `v.size() - 1` sometimes explode?
5. What does `mp[k]` do when `k` is absent?
6. Why is the free `lower_bound` on a `set` O(n), and what should you use?
7. `substr(pos, len)` — what is the second argument?
8. What does `-7 / 2` and `-7 % 2` evaluate to, and how does Python differ?
9. Is `std::sort` stable? Is it O(n log n) worst case?
10. What is `nth_element` and when do you prefer it to `sort`?
11. `priority_queue<int>` — min or max heap? How do you get the other?
12. Why must a comparator return strict `<` and never `<=`?
13. What does `unique()` do, and what must precede it?
14. Give three ways to define a custom sort order.
15. Why is `vector<bool>` unusual?
16. When is a stack-allocated array zero-initialised and when not?
17. What is the precedence trap with `&`, `|` and `^`?
18. What must you write instead of `1 << 31`?
19. Why does `accumulate(all(a), 0)` sometimes give a wrong total?
20. Name three ways to accidentally get O(n) where you expected O(1) or O(log n).

---

## Answers

1. `ios_base::sync_with_stdio(false);` unties C++ streams from C's stdio; `cin.tie(nullptr);` stops `cin` flushing `cout` before each read; using `'\n'` instead of `endl` avoids a flush per line.
2. `endl` is `'\n'` plus an explicit flush, so a loop over 10⁵ lines performs 10⁵ flushes — enough to turn a correct solution into a TLE.
3. The product is computed in **`int`** and wraps before the widening assignment. Cast first: `(long long)a * b`.
4. An unsigned `size_t`. On an empty container `0 - 1` wraps to ~1.8 × 10¹⁹, so a loop bounded by `v.size() - 1` runs essentially forever. Cast to `(int)`.
5. It **inserts** a default-constructed value and returns a reference to it. Use `.count()` or `.find()` for membership tests.
6. `std::lower_bound` binary-searches only random-access iterators; a set's are bidirectional, so it walks linearly. Use the member `s.lower_bound(x)`.
7. A **length**, not an end index.
8. −3 and −1 — C++ truncates toward zero. Python gives −4 and 1, because it floors toward −∞.
9. Not stable (it is introsort), but it **is** O(n log n) worst case thanks to the heapsort fallback. Use `stable_sort` when stability matters.
10. Quickselect — it places the k-th element correctly with everything smaller before it, in **O(n) average**. Use it whenever only the k-th element or an unordered top-k is needed.
11. **Max**-heap by default. Min-heap: `priority_queue<int, vector<int>, greater<int>>`. (Java's default is the opposite.)
12. `std::sort` requires a strict weak ordering. `<=` makes two equal elements each "less than" the other, which can drive the algorithm past the end of the range and segfault.
13. It removes **consecutive** duplicates and returns the new logical end; the range must be sorted first, and you must follow with `a.erase(unique(all(a)), a.end())` to actually shrink it.
14. A lambda passed to `sort`; a member `operator<` on the struct; a free comparator function passed as the third argument.
15. It is a bit-packed specialisation, so it is not a real container of `bool` — `operator[]` returns a proxy rather than a reference, and per-element access is slow. Use `vector<char>`.
16. Globals and `static` locals are zero-initialised; automatic (stack) variables are **not**, and reading one before assignment is undefined behaviour.
17. `&`, `|` and `^` bind **looser** than `==`, so `x & 1 == 0` parses as `x & (1 == 0)`. Always parenthesise.
18. `1LL << 31` — shifting a signed 32-bit `int` into its sign bit is undefined behaviour.
19. The accumulator's type comes from the initial value, so `0` sums in `int` and overflows. Pass `0LL`.
20. The free `lower_bound` on a tree container; `v.erase(it)` or `v.insert` in the middle of a vector; passing a large container by value into a function called in a loop. (A fourth: an `unordered_map` degraded by an anti-hash test.)
