# Complexity Cheatsheet

> One page. Memorise the tables; do not look them up in an interview.

---

## 1. Growth rates, smallest to largest

O(1) < O(log n) < O(√n) < O(n) < O(n log n) < O(n²) < O(n³) < O(2ⁿ) < O(n!)

**Rough operation budget:** ~10⁸ simple operations per second.

| n | n log n | n² | 2ⁿ |
|---|---|---|---|
| 10³ | 10⁴ | 10⁶ ✅ | astronomical |
| 10⁵ | ~1.7×10⁶ ✅ | 10¹⁰ ❌ | — |
| 10⁶ | 2×10⁷ ✅ | 10¹² ❌ | — |
| 10⁷ | 2.3×10⁸ ⚠️ | ❌ | — |

---

## 2. Data structure operations (average / worst)

| Structure | Access | Search | Insert | Delete | Notes |
|---|---|---|---|---|---|
| Array (static) | O(1) | O(n) | — | — | |
| Dynamic array | O(1) | O(n) | **O(1) amortised** push-back | O(n) middle | Doubling gives the amortised bound |
| Sorted array | O(1) | O(log n) | O(n) | O(n) | |
| Singly linked list | O(n) | O(n) | O(1) at head | O(1) given prev | |
| Doubly linked list | O(n) | O(n) | O(1) | O(1) given node | Basis of LRU |
| Stack / Queue / Deque | — | — | O(1) | O(1) | |
| Hash map / set | — | O(1) avg, **O(n) worst** | O(1) avg | O(1) avg | Unordered |
| Balanced BST (tree map) | — | O(log n) | O(log n) | O(log n) | Ordered; floor/ceil/range |
| Binary heap | O(1) top | O(n) | O(log n) | O(log n) pop-top | **Build heap O(n)** |
| Trie | — | O(L) | O(L) | O(L) | L = key length; O(Σ·L) space |
| Union-Find | — | ~O(α(n)) | — | — | With path compression + union by rank |
| Fenwick (BIT) | — | O(log n) | O(log n) point update | — | Prefix aggregates |
| Segment tree | — | O(log n) range query | O(log n) point/range update | — | O(n) build, O(4n) space |
| Sparse table | — | **O(1)** idempotent range query | — | — | O(n log n) build, static only |

**Traps:** hash map worst case is O(n), not O(1) — say "amortised/average". Heap search for an arbitrary element is O(n), only the top is O(1).

---

## 3. Sorting

| Algorithm | Best | Average | Worst | Space | Stable | In-place |
|---|---|---|---|---|---|---|
| Bubble | O(n) | O(n²) | O(n²) | O(1) | ✅ | ✅ |
| Insertion | O(n) | O(n²) | O(n²) | O(1) | ✅ | ✅ | 
| Selection | O(n²) | O(n²) | O(n²) | O(1) | ❌ | ✅ |
| **Merge** | O(n log n) | O(n log n) | O(n log n) | **O(n)** | ✅ | ❌ |
| **Quick** | O(n log n) | O(n log n) | **O(n²)** | O(log n) | ❌ | ✅ |
| **Heap** | O(n log n) | O(n log n) | O(n log n) | O(1) | ❌ | ✅ |
| Counting | O(n+k) | O(n+k) | O(n+k) | O(k) | ✅ | ❌ |
| Radix | O(d(n+k)) | O(d(n+k)) | O(d(n+k)) | O(n+k) | ✅ | ❌ |
| Bucket | O(n+k) | O(n+k) | O(n²) | O(n) | ✅ | ❌ |

- **Comparison-sort lower bound: Ω(n log n)** — from the log₂(n!) decision-tree argument. Counting/radix beat it because they are not comparison sorts.
- Insertion sort is O(n) on nearly-sorted input and is what library sorts use for small subarrays.
- Library sorts: C++ `std::sort` = introsort (quick + heap + insertion), **not stable**; `stable_sort` = merge. Java: primitives → dual-pivot quicksort; objects → Timsort (**stable**). Python `sorted` = Timsort (**stable**).

---

## 4. Graph algorithms — V vertices, E edges

| Algorithm | Time | Space | Use when |
|---|---|---|---|
| BFS / DFS | O(V+E) | O(V) | Traversal, components, unweighted shortest path |
| Topological sort | O(V+E) | O(V) | DAG ordering, dependency resolution |
| **Dijkstra** (binary heap) | O((V+E) log V) | O(V) | Non-negative weights |
| **Bellman-Ford** | O(V·E) | O(V) | Negative weights; detects negative cycles |
| **Floyd-Warshall** | O(V³) | O(V²) | All pairs, V ≤ ~400 |
| Kruskal | O(E log E) | O(V) | MST, sparse graphs |
| Prim (heap) | O(E log V) | O(V) | MST, dense graphs |
| Union-Find op | ~O(α(n)) ≈ O(1) | O(V) | Dynamic connectivity |
| Tarjan SCC / bridges | O(V+E) | O(V) | Strong components, critical edges |
| 0-1 BFS | O(V+E) | O(V) | Weights only 0 or 1 |

Adjacency list: O(V+E) space, O(deg) neighbour scan. Adjacency matrix: O(V²) space, O(1) edge lookup.

---

## 5. Common recurrences (master theorem)

For T(n) = a·T(n/b) + O(n^d):
- a < b^d → **O(n^d)**
- a = b^d → **O(n^d log n)**
- a > b^d → **O(n^{log_b a})**

| Recurrence | Solution | Example |
|---|---|---|
| T(n) = T(n/2) + O(1) | O(log n) | Binary search |
| T(n) = T(n/2) + O(n) | O(n) | Quickselect (average) |
| T(n) = 2T(n/2) + O(1) | O(n) | Tree traversal, heapify |
| T(n) = 2T(n/2) + O(n) | O(n log n) | Merge sort |
| T(n) = 2T(n/2) + O(n²) | O(n²) | |
| T(n) = T(n−1) + O(1) | O(n) | Linear recursion |
| T(n) = T(n−1) + O(n) | O(n²) | Selection sort |
| T(n) = 2T(n−1) + O(1) | O(2ⁿ) | Naive Fibonacci, subsets |

---

## 6. Useful identities

- 1 + 2 + … + n = n(n+1)/2 = **O(n²)**
- 1 + 2 + 4 + … + 2ᵏ = 2^{k+1} − 1 = **O(2ᵏ)** — the last term dominates
- n/1 + n/2 + … + n/n = n·Hₙ ≈ **O(n log n)** — the sieve / divisor-loop bound
- Σ log i = log(n!) = **O(n log n)**
- Number of subsets = 2ⁿ · subsets of a set of size n summed over all subsets = 3ⁿ (submask enumeration)
- Height of a balanced BST / heap = ⌊log₂ n⌋

---

## 7. Space complexity — what counts

**Auxiliary space** excludes the input. An interviewer asking for "O(1) space" means O(1) *auxiliary*.

Recursion costs O(depth) stack space, and it is real: a recursive DFS on a 10⁵-node path graph overflows Python's default 1000-frame limit and risks a segfault in C++.

| Technique | Space |
|---|---|
| In-place array manipulation | O(1) |
| Recursion on a balanced tree | O(log n) |
| Recursion on a skewed tree / path | O(n) |
| 2-D DP table | O(n·m) → often reducible to **O(min(n,m))** by keeping two rows |
| Memoisation | O(number of distinct states) |

---

## 8. How to state complexity in an interview

> "This is O(n log n) time — the sort dominates the linear scan — and O(n) auxiliary space for the hash map. Worst case for the hash map is O(n) per lookup if every key collides, so the O(n) total is average-case."

Name the dominating step, separate time from space, say *auxiliary*, and flag average vs worst where they differ. That single sentence is often worth as much as the solution.
