# Linked Lists — Concepts

## 1. Core idea in 3 lines
A linked list trades O(1) random access for O(1) insertion and deletion given a pointer. Almost every linked-list problem is solved by one of four techniques: dummy head, two pointers, reversal, or a hash map of nodes. Low weight in OAs, high weight in interviews, because it tests pointer discipline rather than cleverness.

---

## 2. The trade-off against arrays

| | Array | Linked list |
|---|---|---|
| Access by index | O(1) | O(n) |
| Insert/delete at head | O(n) | **O(1)** |
| Insert/delete given a node | O(n) | **O(1)** (doubly) |
| Search | O(n) | O(n) |
| Memory | contiguous, cache-friendly | scattered, pointer overhead |
| Resize | amortised O(1) | no resize needed |

The cache argument matters in practice: a linear scan of an array is dramatically faster than a traversal of a linked list of the same length, even though both are O(n).

---

## 3. The four techniques

### Dummy head
Allocate a `dummy` node whose `next` points at the real head, build from `dummy`, and return `dummy.next`. This removes every special case for "the operation affects the head", which is where most linked-list bugs live. Use it for: merge, remove n-th, partition, remove duplicates, add two numbers.

### Two pointers
- **Fast/slow for the middle:** advance `fast` by 2 and `slow` by 1; when `fast` reaches the end, `slow` is at the middle. For even lengths, `while fast and fast.next` lands on the *second* middle; `while fast.next and fast.next.next` lands on the first.
- **Cycle detection (Floyd):** they meet iff a cycle exists. To find the entry point, reset one pointer to the head and advance both one step at a time — they meet at the entry. *Why:* let the tail be length `a`, the entry-to-meeting distance `b`, and the cycle length `c`. Slow has travelled `a+b`, fast `2(a+b)`, and the difference is a multiple of `c`, giving `a ≡ −b (mod c)` — so `a` steps from the head and `a` steps from the meeting point arrive together.
- **N-th from the end:** advance `fast` by n, then move both until `fast` hits the end. With a dummy head, removing the actual head needs no special case.

### Reversal
The three-pointer iterative reversal is the single most-asked linked-list routine:
```
prev = None
while cur: nxt = cur.next; cur.next = prev; prev = cur; cur = nxt
return prev
```
Save `next` **before** overwriting it — forgetting that loses the rest of the list. The recursive version is elegant but O(n) stack.

Reversal composes: "reorder list" = find middle + reverse second half + interleave. "Palindrome list in O(1) space" = find middle + reverse second half + compare. "Reverse in k-groups" = reverse a bounded segment, repeatedly.

### Hash map of nodes
When you need to associate old nodes with new ones (Copy List with Random Pointer) or detect a cycle without Floyd. O(n) space. The O(1)-space version of the random-pointer copy interleaves copies into the original list, then separates them — a good follow-up to know.

---

## 4. LRU Cache — the one to know cold

Requirements: `get` and `put` both O(1), with eviction of the least recently used entry when capacity is exceeded.

**Structure:** a hash map from key → node, plus a **doubly** linked list ordered by recency, with dummy head and tail sentinels.
- `get(key)`: look up the node in the map, unlink it, re-insert it at the front (most recent), return its value.
- `put(key, value)`: if present, update and move to front. Otherwise insert at front and, if over capacity, remove the node before the tail sentinel and delete its key from the map.

The map gives O(1) location; the doubly linked list gives O(1) unlink (you need the `prev` pointer, which is why it must be doubly linked); the sentinels remove all the null checks.

**LFU Cache** is the harder follow-up: a map from frequency → doubly linked list, plus a `minFreq` counter.

---

## 5. Merge patterns

- **Merge two sorted lists:** dummy head, advance whichever head is smaller, then attach the non-empty remainder.
- **Merge k sorted lists:** a min-heap of the k current heads gives O(N log k), where N is the total node count. The alternative — pairwise merging in a tournament — is also O(N log k) and uses O(1) extra space. Sequentially merging one at a time is O(N·k) and is the wrong answer.
- **Sort a linked list:** merge sort is the natural choice (no random access needed, O(1) extra space on lists), giving O(n log n).

---

## 6. Recall questions

1. Why does a dummy head simplify head-affecting operations?
2. State Floyd's cycle detection, and prove why resetting to the head finds the entry.
3. What is the difference between the two `while` conditions for finding the middle?
4. Write the three-pointer reversal and say which line loses the list if omitted.
5. How do you check whether a list is a palindrome in O(1) space?
6. Describe the LRU cache structure and why the list must be doubly linked.
7. What is the complexity of merging k sorted lists with a heap, and what is the naive alternative?
8. How do you copy a list with random pointers in O(1) extra space?
9. Why is array traversal faster than list traversal despite both being O(n)?
10. How do you remove the n-th node from the end in one pass?
