# Binary Search — Flashcards

## Questions

1. What is the real precondition for binary search?
2. Write Form B and state its invariant.
3. Why can Form B never infinite-loop?
4. When must `mid` round up instead of down?
5. Define `lower_bound` and `upper_bound`; give the count-of-x formula.
6. What should `hi` be initialised to for lower_bound, and why?
7. List the three steps of binary search on the answer.
8. Give four phrases that signal binary search on the answer.
9. For "Capacity to Ship Packages", what are `lo` and `hi`, and why is `lo` not 1?
10. In Find Minimum in Rotated Sorted Array, why compare `a[mid]` to `a[hi]`?
11. Why does Find Peak Element work without any sortedness?
12. Why is rotated search with duplicates O(n) in the worst case?
13. How do you binary search over real numbers?
14. How do you binary search a row-major sorted 2-D matrix?
15. Why is `mid = (lo + hi) / 2` a bug in C++ but not in Python?

---

## Answers

1. A monotone predicate over the search space — false up to a point, true thereafter. Sortedness is just the common special case.
2. `while lo < hi: mid = lo + (hi−lo)//2; if pred(mid): hi = mid else: lo = mid+1; return lo`. Invariant: the answer always lies within `[lo, hi]`.
3. Because `lo < hi` implies `mid < hi`, so `hi = mid` strictly shrinks the range, and `lo = mid + 1` obviously does.
4. When one branch does `lo = mid` (the "maximise the feasible value" shape). Use `mid = lo + (hi − lo + 1)//2`.
5. `lower_bound(x)` = first index with `a[i] ≥ x`; `upper_bound(x)` = first with `a[i] > x`. Count of x = `upper_bound(x) − lower_bound(x)`.
6. `len(a)` — the insertion position may legitimately be past the end.
7. Identify the answer range `[lo, hi]`; write a monotone `feasible(x)` check (usually a greedy linear pass); binary search the boundary with Form B.
8. "minimum possible maximum", "maximum possible minimum", "smallest X such that…", "split into k parts minimising the largest".
9. `lo = max(w)`, `hi = sum(w)`. A capacity smaller than the heaviest package can never ship it, no matter how many days.
10. Because a non-rotated array has `a[mid] > a[lo]` for most mid, which would wrongly suggest a rotation. Comparing to `a[hi]` is correct in both the rotated and non-rotated cases.
11. If `a[mid] < a[mid+1]` the sequence is rising, so a peak must exist to the right (it either keeps rising to the boundary, which counts, or turns). That gives a monotone predicate on the slope.
12. When `a[lo] == a[mid] == a[hi]`, neither half can be ruled out, so you must shrink by one and in the worst case examine every element.
13. Run a fixed number of iterations (~100) or until `hi − lo < 1e−9`; never test equality.
14. Treat it as a flat array of length R·C and map `mid` to `(mid // C, mid % C)`.
15. C++ `int` overflows at ~2.1×10⁹ when `lo + hi` exceeds it; Python integers are arbitrary precision. Write `lo + (hi − lo)//2` regardless, for portability of habit.
