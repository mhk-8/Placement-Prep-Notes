# Hashing — Concepts

## 1. Core idea in 3 lines
A hash table trades memory for time: it maps a key to a bucket via a hash function, giving O(1) average insert, lookup and delete. Almost every O(n²) "check every pair" scan collapses to O(n) by remembering what you have already seen. The price is that you lose ordering, and the worst case is O(n).

---

## 2. How a hash table actually works

**Hash function → bucket index.** A good hash distributes keys uniformly and is fast to compute. `index = hash(key) mod capacity`.

**Collisions** are inevitable (pigeonhole: more possible keys than buckets). Two resolution families:

| Strategy | How | Trade-off |
|---|---|---|
| **Separate chaining** | Each bucket holds a list (or tree) of entries | Simple, tolerates load factor > 1, extra pointer memory. Java's HashMap converts a long chain to a red-black tree |
| **Open addressing** | On collision, probe for another slot: linear (`i+1`), quadratic (`i+k²`), or double hashing | Cache-friendly, no pointers, but degrades badly at high load and deletion needs tombstones |

**Load factor** α = entries / buckets. When α exceeds a threshold (typically 0.75), the table **rehashes**: allocate a larger array and reinsert everything, O(n). This is why insert is O(1) *amortised*, not worst-case.

**Worst case is O(n)** when all keys collide. Adversarial inputs exploiting a known hash function are real: Codeforces-style judges have "anti-hash" tests against C++'s `unordered_map`, whose hash for integers is the identity. The defence is a randomised custom hash.

**Ordering is not guaranteed and must never be relied on.** If you need ordering, floor/ceiling, or range queries, use a tree map (`std::map`, `TreeMap`, `SortedDict`) at O(log n).

---

## 3. The patterns

### Seen-set — "has this appeared before?"
Contains-duplicate, first-repeating, cycle detection on values. One pass, O(n).

### Frequency map — counting
`Counter(a)` / `unordered_map<int,int>`. Powers: top-K frequent, majority element, anagram checks, "can we form X from Y". For lowercase letters only, a 26-length array beats a hash map on both speed and clarity.

### Complement lookup — the two-sum family
For each `x`, ask whether `target − x` has already been seen. **Store as you go**, checking before inserting, so an element is not paired with itself.
- 3Sum: sort, fix one element, then two pointers — O(n²). The hash-map variant is the same complexity but duplicate handling is much messier, so prefer two pointers.
- 4Sum II (four separate arrays): hash the pairwise sums of the first two arrays, then look up the negation from the other two — O(n²) instead of O(n⁴).

### Prefix sum + hash map — the highest-yield combination
"Count subarrays whose sum is k", **with negative numbers allowed** (which rules out sliding window). Maintain a running prefix sum and a map from prefix value → how many times it occurred. At each step, `count += seen[run − k]`.
**Seed `seen[0] = 1`** — the empty prefix — or every subarray starting at index 0 is missed.

Variants of the same skeleton:
- Subarray sum divisible by k → key on `run % k` (normalise negatives with `((r % k) + k) % k`)
- Longest subarray with sum k → store the **first** index of each prefix value, never overwrite
- Contiguous array of equal 0s and 1s → map 0 to −1, then it is "sum equals 0"
- Subarray with equal counts of several characters → key on a tuple of differences

### Grouping by canonical key
Group anagrams (key = sorted string or a 26-tuple), group by shape/pattern/slope. The skill is choosing a key that is identical for exactly the things that should group.

### Index map
"Return the indices, not the values" — store `value → index`. Also used for Longest Consecutive Sequence: put everything in a set, then start a run only at values `x` where `x−1` is absent, giving O(n) overall even though it looks quadratic.

---

## 4. Custom keys

| Key type | Python | C++ |
|---|---|---|
| Tuple | native, hashable | `map<pair<int,int>,V>` (ordered) or a custom hash for `unordered_map` |
| List | **not hashable** — convert to tuple | use `vector` with `map`, or encode |
| Object | define `__hash__` and `__eq__` consistently | specialise `std::hash` |

**The contract:** equal objects must have equal hashes. Breaking it (overriding `equals` without `hashCode` in Java) causes lookups to silently fail — a favourite interview question.

**Encoding trick:** a pair of small non-negative ints can be packed as `r * C + c` (grid cells) or `a * 10**6 + b`, avoiding tuple overhead entirely.

---

## 5. When hashing is the wrong tool

- You need sorted order, predecessors/successors, or range queries → tree map.
- You need the k smallest repeatedly → heap.
- Keys are dense small integers → a plain array is faster and simpler.
- Memory is tight → a hash map's overhead per entry is substantial; consider sorting + two pointers at O(n log n), O(1) space.
- You need worst-case guarantees → hashing gives averages only.

---

## 6. Recall questions

1. Explain separate chaining vs open addressing and one trade-off each.
2. What is the load factor and what happens when it is exceeded?
3. Why is hash-map insert O(1) *amortised* rather than worst-case?
4. Give the prefix-sum + hash-map template for counting subarrays summing to k, and explain the `seen[0] = 1` seed.
5. Why can sliding window not solve "subarray sum equals k" with negatives?
6. How do you count subarrays whose sum is divisible by k?
7. What is the equals/hashCode contract and what breaks if you violate it?
8. How is Longest Consecutive Sequence O(n) despite the inner while loop?
9. When would you choose a tree map over a hash map?
10. What is an anti-hash test and how do you defend against it?
