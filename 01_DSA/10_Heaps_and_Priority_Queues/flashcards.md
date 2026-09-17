# Heaps & Priority Queues — Flashcards

## Questions

1. State the min-heap invariant and what it does not guarantee.
2. Give the parent and children index formulas.
3. Why is bottom-up heap construction O(n) while n pushes are O(n log n)?
4. For the k largest elements, which heap type and what size, and why?
5. Compare sorting, a bounded heap and quickselect for "k-th largest".
6. What is the complexity of searching for an arbitrary value in a heap?
7. Describe the two-heap running-median structure and the rebalance rule.
8. What is lazy deletion and where does it appear?
9. How do you merge k sorted lists with a heap, and at what cost?
10. How do you build a max-heap in Python?
11. Why does a C++ min-heap comparator use `>`?
12. Why can a Python heap of `(priority, object)` tuples raise a TypeError?
13. When is a balanced BST preferable to a heap?
14. What is `heappushpop` and why use it?
15. What is the height of a heap with n elements?

---

## Answers

1. Every parent is ≤ both children. It guarantees nothing about siblings, nothing about ordering across subtrees, and the underlying array is not sorted — only the root is the global minimum.
2. Children of i: 2i+1 and 2i+2. Parent of i: (i−1)//2.
3. Sifting down from height h costs O(h), and only n/2^{h+1} nodes sit at height h; Σ h·n/2^{h+1} converges to 2n. Most nodes are near the leaves and barely move.
4. A **min**-heap of size k. The root is then the smallest of the current k candidates, so it is the one to evict when a larger element arrives — and it is the k-th largest overall at the end.
5. Sort O(n log n); bounded heap O(n log k) time and O(k) space, works on a stream; quickselect O(n) average but O(n²) worst and needs the array in memory.
6. O(n) — the heap property gives no guidance about where a non-extreme value lives.
7. A max-heap for the lower half and a min-heap for the upper half, with sizes differing by at most 1. Push to one, move its root across, then rebalance if the upper grew larger. The median is the larger heap's root, or the average of the two roots.
8. Leaving obsolete entries in the heap and skipping them at pop time (`if d > dist[u]: continue`), used when the heap cannot support arbitrary deletion — Dijkstra, sliding-window structures.
9. Push each list's head; repeatedly pop the minimum and push that list's next element. O(N log k) for N total elements.
10. Negate the keys on push and negate again on pop, since `heapq` is min-only.
11. `priority_queue`'s comparator answers "does a have lower priority than b"; returning `a > b` means larger values have lower priority, which puts the smallest at the top.
12. When two priorities are equal, the comparison falls through to the second tuple element; arbitrary objects define no ordering. Insert a unique counter as a tiebreaker.
13. When you need ordered iteration, predecessor/successor queries, range queries, or deletion of arbitrary elements in O(log n).
14. It pushes then pops in a single sift, which is faster than separate calls and keeps a bounded heap at exactly size k.
15. ⌊log₂ n⌋ — it is a complete binary tree.
