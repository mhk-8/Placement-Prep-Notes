# Hashing — Flashcards

## Questions

1. Name the two collision-resolution families and one trade-off of each.
2. What is the load factor, and what happens when it is exceeded?
3. Why is hash-map insertion O(1) amortised rather than worst-case?
4. What is the worst-case complexity of a hash-map lookup and when does it occur?
5. Write the counting template for "number of subarrays summing to k".
6. Why must `seen[0]` be seeded to 1?
7. Why does sliding window fail for "subarray sum equals k" with negatives?
8. How do you count subarrays whose sum is divisible by k?
9. How do you turn "equal number of 0s and 1s" into a prefix-sum problem?
10. Why is Longest Consecutive Sequence O(n) despite an inner while loop?
11. State the equals/hashCode contract.
12. What does `mp[key]` do in C++ when the key is absent?
13. When do you choose `std::map` over `std::unordered_map`?
14. What is an anti-hash test, and what is the defence?
15. How do you encode a grid cell (r, c) as a single integer key?

---

## Answers

1. Separate chaining (bucket holds a list; simple, tolerates α > 1, pointer overhead) and open addressing (probe for another slot; cache-friendly, no pointers, degrades at high load, deletion needs tombstones).
2. entries ÷ buckets. Past the threshold (~0.75) the table rehashes into a larger array — an O(n) operation.
3. Because rehashing is O(n) but happens only after Θ(n) insertions, so the cost spreads to O(1) per insert.
4. O(n), when every key hashes to the same bucket.
5. `seen={0:1}; run=cnt=0; for x in a: run+=x; cnt+=seen[run−k]; seen[run]+=1`.
6. It represents the empty prefix, which is what a subarray starting at index 0 needs as its left boundary.
7. Sliding window requires that extending the window moves the metric monotonically. Negatives break monotonicity, so shrinking from the left is not safe.
8. Key the map on `run % k` (normalised to be non-negative) instead of `run`, and count equal residues.
9. Map every 0 to −1; then "equal counts" is exactly "sum equals 0", solved with a first-index map.
10. A run is only started at values `x` with `x−1` absent, so each element is touched by the inner loop at most once across all runs.
11. If two objects are equal, their hash codes must be equal. Violating it makes lookups fail even for keys that compare equal.
12. It **inserts** a default-constructed value and returns a reference to it. Use `.count()` or `.find()` for membership tests.
13. When you need ordering, `lower_bound`/`upper_bound`, range iteration, or guaranteed O(log n) rather than average O(1).
14. A test crafted so that all keys collide under a known hash function (C++'s integer hash is the identity), forcing O(n²). Defend with a randomised custom hash seeded from the clock.
15. `r * C + c`, where C is the number of columns — unique because `0 ≤ c < C`.
