# Binary Search — Concepts

## 1. Core idea in 3 lines
Binary search does not require a sorted array; it requires a **monotone predicate** — a function over the search space that is false up to some point and true thereafter. Half the "hard" OA problems are binary search on the *answer* rather than on the input. Getting the boundary conditions right is 90% of the difficulty, which is why you memorise one template instead of re-deriving.

---

## 2. The precondition

Binary search is valid iff there exists a predicate P over the search space such that

```
P(x) = False, False, ..., False, True, True, ..., True
```

You are then finding the boundary. A sorted array is just the special case `P(i) = (a[i] >= target)`.

**Always state the predicate explicitly before coding.** "P(capacity) = can we ship everything within D days with this capacity" is the whole solution; the loop is boilerplate.

---

## 3. The three forms

### Form A — exact match
Classic `lo <= hi`, returning the index or −1. Fine, but it is the form that produces the most off-by-one bugs, because the loop can terminate with `lo > hi` in several ways.

### Form B — first index where the predicate is true (`lower_bound`)
```
while lo < hi:
    mid = lo + (hi - lo) // 2
    if P(mid): hi = mid          # mid might be the answer — keep it
    else:      lo = mid + 1      # mid is definitely not — discard it
return lo
```
**Make this your default.** The invariant is "the answer lies in [lo, hi]", the range strictly shrinks every iteration (because `mid < hi` when `lo < hi`), and it terminates with `lo == hi`. There is no off-by-one to get wrong.

`lower_bound(x)` = first index with `a[i] >= x`. `upper_bound(x)` = first index with `a[i] > x`. Then:
- count of x = `upper_bound(x) − lower_bound(x)`
- first occurrence = `lower_bound(x)` (check it is in range and equals x)
- last occurrence = `upper_bound(x) − 1`
- insertion position = `lower_bound(x)`

### Form C — binary search on the answer
The search space is the range of possible *answers*, not array indices. Structure:
1. Identify the answer's range `[lo, hi]`.
2. Write `feasible(x)` — a linear check, usually greedy.
3. Binary search for the boundary with Form B.

Total cost: O(n log(range)).

**Trigger phrases:** "minimum possible maximum", "maximum possible minimum", "minimise the largest…", "the smallest capacity/speed/time such that…", "split into k parts minimising…", "k-th smallest in a matrix/multiplication table".

Canonical examples: Koko Eating Bananas (P = "can finish in h hours at speed s"), Capacity to Ship Packages (P = "capacity c suffices in D days"), Split Array Largest Sum (P = "can split into ≤ m parts each ≤ s"), Minimum Days to Make Bouquets, Aggressive Cows.

---

## 4. Rotated and unsorted variants

**Search in Rotated Sorted Array.** At any `mid`, at least one half is sorted. Determine which by comparing `a[lo]` with `a[mid]`; if the target lies inside that sorted half's range, recurse there, else the other half. With duplicates, `a[lo] == a[mid] == a[hi]` gives no information and you must shrink `lo++`/`hi--`, degrading the worst case to O(n).

**Find Minimum in Rotated Sorted Array.** Compare `a[mid]` with `a[hi]` (not `a[lo]`): if `a[mid] > a[hi]` the pivot is right of mid, else it is at mid or left. Comparing against `a[lo]` fails on a non-rotated array.

**Find Peak Element.** No sortedness at all, yet binary search works: if `a[mid] < a[mid+1]`, a peak must exist to the right (the sequence is rising and must eventually fall or hit the boundary). The monotone predicate here is "is the slope descending at mid".

**Median of Two Sorted Arrays.** Binary search on the *partition point* of the smaller array. Find `i` such that `max(left part) <= min(right part)` across both arrays, with `i + j = (m+n+1)//2`. O(log min(m,n)). Hard, and worth writing out once — the sentinel values `±inf` at the edges are what make the code clean.

---

## 5. Binary search on real numbers

No equality test exists, so run a **fixed number of iterations** (≈100 for double precision, or until `hi − lo < 1e-9`). Never `while lo < hi` on floats — it may not terminate.

---

## 6. Complexity

| Situation | Time |
|---|---|
| Sorted array search | O(log n) |
| BS on answer, range R, O(n) check | O(n log R) |
| Sorting first, then searching q times | O(n log n + q log n) |
| Rotated with duplicates | O(n) worst case |

**Binary search only pays off once.** If you must sort first for a single query, the sort dominates and a linear scan is equivalent. Binary search earns its keep with many queries or with a large answer range.

---

## 7. Recall questions

1. What is the actual precondition for binary search?
2. Write Form B from memory and state its loop invariant.
3. Why does Form B never infinite-loop?
4. Define `lower_bound` and `upper_bound`, and express "count of x" with them.
5. What are the three steps of binary search on the answer?
6. Name four phrases in a problem statement that signal BS on the answer.
7. In Find Minimum in Rotated Sorted Array, why compare against `a[hi]` and not `a[lo]`?
8. Why does Find Peak Element work with no sortedness?
9. Why is rotated search with duplicates O(n) worst case?
10. How do you binary search on real numbers?
