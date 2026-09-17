# Advanced Data Structures — Concepts

## 1. Core idea in 3 lines
These structures exist for one reason: a problem needs both **updates and range queries**, and neither a prefix-sum array (fast queries, slow updates) nor a plain array (fast updates, slow queries) is enough. Fenwick and segment trees give O(log n) for both. The design questions in this folder — LRU/LFU caches, O(1) random sets — test whether you can compose simple structures into one with better guarantees than any part alone.

---

## 2. Choosing the right structure

| Requirement | Structure | Query | Update |
|---|---|---|---|
| Range sum, **no** updates | Prefix sums | O(1) | — |
| Range update, one final read | Difference array | — | O(1) |
| Range sum + **point** update | **Fenwick (BIT)** | O(log n) | O(log n) |
| Any associative range op + point update | **Segment tree** | O(log n) | O(log n) |
| Range op + **range** update | Segment tree + **lazy propagation** | O(log n) | O(log n) |
| Range min/max, **static** array | Sparse table | **O(1)** | — |
| Dynamic connectivity | Union-Find | ~O(1) | ~O(1) |
| Ordered set with rank queries | Balanced BST / order-statistic tree | O(log n) | O(log n) |

**The decision tree:** are there updates? No → prefix sums or sparse table. Yes, point updates and sums only → Fenwick (shorter to write). Yes, and the operation is min/max/gcd, or updates are over ranges → segment tree.

---

## 3. Fenwick tree (Binary Indexed Tree)

An array where index `i` stores the aggregate of a block of length `i & -i` ending at `i`. Update walks up by `i += i & -i`; prefix query walks down by `i -= i & -i`. Both touch O(log n) entries.

**Why it works:** every index's binary representation decomposes any prefix into O(log n) disjoint blocks, one per set bit. The `i & -i` idiom (`15_Bit_Manipulation`) is exactly "the lowest set bit", i.e. this block's length.

It is **1-indexed by necessity** — index 0 has no lowest set bit, so the update loop would never terminate.

Range sum `[l, r]` = `prefix(r) − prefix(l−1)`. That subtraction is why Fenwick handles sums and any invertible operation, but **not** min or max — you cannot undo a min. For min/max, use a segment tree.

Fenwick is about 15 lines against a segment tree's 40, so when the operation is a sum, prefer it under time pressure.

**Counting applications:** Count of Smaller Numbers After Self, counting inversions, and "how many previously-seen values are less than x" — all done by compressing coordinates and using the BIT as a frequency counter.

---

## 4. Segment tree

A binary tree over array ranges: each node stores the aggregate of its range, leaves are single elements. Build O(n), query and point update O(log n), space O(4n) for the array representation (`2*node` and `2*node+1` for children).

Works for **any associative operation**: sum, min, max, gcd, XOR, "count of zeros", or a small struct combining several of these. That generality is why it survives where Fenwick cannot.

**Lazy propagation** handles range updates: instead of touching every leaf, mark a node with a pending update and push it down only when a query descends into it. This keeps range-update + range-query at O(log n). The discipline is that every node must be "pushed" before its children are read.

Variants worth recognising by name: iterative (bottom-up) segment trees, merge-sort trees, persistent segment trees. All P3.

---

## 5. Sparse table

Precompute `st[k][i]` = the aggregate of the range starting at `i` of length 2ᵏ, in O(n log n). A query on `[l, r]` takes `k = ⌊log₂(r−l+1)⌋` and combines the two overlapping blocks `st[k][l]` and `st[k][r−2ᵏ+1]` — **O(1)**.

The overlap is what makes it O(1), and it is why sparse tables only work for **idempotent** operations (min, max, gcd), where counting an element twice is harmless. Sums would be double-counted.

Static only: there is no update operation.

---

## 6. Design problems

These appear as standalone interview questions and test composition.

**LRU Cache** — hash map + doubly linked list + sentinels. Covered in `07_Linked_List`; know it cold.

**LFU Cache** — hash map key→node, hash map frequency→doubly linked list of nodes at that frequency, and a `minFreq` counter. On access, move the node from list `f` to list `f+1`; if list `minFreq` becomes empty, increment `minFreq`. Eviction takes the tail of list `minFreq`. All O(1).

**Insert Delete GetRandom O(1)** — a dynamic array for O(1) random indexing plus a hash map value→index. Deletion swaps the target with the last element, pops, and fixes the moved element's index. The swap-with-last trick is the whole idea.

**Design HashMap** — array of buckets with chaining, plus resize when the load factor is exceeded.

**Min Stack**, **Max Queue**, **Design Twitter** (merge k feeds with a heap), **Time-Based Key-Value Store** (binary search over per-key timestamp lists).

---

## 7. Recall questions

1. Give the decision tree for choosing between prefix sums, Fenwick, segment tree and sparse table.
2. What does Fenwick index `i` store, and what is `i & -i`?
3. Why must a Fenwick tree be 1-indexed?
4. Why can Fenwick not answer range minimum queries?
5. What operations does a segment tree support that Fenwick cannot?
6. What is lazy propagation and what problem does it solve?
7. Why is a sparse table O(1) per query, and why only for idempotent operations?
8. Describe the LFU cache structure.
9. How does Insert-Delete-GetRandom achieve O(1) deletion?
10. What is the space complexity of an array-based segment tree, and why 4n?
