# Two Pointers & Sliding Window — Flashcards

## Questions

1. Why is the sliding-window template O(n) despite its inner while loop?
2. What is the loop-shape difference between longest-valid and shortest-valid windows?
3. In Container With Most Water, why is moving the taller wall never useful?
4. State the at-most-K trick and why "exactly K" cannot be windowed directly.
5. What does `res += right - left + 1` count?
6. How do you maintain the distinct-element count of a window in O(1) per operation?
7. Explain `have`/`need` (or `missing`) in minimum window substring.
8. Why does a sliding window fail for "subarray sum equals k" with negatives, and what replaces it?
9. State Floyd's cycle detection, and how the cycle entry point is found.
10. How do you find the n-th node from the end in a single pass?
11. In 3Sum, at which three points must duplicates be skipped?
12. What is the condition for "longest repeating character replacement" to be valid?
13. Why does the last-index jump in "longest substring without repeating characters" need a `>= left` guard?
14. When is `while i < j` wrong and `while i <= j` right?
15. Give the test for whether a sliding window is applicable at all.

---

## Answers

1. Each index is added once and removed at most once, so the combined pointer movement is at most 2n — an amortised argument, not a nested-loop one.
2. Longest: shrink while the window is **invalid**, record after. Shortest: shrink while it is **still valid**, record before each shrink.
3. Area is `min(h[i],h[j]) × width`. Moving the taller wall shrinks the width and cannot raise the capped height, so it can never improve the area.
4. `exactly(K) = atMost(K) − atMost(K−1)`. "At most K" is monotone under extension and shrinking, so it is windowable; "exactly K" is not.
5. Every valid subarray *ending* at index `right` — there are `right − left + 1` of them once the window is valid.
6. Keep a `Counter` and a separate `distinct` counter: increment when a count goes 0 → 1 and decrement when it goes 1 → 0 (or delete zero-count keys and use the map's size).
7. `need` is how many distinct characters of t still have unmet multiplicity; it decrements when a character's requirement is exactly met and increments when it is broken. The window is valid iff `need == 0` — an O(1) test.
8. Extending the window no longer moves the sum monotonically, so shrinking from the left is not safe. Use a running prefix sum with a hash map of previously seen prefixes.
9. Slow advances 1, fast advances 2; meeting implies a cycle. Reset one pointer to the head and advance both by 1 — they meet at the entry node, because the tail length and the remaining cycle distance are congruent mod the cycle length.
10. Advance `fast` by n, then move `fast` and `slow` together until `fast` hits the end; `slow` is at the n-th from the end. Use a dummy head so removing the actual head works.
11. At the anchor `i` (skip if `a[i] == a[i−1]`), and after each successful triplet when advancing `l` and when retreating `r`.
12. `window_length − max_frequency_in_window ≤ k`, i.e. the number of characters that must be replaced fits the budget.
13. A character's stored last index may predate the current window; without the guard, `left` would jump backwards and re-admit duplicates.
14. `i < j` is right for pair searches where an element must not pair with itself; `i <= j` is right for binary search and for partition scans where the middle element must still be examined.
15. Ask: "if extending makes the window invalid, could extending further ever make it valid again?" If yes, a window will not work.
