# Stacks & Queues — Flashcards

## Questions

1. Why is a monotonic stack O(n) despite an inner while loop?
2. Which stack order and scan direction give "next greater element to the right"?
3. How do you get "previous smaller element" with the same machinery?
4. Why store indices rather than values on a monotonic stack?
5. In Largest Rectangle in Histogram, what is the width of the rectangle when a bar is popped?
6. Why append a sentinel height of 0?
7. How is Maximal Rectangle reduced to Largest Rectangle?
8. Describe the monotonic deque for sliding-window maximum.
9. Why can an element smaller than the incoming one be discarded from the deque's back?
10. Compare deque, heap and multiset for sliding-window maximum.
11. How do you implement a queue with two stacks, and what is the amortised cost per operation?
12. Why must a min-stack push a new minimum on `<=` rather than `<`?
13. In RPN evaluation, which of the two pops is the left operand?
14. How do you handle next-greater in a circular array?
15. Why is `list.pop(0)` a bug in a Python BFS?

---

## Answers

1. Each index is pushed exactly once and popped at most once, so total pushes plus pops is ≤ 2n — an amortised argument, not a per-iteration one.
2. A stack kept **decreasing** from bottom to top, scanned left to right, popping while the top's value is less than the current value.
3. Keep an **increasing** stack; after popping everything ≥ the current value, whatever remains on top is the previous smaller element.
4. Because the answers usually involve distances or widths (`i − j`, `i − st[-1] − 1`), and the value is recoverable from the index anyway.
5. `i − st[-1] − 1` computed **after** the pop, or `i` if the stack became empty.
6. It is smaller than every real bar, so it forces every remaining bar to be popped and evaluated at the end of the scan.
7. Accumulate per-column consecutive-ones heights row by row, and run the histogram algorithm on each row's height array.
8. Keep a deque of indices whose values are decreasing: pop from the back while the back's value ≤ the new value, push the new index, pop the front if it has left the window, and read the front as the window maximum.
9. It is both smaller and older than the incoming element, so any future window containing it also contains the larger, longer-lived one.
10. Deque: O(n) time, O(k) space, the intended solution. Heap: O(n log k) and needs lazy deletion of stale entries. Multiset: O(n log k), simpler but slower.
11. An `in` stack for pushes and an `out` stack for pops; when `out` is empty, drain `in` into it. Each element moves at most once between them, so it is amortised O(1).
12. With strict `<`, a duplicate of the current minimum is not recorded, so popping the first copy loses the minimum while an equal value is still present.
13. The **second** pop. The first pop is the right operand.
14. Iterate `2n` times using `a[i % n]`, but push indices only during the first pass so each position is answered once.
15. `pop(0)` on a Python list is O(n) because every remaining element shifts, turning an O(V+E) BFS into O(V²). Use `collections.deque.popleft()`.
