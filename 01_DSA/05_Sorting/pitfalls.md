# Sorting — Pitfalls

## Comparators
- Returning `<=` from a C++ comparator. `std::sort` requires **strict** weak ordering; `<=` can read out of bounds and crash. This is a genuine segfault, not a wrong answer.
- An inconsistent comparator (not transitive, or not antisymmetric) — undefined behaviour.
- In Python, using `cmp` semantics directly; you must wrap with `functools.cmp_to_key`.
- Sorting tuples and forgetting that ties fall through to the next element, which may not be comparable.
- Sorting by a key that mutates during the sort.

## Stability
- Assuming `std::sort` is stable. It is not. Use `stable_sort`.
- Assuming Java's `Arrays.sort` is stable for primitives. It is not (dual-pivot quicksort); it is stable for objects (Timsort).
- Multi-key sorting in the wrong order: sort by the **least** significant key first when chaining stable sorts.
- Implementing merge with `<` instead of `<=` in the comparison, which silently destroys stability.

## Complexity claims
- Saying quicksort is O(n log n) worst case — it is O(n²).
- Saying "sorting is always O(n log n)" — counting and radix sorts are linear when applicable.
- Ignoring the key-comparison cost: sorting n strings of length L is O(n·L·log n).
- Claiming counting sort is always better — it is O(n + k), and k can be astronomically large.

## Implementation
- Merge sort: forgetting to append the remaining tail of either half.
- Quicksort: infinite recursion when the partition returns a boundary index and the recursion does not exclude the pivot.
- Quicksort with a fixed first-element pivot on sorted input → O(n²). Randomise.
- Counting sort: iterating the input forwards in the placement loop, which breaks stability. Iterate in reverse.
- Cyclic sort: comparing indices instead of values (`if a[i] != i+1` inside the swap loop), which loops forever on duplicates. Compare `a[i] != a[j]`.
- Recursion depth: a quicksort on 10⁵ adversarial elements can blow the stack.

## Problem-level
- Sorting when the problem requires the **original indices** — sort `(value, index)` pairs instead.
- Sorting an array that the caller still needs in its original order.
- Sorting intervals by start when the greedy needs end time (see `16_Intervals`).
- Sorting to answer a single query that a linear scan answers faster.
