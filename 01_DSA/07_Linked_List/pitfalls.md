# Linked Lists — Pitfalls

## Null dereferences
- `while fast.next and fast.next.next` vs `while fast and fast.next` — using the wrong one crashes on an empty or single-node list.
- Checking `fast.next` before `fast`. Order matters: the left operand of `and` must be the null check.
- Forgetting the empty-list case entirely (`head is None`).
- Accessing `.next` of a node just unlinked.

## Lost pointers
- Overwriting `cur.next` before saving it — the rest of the list becomes unreachable. In reversal, `nxt = cur.next` must come first.
- Forgetting to set the new tail's `next` to `None`, creating a cycle. Very common in reorder, partition and odd-even problems.
- In "reorder list", forgetting `slow.next = None` before reversing the second half — the two halves stay linked and you build a cycle.

## Head handling
- Not using a dummy head, then needing a special case for "the head itself is removed/changed" and getting it wrong.
- Returning `head` instead of `dummy.next` after the head was replaced.
- Reusing `dummy` as the moving pointer instead of a separate `tail`, so the return value is lost.

## Doubly linked lists
- Updating only one of the two pointers on unlink or insert — corruption that shows up much later.
- Omitting sentinel nodes and then null-checking `prev` and `next` everywhere; sentinels remove all of it.
- In LRU: evicting `tail` instead of `tail.prev`, or forgetting to delete the evicted key from the hash map (a slow memory leak and a correctness bug on re-insert).

## Complexity
- Merging k lists one at a time: O(N·k). Use a heap or pairwise merging for O(N log k).
- Accessing by index inside a loop — each access is O(n), making the loop O(n²).
- Recursive reversal on a 10⁵-node list: O(n) stack, likely overflow.

## Heaps of nodes
- Pushing `(val, node)` tuples in Python: when two `val`s tie, Python compares the `ListNode`s and raises `TypeError`. Insert a unique tie-breaker: `(val, index, node)`.

## Interview behaviour
- Coding before drawing. Sketch the pointers for a 3-node list and trace the first two iterations; almost every bug in this topic is visible on paper in 60 seconds.
