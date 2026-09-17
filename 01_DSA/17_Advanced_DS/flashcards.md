# Advanced Data Structures — Flashcards

## Questions

1. Give the decision tree for prefix sums vs Fenwick vs segment tree vs sparse table.
2. What does Fenwick index `i` store, and what does `i & -i` represent?
3. Why must a Fenwick tree be 1-indexed?
4. Why can a Fenwick tree not answer range minimum queries?
5. How do you get a range sum `[l, r]` from a Fenwick tree?
6. What does a segment tree support that Fenwick does not?
7. What is the space requirement of a recursive array-based segment tree, and why?
8. What is lazy propagation and what does it buy?
9. Why is a sparse table O(1) per query?
10. Why does a sparse table only work for idempotent operations?
11. Describe the LFU cache structure and the role of `minFreq`.
12. How does Insert-Delete-GetRandom achieve O(1) removal?
13. How do you count "smaller elements to the right" with a Fenwick tree?
14. What is coordinate compression and when do these structures need it?
15. When is a difference array sufficient instead of any of these?

---

## Answers

1. No updates and sums → prefix sums. No updates and min/max → sparse table. Point updates and sums → Fenwick. Any associative operation, or range updates → segment tree (with lazy propagation for range updates).
2. The aggregate of a block of length `i & -i` ending at index i. `i & -i` isolates the lowest set bit, which is that block's length.
3. `0 & -0` is 0, so the update loop `i += i & -i` would never advance and would loop forever.
4. Range queries are computed as `prefix(r) − prefix(l−1)`, which requires an invertible operation. Min cannot be undone by subtraction.
5. `prefix(r) − prefix(l−1)`, with both indices 1-based.
6. Non-invertible associative operations — min, max, gcd — and, with lazy propagation, range updates.
7. 4n. The recursion over arbitrary n is not a perfect binary tree, and 4n is the safe upper bound on node indices under the `2*node` / `2*node+1` layout.
8. Marking a node with a pending range update and applying it to children only when a query descends there, so range updates stay O(log n) instead of O(n).
9. It combines two precomputed blocks of length 2ᵏ that together cover the range, overlapping in the middle — a single constant-time combine.
10. Because the two blocks overlap, elements in the overlap are counted twice; only operations where that is harmless (min, max, gcd) are valid. Sums would be wrong.
11. A key→value map, a key→frequency map, and a frequency→ordered-list-of-keys map, plus `minFreq` tracking the smallest live frequency so eviction is O(1). On access the key moves from bucket f to f+1, and `minFreq` increments if bucket f empties.
12. It swaps the target with the last array element, pops the array, and updates the moved element's index in the hash map — avoiding the O(n) shift.
13. Compress the values to ranks, scan the array from right to left, query `prefix(rank(x) − 1)` for the count of already-seen smaller values, then insert x with `update(rank(x), 1)`.
14. Mapping the sorted distinct values to 0..m−1, needed whenever the value range is far larger than the number of distinct values, so the structure can be sized by m rather than by the raw range.
15. When all updates are range increments and the array is read only once at the end — O(1) per update plus one O(n) prefix-sum pass.
