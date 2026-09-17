# Binary Search — Pitfalls

## Infinite loops
- **`lo = mid` with `mid` rounding down.** If `lo = mid` is ever an update, `mid` must round **up**: `mid = lo + (hi - lo + 1) // 2`. Otherwise with `hi = lo + 1`, `mid == lo` and nothing moves. This is the classic hang in "maximise the minimum" problems.
- Mixing `lo < hi` with `hi = mid - 1`, or `lo <= hi` with `hi = mid` — each combination is consistent only with its partner.
- Forgetting to move a pointer at all in some branch.

## Off-by-one
- `hi = len(a)` vs `hi = len(a) - 1`. For lower/upper bound use `hi = len(a)` (an insertion position of n is legitimate); for exact search use `len(a) - 1`.
- Returning `lo` without checking it is in range and actually equals the target.
- `last_occurrence = upper_bound(x) - 1` — forgetting the `- 1`.
- Using `bisect_left` where `bisect_right` is needed (LIS with non-strict increase, counting duplicates).

## Overflow
- `mid = (lo + hi) / 2` overflows in C++/Java when both are near 2³¹. Always `lo + (hi - lo) / 2`.
- In "search on the answer", `hi = sum(a)` can exceed 32 bits.
- `mid * mid <= x` in Sqrt overflows; compare with `mid <= x / mid` or use 64-bit.

## Binary search on the answer
- Getting the **direction** of the predicate backwards; write out the F...FT...T pattern before coding.
- A wrong lower bound: for "ship packages", `lo` must be `max(w)`, not 1 — a capacity below the largest single package is infeasible for any number of days.
- A `feasible` check that is not actually monotone. Verify: if x works, does x+1 always work?
- Off-by-one in ceiling division: use `(p + s - 1) // s`, not `p // s + 1`.

## Rotated arrays
- Comparing `a[mid]` against `a[lo]` when finding the minimum — fails on a non-rotated array. Compare against `a[hi]`.
- Using non-strict `<=` where strict `<` is needed in the "which half is sorted" test, when duplicates exist.
- Claiming O(log n) for the duplicates variant; it is O(n) worst case.

## Reals
- `while lo < hi` on floats — may never terminate. Use a fixed iteration count.
- Comparing floats with `==`.

## Applicability
- Binary searching unsorted data with no monotone predicate.
- Sorting an array (O(n log n)) purely to answer **one** query that a linear scan answers in O(n).
- Forgetting that the array must stay sorted if elements are inserted between queries.
