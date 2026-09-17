# Two Pointers & Sliding Window — Pitfalls

## Window structure
- **Shrinking in the wrong direction.** Longest → shrink while *invalid*. Shortest → shrink while *valid*. Swapping these is the most common bug in the pattern.
- Recording the answer at the wrong moment: for longest, after the shrink; for shortest, before each shrink.
- Forgetting `left` never decreases — if your logic needs it to move backwards, a window is the wrong pattern.
- Using `while` where `if` is correct (or vice versa) in the fixed-size case.

## Window state
- Not removing keys whose count hits zero, so `len(cnt)` overstates the distinct count.
- Updating the state on add but forgetting the symmetric update on remove.
- Maintaining state that cannot be updated in O(1) on removal (e.g. recomputing a max by scanning), which quietly makes the solution O(nk).
- In "longest substring without repeats", setting `left = last[ch] + 1` **without** the `last[ch] >= left` guard — a stale index drags `left` backwards.

## Two pointers
- Applying converging two pointers to **unsorted** input.
- 3Sum: skipping duplicates in only one or two of the three required places (the anchor, after moving `l`, after moving `r`).
- `while i < j` vs `while i <= j` — using `<=` in a pair search lets an element pair with itself.
- Container With Most Water: moving the taller wall, or moving both.
- Off-by-one in `right - left + 1` for the window length.

## Applicability
- Using a window on an array with **negative** values and an exact-sum target. Extension is no longer monotone; use prefix sums + a hash map.
- Using a window for a *subsequence* (non-contiguous) problem — that is DP.
- Trying to window "exactly K" directly instead of using the at-most difference.

## Linked-list pointers
- `while fast and fast.next` — omitting either check dereferences null on even-length lists.
- Advancing `fast` by two in one statement without checking `fast.next` first.
- For "n-th from the end", forgetting the dummy head, which breaks the case where the head itself is removed.

## General
- Returning `inf` instead of 0 when no valid window exists.
- Integer overflow on running sums/products.
- Mutating the input array with `sort()` when the caller needs the original order (3Sum returns values, so it is fine; index-returning problems are not).
