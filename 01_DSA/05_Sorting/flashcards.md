# Sorting — Flashcards

## Questions

1. Give best/average/worst, space and stability for merge, quick and heap sort.
2. Why is quicksort O(n²) in the worst case, and what are two mitigations?
3. State the comparison-sort lower bound and its proof in one sentence.
4. Why can counting sort beat it?
5. Why must radix sort's inner sort be stable, and which digit is processed first?
6. Which library sorts are stable in C++, Java and Python?
7. What is Timsort and why is it O(n) on already-sorted input?
8. What is a stable sort, and give a case where stability changes the answer.
9. Describe quickselect and its average and worst complexity.
10. How does merge sort count inversions?
11. What is cyclic sort and when is it applicable?
12. What is a strict weak ordering, and what happens if a C++ comparator violates it?
13. When do you use a heap instead of sorting?
14. Which sort has guaranteed O(n log n) time and O(1) space?
15. How do you sort by two keys, one ascending and one descending?

---

## Answers

1. Merge: O(n log n) all cases, O(n) space, stable, not in-place. Quick: O(n log n) best/avg, O(n²) worst, O(log n) stack, not stable, in-place. Heap: O(n log n) all cases, O(1) space, not stable, in-place.
2. Consistently extreme pivots (e.g. first element on sorted input) give partitions of size n−1. Mitigate with a randomised pivot, median-of-three, or introsort's depth-triggered switch to heapsort.
3. Ω(n log n): a comparison sort is a decision tree with n! leaves, so its height is ≥ log₂(n!) = Θ(n log n).
4. It does not compare elements; it indexes by key value, which is outside the decision-tree model.
5. Because earlier (less significant) digit orderings must be preserved; LSD-first with a stable inner sort makes the final pass correct.
6. C++: `sort` not stable, `stable_sort` stable. Java: primitives not stable, objects (Timsort) stable. Python: stable.
7. A merge sort that finds existing ascending/descending runs and merges them, with insertion sort for small runs. Already-sorted input is one run, so it is a single O(n) pass.
8. Equal elements retain input order. Sorting employees by salary then stably by department preserves the salary order within each department; an unstable sort would scramble it.
9. Partition as in quicksort but recurse only into the side containing index k. Average O(n) (n + n/2 + n/4 + … = 2n), worst O(n²) without a randomised pivot.
10. During the merge, when an element of the right half is emitted, every remaining element of the left half is greater than it — add `len(L) − i` to the count.
11. Repeatedly swap each value to the index it belongs at; applicable when values form a permutation of 1..n. O(n) time, O(1) space.
12. An ordering that is irreflexive, antisymmetric and transitive — i.e. a genuine "less than". A comparator returning `<=` breaks it and can make `std::sort` read out of bounds and crash.
13. When only the top k (or a streaming k-th) is needed: O(n log k) beats O(n log n) and uses O(k) space.
14. Heapsort.
15. `key=lambda x: (x.a, -x.b)` in Python (negate the numeric descending key), or a comparator that compares `a` then reverses the `b` comparison.
