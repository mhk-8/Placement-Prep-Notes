# Arrays & Strings — Concepts

## 1. Core idea in 3 lines
An array is contiguous memory with O(1) indexing — every other property follows from that. Most OA problems are array problems wearing a costume, so the real skill is recognising which of about six array techniques applies. Strings are arrays of characters plus immutability rules that decide whether your solution is O(n) or O(n²).

---

## 2. The array techniques, in order of how often they appear

### Prefix sums — range queries in O(1) after O(n) setup
`pre[i+1] = pre[i] + a[i]`, then `sum(a[l..r]) = pre[r+1] - pre[l]`.
Use when there are **many range-sum queries and no updates**. With updates, you need a Fenwick tree (`17_Advanced_DS`).

Variants: prefix XOR (same idea, XOR instead of +), prefix products (careful with zeros), prefix max, and **2-D prefix sums** for submatrix sums:
`S[r][c] = S[r-1][c] + S[r][c-1] - S[r-1][c-1] + a[r][c]` (inclusion–exclusion).

### Difference array — range *updates* in O(1), one final pass
To add `v` to `a[l..r]`: `diff[l] += v; diff[r+1] -= v`. After all updates, prefix-sum `diff` to recover the array. This turns q range updates from O(q·n) into O(q + n). It is the standard answer to "apply these 10⁵ range increments".

### Kadane — maximum subarray in O(n)
Track `cur = max(x, cur + x)` and `best = max(best, cur)`. The insight: a prefix with negative running sum can never help a later subarray, so discard it.
- **All-negative input:** initialise `best = -inf` and `cur = 0` carefully, or the answer comes out 0 instead of the largest single element. This is the most common Kadane bug.
- **Maximum product subarray** needs both a running max *and* a running min, because a negative times a negative flips.
- **Circular maximum subarray** = max(normal Kadane, total − minimum subarray), with the all-negative case handled separately.

### In-place manipulation — O(1) space
- **Reversal rotation:** to rotate right by k, reverse the whole array, then reverse the first k, then the rest. Remember `k %= n`.
- **Dutch national flag** (Sort Colors): three pointers `low, mid, high`; swap 0s to the front and 2s to the back in one pass. Do **not** advance `mid` after swapping with `high` — the incoming value is unexamined.
- **Marking by sign or by index:** when values are in [1, n], encode "seen" by negating `a[|x|-1]` or by adding n. This is how "find the duplicate/missing in O(1) space" problems are intended.
- **Two-pointer overwrite:** for "remove element" / "remove duplicates from sorted array", keep a write index that lags the read index.

### Matrix manipulation
- **Rotate 90° clockwise in place** = transpose, then reverse each row. (Anticlockwise = transpose, then reverse each column.)
- **Spiral traversal**: four boundaries (top, bottom, left, right), shrink after each pass, and check `top <= bottom` / `left <= right` *before* the third and fourth legs or you revisit a row.
- **Set matrix zeroes in O(1) space**: use row 0 and column 0 as the marker storage, with a separate flag for column 0 itself.

---

## 3. Strings

### Cost model — the decisive fact
Strings are **immutable** in Python and Java. `s += c` in a loop copies the whole string each time: O(n²) total. This is the single most common cause of a TLE on an otherwise-correct OA solution.

| Language | Build a string in a loop | Notes |
|---|---|---|
| Python | `''.join(parts)` on a list | `+=` is O(n²) |
| Java | `StringBuilder` | `String +=` is O(n²) |
| C++ | `s += c` on `std::string` | Mutable, amortised O(1) — fine |

Also: `s[i:j]` in Python copies O(j−i); `s.substr()` in C++ copies too. Inside a loop, slicing turns O(n) into O(n²).

### Character arithmetic
`ord(c) - ord('a')` maps 'a'..'z' to 0..25 — the basis of the 26-length frequency array that replaces a hash map for lowercase-only problems (faster and it makes the "compare two frequency maps" step O(26)).

### Palindromes
- **Two-pointer check:** O(n) time, O(1) space.
- **Longest palindromic substring — expand around centre:** O(n²) time, O(1) space, 2n−1 centres (n single characters + n−1 gaps). This is the expected answer; Manacher's O(n) is rarely required.
- **Longest palindromic *subsequence*** is a different problem — that is 2-D DP (`13_Dynamic_Programming`).

### Anagrams
Two strings are anagrams iff their character counts match. Sorting gives O(n log n); counting gives O(n). For **grouping** anagrams, the canonical key is either the sorted string or a 26-tuple of counts.

### Pattern matching
- **Naive:** O(n·m).
- **KMP:** O(n+m) using the prefix function π, where π[i] is the length of the longest proper prefix of `s[0..i]` that is also a suffix. The failure-link idea is what interviewers probe.
- **Z-function:** z[i] = length of the longest substring starting at i that matches a prefix. Often simpler to code than KMP.
- **Rabin-Karp rolling hash:** O(n+m) average; useful for "find duplicate substrings of length L", usually combined with binary search on L. Beware hash collisions — use a large prime modulus, or two.

These three are **P3**: worth understanding, rarely required in an OA.

---

## 4. Choosing the right technique

| Signal in the problem | Technique |
|---|---|
| Many range-sum queries, no updates | Prefix sums |
| Many range *updates*, one final read | Difference array |
| "Maximum sum of a contiguous subarray" | Kadane |
| "Subarray sum equals k" with negatives | Prefix sum + hash map |
| Contiguous + a monotone constraint | Sliding window (`03`) |
| Sorted array, find a pair | Two pointers (`03`) |
| "O(1) extra space", values in [1, n] | Index/sign marking |
| Submatrix sums | 2-D prefix sums |
| "Rotate", "reverse", "in place" | Reversal trick |

---

## 5. Recall questions

1. Give the 2-D prefix-sum formula and explain the inclusion–exclusion.
2. How does a difference array turn q range updates into O(q + n)?
3. What is Kadane's invariant, and what breaks when all values are negative?
4. Why does maximum *product* subarray need a running minimum?
5. Describe the reversal trick for rotating an array by k.
6. Why must `mid` not advance after swapping with `high` in Dutch national flag?
7. Why is `s += c` in a Python loop O(n²), and what replaces it?
8. How many centres does "expand around centre" consider, and why?
9. What does π[i] mean in KMP?
10. How do you rotate a matrix 90° clockwise in place?
