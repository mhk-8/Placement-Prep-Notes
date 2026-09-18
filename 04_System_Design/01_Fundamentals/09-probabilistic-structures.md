# Probabilistic Data Structures

> Structures that trade a bounded, quantified amount of accuracy for enormous savings in space. They appear in almost every large system, and they make excellent deep-dive material because the mathematics is simple enough to derive in an interview.

---

## 1. Bloom filter

### What it is
A space-efficient **set membership** test answering one question: *is x possibly in the set, or definitely not?*

- **No false negatives.** If it says "not present", it is definitely not present.
- **False positives are possible.** If it says "present", it might not be.

That asymmetry is the whole design, and it is what makes the structure useful: you use it as a **cheap filter before an expensive lookup**.

### How it works

A bit array of **m** bits and **k** independent hash functions.

**Insert(x):** compute `h₁(x) … h_k(x)`, and set those k bits to 1.
**Query(x):** compute the same k positions. If **any** is 0, x is definitely absent. If **all** are 1, x is probably present — those bits may have been set by other elements.

```
m = 16 bits, k = 3

insert "apple" → bits 2, 7, 11
insert "banana" → bits 4, 7, 14

  0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15
  0 0 1 0 1 0 0 1 0 0  0  1  0  0  1  0

query "apple"  → check 2, 7, 11 → all 1 → "possibly present"  ✔
query "cherry" → check 1, 5, 9  → 1 is 0 → "definitely absent" ✔
query "durian" → check 4, 7, 11 → all 1  → "possibly present"  ✘ FALSE POSITIVE
                 (no one inserted durian; those bits came from apple and banana)
```

**Deletion is not supported.** Clearing a bit could break another element that shares it. A **counting Bloom filter** replaces bits with small counters to allow deletion, at 4× the space.

### The mathematics

With n elements inserted into m bits using k hash functions:

```
False positive rate   p ≈ (1 − e^(−kn/m))^k

Optimal k             k = (m/n) · ln 2  ≈ 0.693 · (m/n)

Required bits         m = −(n · ln p) / (ln 2)²
```

**The number worth memorising:** at the optimal k, you need about **9.6 bits per element for a 1% false positive rate**, and about **14.4 bits per element for 0.1%**.

**Worked example.** 1 billion elements at a 1% false positive rate:
```
m = 1e9 × 9.6 bits ≈ 9.6 Gbit ≈ 1.2 GB
k = 0.693 × 9.6 ≈ 7 hash functions
```
Storing 1 billion 16-byte keys in a hash set would need **16 GB plus overhead** — realistically 30–40 GB. The Bloom filter does the negative-lookup job in **1.2 GB**, a 25× saving. That comparison is the argument to make.

### Where it is used

| System | Use |
|---|---|
| **LSM-tree databases** (Cassandra, RocksDB, HBase) | each SSTable carries a Bloom filter; a read skips files that definitely lack the key, avoiding a disk seek |
| **CDNs** | "cache this only on the second request" — a filter records first sightings |
| **Web crawlers** | has this URL been seen before? |
| **Databases** | avoid a disk lookup for keys that do not exist |
| **Caches** | prevent cache penetration from nonexistent-key requests |
| **Bitcoin / SPV clients** | request only relevant transactions without revealing exactly which |
| **Spell checkers, malicious URL lists** | large dictionaries in small memory |

**The LSM-tree use is the canonical one**, and it shows exactly why the asymmetry matters: a false positive costs one unnecessary disk read, while a false negative would cost *correctness*. The structure's error is on the affordable side.

### Design considerations

- **Size it for the expected n.** As n grows past the design point, the fill rate rises and the false positive rate degrades sharply — eventually to 100%. Monitor the fill ratio.
- **Scalable Bloom filters** chain a series of filters with geometrically decreasing error rates when n is unknown in advance.
- **You do not need k independent hash functions.** Two suffice: `h_i(x) = h₁(x) + i·h₂(x) mod m` (Kirsch–Mitzenmacher) gives k derived hashes with no measurable loss of accuracy — a good implementation detail to mention.

---

## 2. Counting Bloom filter

Replace each bit with a 4-bit counter. Insert increments the k counters; delete decrements them; query checks that all k are non-zero.

**Gains:** deletion.
**Costs:** 4× the memory, and counter overflow if an element is inserted more than 15 times (usually handled by saturating).

Used where the set genuinely shrinks — a cache admission filter, or tracking currently-active items.

---

## 3. HyperLogLog — approximate cardinality

### The problem
Count the number of **distinct** items in a stream. Exactly counting 1 billion distinct user ids needs a set holding all of them: tens of gigabytes.

### The idea
A hash of a random value has a leading run of zeros whose length is geometrically distributed. Seeing a hash beginning with **k zeros** suggests roughly **2^k** distinct values have been observed — because the probability of any single hash starting with k zeros is 2^−k.

A single such estimate has enormous variance, so HyperLogLog **splits the hash space into m registers** (using the first few bits as a register index), tracks the maximum leading-zero count per register, and combines them with a **harmonic mean** — which suppresses the influence of outlier registers.

```
Standard error ≈ 1.04 / √m

m = 16,384 registers × 6 bits each = 12 KB  →  error ≈ 0.81%
```

**12 KB to count billions of distinct items to within 1%.** An exact set would need gigabytes.

### Properties worth knowing
- **Mergeable.** The union of two HyperLogLogs is the element-wise maximum of their registers — so per-server counts combine into a global count with no re-scan. This is what makes it viable for distributed analytics.
- **Not subtractable.** You cannot compute a difference of cardinalities.
- Redis implements it as `PFADD` / `PFCOUNT` / `PFMERGE`.

**Uses:** unique visitors per page per day, distinct IPs hitting an endpoint, distinct search terms, cardinality estimation inside query planners.

---

## 4. Count-min sketch — approximate frequencies

### The problem
Track how often each item appears in a stream, when the number of distinct items is too large to hold counters for.

### The idea
A 2-D array of counters, **d** rows × **w** columns, with one hash function per row.

**Increment(x):** for each row i, increment `count[i][h_i(x) mod w]`.
**Estimate(x):** return the **minimum** of those d counters.

Collisions can only ever *inflate* a counter, so every row gives an overestimate — and taking the minimum picks the least-polluted one.

```
Error bound:  estimate ≤ true + ε·N   with probability 1 − δ
              where w = ⌈e/ε⌉  and  d = ⌈ln(1/δ)⌉
```

**Guarantees:** never underestimates, may overestimate. So it is reliable for finding **heavy hitters** (frequent items) and unreliable for rare ones, whose relative error is largest.

**Uses:** finding the top-k frequent items in a stream, detecting a hot key or a hot partition, network flow monitoring, and trending-content detection.

**Bloom filter vs count-min sketch:** the Bloom filter answers *membership* (is it there?); the count-min sketch answers *frequency* (how often?). A Bloom filter is effectively a count-min sketch with 1-bit saturating counters.

---

## 5. Other structures worth recognising

**Cuckoo filter.** Like a Bloom filter but **supports deletion** and often uses less space at low false-positive rates. Stores short fingerprints in a cuckoo hash table.

**Skip list.** A probabilistic alternative to a balanced tree: a linked list with randomised express lanes, giving expected O(log n) search and insertion with far simpler code than a red-black tree. Used in Redis sorted sets, LevelDB's memtable, and many concurrent maps — it is much easier to make lock-free than a balanced tree.

**MinHash.** Estimates the **Jaccard similarity** of two sets from small signatures. Used for near-duplicate detection in crawlers and recommendation systems.

**Reservoir sampling.** Maintain a uniform random sample of size k from a stream of unknown length, in O(k) space. Element i replaces a random slot with probability k/i.

**t-digest.** Approximates quantiles (p50, p99) in a stream with bounded memory and high accuracy in the tails — which is exactly what latency monitoring needs.

---

## 6. When to reach for these

The decision is always the same shape: **is the exact answer worth its cost?**

| Question | Exact cost | Approximate |
|---|---|---|
| Is x in this huge set? | a full index or disk lookup | **Bloom filter** |
| How many distinct? | store every item | **HyperLogLog** |
| How often does x appear? | a counter per item | **Count-min sketch** |
| What is the p99 latency? | store every sample | **t-digest** |
| Are these two documents similar? | pairwise comparison | **MinHash** |
| A random sample of a stream | buffer everything | **Reservoir sampling** |

**Say the error bound out loud.** "HyperLogLog gives me unique-visitor counts within about 1% using 12 KB per counter, and for a dashboard 1% is invisible" is a far stronger answer than "we'd use HyperLogLog". The quantified trade-off *is* the answer.

---

## 7. Recall questions

1. What exactly does a Bloom filter guarantee, and what does it not?
2. Describe insert and query, and explain why deletion is unsafe.
3. Give the optimal k and the bits-per-element figure for 1% and 0.1% error.
4. Size a Bloom filter for 1 billion elements at 1%, and compare with a hash set.
5. Why is a Bloom filter's error "on the affordable side" in an LSM tree?
6. What happens as n grows past the design point, and what should you monitor?
7. How do you get k hash functions from two?
8. What does a counting Bloom filter gain and cost?
9. Explain HyperLogLog's leading-zeros intuition and why it uses many registers.
10. Why does HyperLogLog use a harmonic mean?
11. Give HyperLogLog's error formula and the standard 12 KB configuration.
12. Why does mergeability matter for distributed counting?
13. Why does a count-min sketch take the minimum across rows?
14. Why is a count-min sketch reliable for heavy hitters and not for rare items?
15. State the difference between a Bloom filter and a count-min sketch in one sentence.
16. Why is a skip list often preferred to a balanced tree in concurrent code?
