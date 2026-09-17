# Arrays & Strings — Flashcards

## Questions

1. Give the 2-D prefix-sum build formula and the rectangle-query formula.
2. How does a difference array support O(1) range updates?
3. State Kadane's recurrence and its one-line justification.
4. What goes wrong with Kadane when all elements are negative?
5. Why does maximum product subarray track a running minimum?
6. Describe the three reversals that rotate an array right by k.
7. In Dutch national flag, why is `mid` not advanced on a swap with `high`?
8. How do you solve "product of array except self" without division and in O(1) extra space?
9. Why is `s += c` in a Python loop quadratic?
10. How many centres does expand-around-centre use, and why that number?
11. What does `pi[i]` mean in the KMP prefix function?
12. How do you rotate a square matrix 90° clockwise in place?
13. What is wrong with `[[0]*C]*R` in Python?
14. How do you detect a duplicate in an array of values 1..n using O(1) extra space?
15. Which technique handles many range updates followed by one read, and which handles many range reads with no updates?

---

## Answers

1. `S[r+1][c+1] = S[r][c+1] + S[r+1][c] − S[r][c] + g[r][c]`; rectangle = `S[r2+1][c2+1] − S[r1][c2+1] − S[r2+1][c1] + S[r1][c1]`. Inclusion–exclusion: the overlap subtracted twice is added back.
2. `diff[l] += v; diff[r+1] −= v`, then prefix-sum the whole array once at the end. q updates + one O(n) pass.
3. `cur = max(x, cur + x)`, `best = max(best, cur)`. A prefix with a negative running sum can never improve a later subarray, so it is discarded.
4. If `best` is initialised to 0 it returns 0. Initialise `best = cur = a[0]`.
5. A negative multiplied by the running minimum can become the new maximum, so both extremes must be carried.
6. Reverse all, reverse the first k, reverse the rest — after `k %= n`.
7. The element swapped in from `high` has not yet been classified, so it must be examined at the same `mid`.
8. Two passes: a left-running product written into the result, then a right-running product multiplied in. The output array does not count as extra space.
9. Strings are immutable, so each `+=` copies the whole accumulated string: 1+2+…+n = O(n²). Use a list and `''.join(...)`.
10. 2n−1: n single-character centres for odd-length palindromes and n−1 gap centres for even-length ones.
11. The length of the longest proper prefix of `s[0..i]` that is also a suffix of it — the failure link used to skip re-comparisons.
12. Transpose, then reverse each row. (Anticlockwise: transpose, then reverse each column.)
13. It makes R references to one list object, so writing to one row writes to all. Use a comprehension.
14. Mark visited values by negating `a[abs(x)−1]`, or place each value at its index; a second occurrence lands on an already-negative slot. (Floyd's cycle detection also works and does not modify the input.)
15. Difference array for update-heavy; prefix sums for read-heavy. Both updates and reads → Fenwick/segment tree.
