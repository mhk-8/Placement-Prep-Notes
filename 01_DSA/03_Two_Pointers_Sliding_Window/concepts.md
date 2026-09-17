# Two Pointers & Sliding Window — Concepts

## 1. Core idea in 3 lines
Both patterns replace a nested double loop with two indices that only ever move forward, giving O(n) instead of O(n²). Two pointers converge from the ends of **sorted** data; a sliding window maintains a **contiguous** span satisfying a constraint. The precondition for both is monotonicity: moving a pointer must change the answer predictably in one direction.

---

## 2. Two pointers

### Converging (opposite ends)
Requires sorted input, or a quantity that behaves monotonically from the ends.

**Two Sum II:** `i` at the start, `j` at the end. If `a[i] + a[j] < target`, the only way to increase the sum is `i += 1`; if it is greater, `j -= 1`. Every step eliminates one candidate permanently, so the scan is O(n).

**Container With Most Water:** area = `min(h[i], h[j]) * (j − i)`. Moving the *taller* wall inwards can never help, because the width shrinks and the height is capped by the shorter wall. So always move the shorter one. That argument — "moving the other pointer is provably never better" — is what you must be able to state.

**3Sum:** sort, fix `a[i]`, two-pointer the remainder for `−a[i]`. O(n²). Skip duplicates at both the fixed index and after each pointer move, or you emit repeated triplets.

**Trapping Rain Water:** maintain `leftMax` and `rightMax`; whichever side is smaller is the binding constraint, so water above that bar is determined and the pointer can advance. O(n) time, O(1) space.

### Same-direction (fast/slow)
- **Cycle detection (Floyd):** slow moves 1, fast moves 2. If they meet, a cycle exists. Reset one to the head and advance both by 1; they meet at the cycle entrance. (Proof: if the tail is `a` and the meeting point is `b` into a cycle of length `c`, then `a ≡ −b (mod c)`.)
- **Middle of a list:** when fast reaches the end, slow is at the middle.
- **Nth from the end:** advance fast by n, then move both.
- **Read/write pointers:** in-place removal and de-duplication (`01_Arrays_and_Strings`).

---

## 3. Sliding window

### The single template
```
for right in range(n):
    add a[right] to the window
    while window is invalid:
        remove a[left]; left += 1
    record the answer
```
Each index enters and leaves at most once, so the total work is O(n) even though there is a nested `while`. Say this out loud in an interview — the amortised argument is the part being assessed.

### Fixed size k
No `while` needed: add `a[right]`, and once the window exceeds k, remove `a[right − k]`. Update the answer from index `k−1` onwards.

### Variable size — two flavours

| Goal | Loop shape |
|---|---|
| **Longest** valid window | Shrink only while *invalid*; record after the shrink |
| **Shortest** valid window | Shrink *while still valid*, recording before each shrink |

Getting these two backwards is the classic sliding-window bug. "Longest without repeating characters" shrinks while invalid; "minimum window substring" shrinks while valid.

### The at-most-K trick
"Exactly K distinct" is not directly windowable, because the validity predicate is not monotone. But "at most K distinct" is. So:
**exactly(K) = atMost(K) − atMost(K−1).**
The same trick converts "exactly K odd numbers", "exactly K zeros", and similar.

### Window state
The window is summarised by a small piece of state you can update in O(1) on both add and remove:
- a running sum or product
- a `Counter` plus a `distinct` count (increment `distinct` when a count goes 0 → 1, decrement when it goes 1 → 0)
- a `need`/`have` pair for "window contains all characters of t" — `have` counts how many *distinct required characters* have reached their required multiplicity, so validity is the O(1) test `have == need`

That O(1) update requirement is what makes the pattern linear. If updating the state after a removal costs O(k), you have an O(nk) algorithm, not O(n).

---

## 4. When the window does **not** apply

Sliding window requires that shrinking from the left is *safe* — that a discarded element can never be needed again. This fails when:
- values can be **negative** and the constraint is on a sum ("subarray sum equals k" → prefix + hash map instead)
- the subsequence need not be contiguous (→ DP)
- the constraint is non-monotone (→ at-most trick, or DP)

Test: "if I extend the window and it becomes invalid, can extending further ever make it valid again?" If yes, a window will not work.

---

## 5. Choosing between them

| Signal | Pattern |
|---|---|
| Sorted array, find a pair/triplet with a target | Converging two pointers |
| "Contiguous subarray/substring" + a constraint | Sliding window |
| "Longest/shortest such that…" | Variable window |
| "Every window of size k" | Fixed window |
| "Exactly K …" | atMost(K) − atMost(K−1) |
| Linked list: cycle, middle, n-th from end | Fast/slow pointers |
| Remove/de-duplicate in place | Read/write pointers |
| Contiguous + negatives + exact sum | **Not** a window — prefix + hash map |

---

## 6. Recall questions

1. Why is a sliding window O(n) despite containing a nested while loop?
2. In Container With Most Water, why do you always move the shorter wall?
3. What is the difference in loop structure between finding the longest and the shortest valid window?
4. State the at-most-K trick and why it is needed.
5. How do you maintain a distinct-count in O(1) per add and remove?
6. What is `have`/`need` in minimum window substring?
7. Why does a sliding window fail on arrays with negative values?
8. State Floyd's cycle detection and how to find the cycle's entry point.
9. How do you find the n-th node from the end in one pass?
10. Why must duplicates be skipped in 3Sum at three separate places?
