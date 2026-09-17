# Linked Lists — Flashcards

## Questions

1. Why does a dummy head remove special cases?
2. Write the iterative reversal, and say which statement loses the list if omitted.
3. What is the difference between `while fast and fast.next` and `while fast.next and fast.next.next`?
4. State Floyd's cycle detection and how the entry node is found.
5. Sketch the proof that resetting to the head finds the cycle entry.
6. How do you remove the n-th node from the end in a single pass?
7. How do you test whether a list is a palindrome in O(1) extra space?
8. Describe the LRU cache data structures and the role of each.
9. Why must the LRU list be doubly linked?
10. What is the complexity of merging k sorted lists with a heap? With sequential merging?
11. Why does a Python heap of `(val, node)` tuples raise a TypeError?
12. How do you copy a list with random pointers in O(1) extra space?
13. Why is array traversal faster than list traversal despite equal asymptotics?
14. Which sort is natural for a linked list, and why?
15. What is the most common way a reorder/partition solution creates an accidental cycle?

---

## Answers

1. Every operation that could change the head becomes uniform: you always modify `prev.next`, never the head variable itself, and return `dummy.next`.
2. `prev=None; while cur: nxt=cur.next; cur.next=prev; prev=cur; cur=nxt`. Omitting `nxt = cur.next` loses everything after `cur`.
3. The first lands `slow` on the second middle for even lengths; the second lands it on the first middle. Choose based on which half you want to detach.
4. Slow advances 1, fast 2; meeting implies a cycle. Reset one pointer to the head, then advance both one step at a time until they meet — that is the entry.
5. With tail length `a`, entry-to-meeting `b` and cycle length `c`: slow travels `a+b`, fast `2(a+b)`, so `a+b ≡ 0 (mod c)`, giving `a ≡ −b (mod c)`. Hence `a` steps from the head and `a` steps onward from the meeting point coincide at the entry.
6. Advance `fast` n steps from a dummy head, then advance `fast` and `slow` together until `fast.next` is null; `slow.next` is the node to unlink.
7. Find the middle with fast/slow, reverse the second half, compare the halves element by element.
8. A hash map key → node for O(1) lookup, and a doubly linked list ordered by recency with head and tail sentinels for O(1) unlink and push-front.
9. Unlinking an arbitrary node in O(1) requires its `prev` pointer; a singly linked list would need an O(n) scan to find it.
10. Heap: O(N log k) for N total nodes. Sequential one-at-a-time merging: O(N·k).
11. When two values tie, the tuple comparison falls through to the `ListNode` objects, which define no ordering. Add a unique integer tie-breaker.
12. Interleave each copy after its original, wire `copy.random = original.random.next`, then separate the two interleaved lists.
13. Array elements are contiguous, so a traversal benefits from cache-line prefetching; list nodes are scattered, causing a cache miss per node.
14. Merge sort — it needs no random access and can be done with O(1) extra space on a list, giving guaranteed O(n log n).
15. Failing to null-terminate the detached half (e.g. missing `slow.next = None`), so the halves remain linked while also being re-attached.
