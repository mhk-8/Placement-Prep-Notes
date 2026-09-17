# Arrays & Strings — Pitfalls

## Kadane
- Initialising `best = 0` — returns 0 for an all-negative array instead of the largest element. Initialise from `a[0]`.
- Forgetting that the empty subarray may or may not be allowed; ask.
- Applying plain Kadane to *products* — negatives flip the sign, so you need both a running max and min.

## Prefix sums
- Off-by-one: the prefix array has n+1 entries, and `sum(l..r) = pre[r+1] - pre[l]`. Write this down before using it.
- Forgetting to seed `seen[0] = 1` in the prefix-sum + hash-map counting pattern — it misses subarrays starting at index 0.
- Using prefix *products* when a zero can appear.
- 32-bit overflow on the prefix array.

## In-place work
- Rotation without `k %= n`.
- Advancing `mid` after swapping with `high` in Dutch national flag — the swapped-in value has not been examined.
- Mutating a list while iterating it.
- "Set matrix zeroes": using the first row/column as markers but forgetting the flag for column 0 itself, so it gets clobbered.
- Spiral traversal without the `top <= bot` and `left <= right` guards before the third and fourth legs — single-row or single-column matrices get revisited.

## Strings
- `s += c` inside a loop in Python or Java: O(n²). Use a list + `join`, or `StringBuilder`.
- Slicing inside a loop (`s[i:]`) — each slice copies.
- Assuming lowercase-only input and indexing a 26-array with a space, digit or uppercase character.
- Comparing strings with `==` in Java (compares references, not content — use `.equals`).
- Forgetting that Unicode characters are not one byte, if the problem says so.
- Expand-around-centre: forgetting the even-length centres (`i, i+1`), which halves the answers.
- Off-by-one when returning the substring after expansion — the loop overshoots by one on both sides, so the answer is `s[l+1:r]`.

## Matrices
- Transposing with `for c in range(n)` instead of `range(r+1, n)` — swaps everything twice, leaving the matrix unchanged.
- Confusing rows and columns in a non-square matrix.
- `[[0]*C]*R` in Python creates R references to the **same** row. Use `[[0]*C for _ in range(R)]`. This is a silent, devastating bug.

## General
- Not checking for an empty array before touching `a[0]`.
- Reading indices as values or vice versa.
- Assuming the input is sorted when the problem never said so.
