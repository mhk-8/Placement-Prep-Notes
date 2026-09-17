# Advanced Data Structures — Pitfalls

## Fenwick tree
- **Using 0-based indexing.** `i & -i` is 0 for i = 0, so the update loop never advances and hangs. Always 1-index internally, converting at the boundary.
- Trying to answer range minimum with a BIT. Prefix subtraction requires an invertible operation; min is not invertible.
- Overflow: prefix sums over 10⁵ values of 10⁹ exceed 32 bits.
- Forgetting coordinate compression when values are large, then allocating an array of size 10⁹.
- Mixing up `update(i, delta)` (add) with `set(i, value)` (assign) — the BIT stores deltas, so assignment needs `update(i, new - old)`.

## Segment tree
- Allocating `2n` for the recursive form; it needs `4n`. The iterative form does use `2n`, but only for a power-of-two-padded or carefully indexed layout.
- Off-by-one between inclusive `[l, r]` and half-open `[l, r)` conventions. Choose one and write it in a comment.
- Forgetting the identity element for the operation (0 for sum, +inf for min, 0 for XOR) and returning garbage for empty ranges.
- Lazy propagation: reading a node's value before pushing its pending update down.
- Lazy propagation: pushing to children when the node is a leaf, writing out of bounds.

## Sparse table
- Using it for sums — the two query blocks overlap, so non-idempotent operations double-count.
- Trying to update it. It is static by construction.
- `k = (r - l + 1).bit_length() - 1` off by one, indexing a block longer than the range.

## Cache design
- LRU: evicting `tail` instead of `tail.prev`, or forgetting to delete the evicted key from the hash map.
- LFU: forgetting to reset `min_freq = 1` on every new insertion.
- LFU: not maintaining LRU order *within* a frequency bucket, so the tie-break is wrong.
- Both: not handling `capacity == 0`.

## RandomizedSet
- Deleting by shifting the array (O(n)) instead of swapping with the last element.
- Forgetting to update the moved element's index in the map.
- Removing the element that *is* the last element — the swap is a no-op, but the map update must still be ordered correctly or you delete the wrong key.

## General
- Reaching for a segment tree where a prefix-sum array or a difference array suffices. Check whether updates actually exist before writing 40 lines.
- Writing a segment tree when Fenwick answers the question in 15 lines.
- Claiming O(1) for an amortised or average bound without saying so.
