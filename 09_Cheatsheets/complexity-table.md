
# Complexity Table

> **Use:** 60-second scan before any coding round. If you cannot state the complexity of your own
> solution the moment you finish describing it, you have not finished describing it.

---

## 1. The constraint → complexity map ⭐⭐⭐ (memorise this first)

| `n` ≤ | Allowed complexity | Typical technique |
|---|---|---|
| 10–12 | O(n!) | Permutations, brute-force TSP |
| 20 | O(2ⁿ) · O(2ⁿ·n) | **Bitmask DP**, subset enumeration |
| 100 | O(n⁴) | 4 nested loops, Floyd-Warshall on small graphs |
| 500 | O(n³) | Floyd-Warshall, matrix chain, interval DP |
| 5·10³ | O(n²) | 2-D DP (edit distance, LCS) |
| 10⁵ | O(n log n) | Sort, heap, binary search, segment tree ⭐ most common |
| 10⁶ | O(n) · O(n log n) tight | Two pointers, prefix sums, sieve, hashing |
| 10⁸ | O(n) with a tiny constant | Simple loop, no allocations inside |
| 10¹⁸ | O(log n) | **Binary search on the answer**, fast power, math |

> **Rule of thumb:** ~10⁸ simple operations per second in C++, ~10⁷ in Python/Java with boxing.
> If your estimate exceeds 10⁹, it will TLE. State the estimate out loud in interviews.

---

## 2. Data-structure operations

| Structure | Access | Search | Insert | Delete | Notes |
|---|---|---|---|---|---|
| Array (static) | O(1) | O(n) | O(n) | O(n) | Best cache locality ⭐ |
| Dynamic array / vector | O(1) | O(n) | O(1)* | O(n) | *amortised push_back; doubling |
| Sorted array | O(1) | O(log n) | O(n) | O(n) | Binary search |
| Singly linked list | O(n) | O(n) | O(1)† | O(1)† | †given the node/prev pointer |
| Doubly linked list | O(n) | O(n) | O(1)† | O(1)† | LRU cache uses this + hash map ⭐ |
| Stack / Queue | — | — | O(1) | O(1) | |
| Hash table | — | O(1) avg, **O(n) worst** | O(1) avg | O(1) avg | ⚠️ worst case on collisions |
| BST (unbalanced) | O(n) | O(n) | O(n) | O(n) | Degenerates on sorted input ⚠️ |
| Balanced BST (AVL/RB) | O(log n) | O(log n) | O(log n) | O(log n) | `std::map`, `TreeMap` |
| Binary heap | O(1) min/max | O(n) | O(log n) | O(log n) | Build from array in **O(n)** ⭐ |
| Fibonacci heap | O(1) min | O(n) | O(1) | O(log n) am. | Theoretical Dijkstra bound |
| Trie | O(L) | O(L) | O(L) | O(L) | L = key length; Σ = alphabet |
| Union-Find (path compr. + rank) | — | O(α(n)) ≈ O(1) | — | — | α = inverse Ackermann ⭐ |
| Segment tree | — | O(log n) query | O(log n) update | — | Build O(n) |
| Fenwick / BIT | — | O(log n) prefix | O(log n) | — | Smaller constant than segtree |
| Sparse table | — | **O(1)** range min | — | — | Build O(n log n), static only |
| Skip list | O(log n) exp. | O(log n) exp. | O(log n) exp. | O(log n) exp. | Redis sorted sets |
| B-tree / B⁺-tree | O(log_B n) | O(log_B n) | O(log_B n) | O(log_B n) | Disk-based; DB indexes ⭐ |

### Heap facts worth remembering
```
heapify (build from n elements)       O(n)       ⭐ not O(n log n) — common interview trap
k largest of n                        O(n log k) with a size-k min-heap
heap-sort                             O(n log n), in-place, NOT stable
decrease-key in a binary heap         O(log n)  (needs an index map)
```

---

## 3. Sorting

| Algorithm | Best | Average | Worst | Space | Stable | When |
|---|---|---|---|---|---|---|
| Bubble / Insertion | O(n) | O(n²) | O(n²) | O(1) | ✅ | Tiny or nearly-sorted input |
| Selection | O(n²) | O(n²) | O(n²) | O(1) | ❌ | Never, except minimising swaps |
| **Merge sort** | O(n log n) | O(n log n) | O(n log n) | **O(n)** | ✅ | Linked lists, external sort, stability |
| **Quicksort** | O(n log n) | O(n log n) | **O(n²)** ⚠️ | O(log n) | ❌ | Fastest in practice; randomise the pivot |
| Heapsort | O(n log n) | O(n log n) | O(n log n) | O(1) | ❌ | Guaranteed bound, in-place |
| Counting sort | O(n+k) | O(n+k) | O(n+k) | O(k) | ✅ | Small integer range k |
| Radix sort | O(d(n+k)) | same | same | O(n+k) | ✅ | Fixed-width integers / strings |
| Bucket sort | O(n+k) | O(n+k) | O(n²) | O(n) | ✅ | Uniformly distributed reals |
| Tim sort | O(n) | O(n log n) | O(n log n) | O(n) | ✅ | Python `sorted`, Java objects ⭐ |

> **Comparison sorts cannot beat Ω(n log n).** Proof sketch: n! leaves in the decision tree,
> height ≥ log₂(n!) = Θ(n log n). Counting/radix beat it by not comparing.

| Library | Algorithm |
|---|---|
| C++ `std::sort` | introsort = quicksort → heapsort on deep recursion → insertion on small ⭐ not stable |
| C++ `std::stable_sort` | merge sort (O(n) extra) |
| Python `sorted` / `list.sort` | Timsort, stable |
| Java `Arrays.sort(int[])` | dual-pivot quicksort (not stable) |
| Java `Arrays.sort(Object[])` / `Collections.sort` | Timsort, stable ⭐ the asymmetry is a classic question |

**Selection:** quickselect — O(n) average, O(n²) worst; median-of-medians gives O(n) worst.

---

## 4. Searching and order statistics

| Problem | Complexity | Note |
|---|---|---|
| Binary search (sorted array) | O(log n) | `lo + (hi-lo)/2` to avoid overflow ⚠️ |
| Binary search on the answer | O(log(range) · check) | Monotone predicate required ⭐ |
| Exponential search | O(log i) | Unbounded / infinite arrays |
| Ternary search | O(log n) | Unimodal functions only |
| k-th smallest (quickselect) | O(n) avg | |
| k-th smallest (two sorted arrays) | O(log(min(m,n))) | Median of two sorted arrays |

---

## 5. Graph algorithms

Notation: V vertices, E edges.

| Algorithm | Complexity | Space | Constraint |
|---|---|---|---|
| BFS / DFS | O(V+E) | O(V) | |
| **0-1 BFS** (deque) | O(V+E) | O(V) | Edge weights ∈ {0,1} ⭐ |
| **Dijkstra** (binary heap) | O((V+E) log V) | O(V) | **No negative edges** ⚠️ |
| Dijkstra (Fibonacci heap) | O(E + V log V) | O(V) | Theoretical |
| **Bellman-Ford** | O(V·E) | O(V) | Handles negative edges; **detects negative cycles** |
| SPFA (queue Bellman-Ford) | O(E) avg, O(VE) worst | O(V) | |
| **Floyd-Warshall** | O(V³) | O(V²) | All-pairs; negative edges OK, no neg. cycles |
| Johnson's | O(V² log V + VE) | O(V²) | All-pairs sparse, negative edges |
| **Topological sort** (Kahn / DFS) | O(V+E) | O(V) | DAG only; Kahn also detects cycles ⭐ |
| **Kruskal MST** | O(E log E) | O(V) | Sort + union-find |
| **Prim MST** (heap) | O(E log V) | O(V) | Better on dense graphs with an adjacency matrix: O(V²) |
| Tarjan / Kosaraju SCC | O(V+E) | O(V) | Kosaraju = 2 DFS passes |
| Bridges / articulation points | O(V+E) | O(V) | Tarjan low-link |
| Euler path / circuit (Hierholzer) | O(E) | O(E) | Degree conditions first |
| Bipartite check | O(V+E) | O(V) | 2-colour BFS |
| **Max flow — Edmonds-Karp** | O(V·E²) | O(V+E) | BFS augmenting paths |
| Max flow — Dinic | O(V²·E), O(E√V) unit | O(V+E) | |
| Bipartite matching (Hopcroft-Karp) | O(E√V) | O(V+E) | |
| LCA — binary lifting | O(n log n) build, O(log n) query | O(n log n) | |
| LCA — Euler tour + sparse table | O(n log n) build, **O(1)** query | O(n log n) | |
| Δ-stepping (parallel SSSP) | work ≈ Dijkstra, more parallelism | O(V) | ⭐ your SSSP project |

> ⚠️ **The most common Dijkstra mistake:** using it with negative weights "because it usually
> works". It can finalise a node too early. Say *why* it fails — the greedy invariant that the
> minimum-tentative-distance node is final breaks.

### Representation trade-off
| | Space | Edge exists? | Iterate neighbours |
|---|---|---|---|
| Adjacency matrix | O(V²) | O(1) | O(V) |
| Adjacency list | O(V+E) | O(deg) | O(deg) ⭐ default |
| **CSR** (row-ptr + col-idx) | O(V+E) | O(deg) | O(deg), contiguous ⭐ GPU-friendly |

---

## 6. Dynamic programming ⚠️ (your weakest topic — read this twice)

| Problem | Time | Space | Space-optimised |
|---|---|---|---|
| Fibonacci | O(n) | O(n) | O(1) |
| **0/1 knapsack** | O(n·W) | O(n·W) | **O(W)** — iterate W downward ⭐ |
| Unbounded knapsack | O(n·W) | O(W) | iterate W upward |
| Subset sum / partition | O(n·S) | O(S) | bitset → O(n·S/64) ⭐ |
| **LIS** | O(n²) naive, **O(n log n)** | O(n) | patience sorting + binary search |
| **LCS** | O(n·m) | O(n·m) | O(min(n,m)) for length only |
| Edit distance | O(n·m) | O(n·m) | O(min(n,m)) |
| Matrix chain multiplication | O(n³) | O(n²) | — |
| Coin change (min coins) | O(n·amount) | O(amount) | — |
| Coin change (count ways) | O(n·amount) | O(amount) | ⚠️ loop order decides combinations vs permutations |
| Longest palindromic subsequence | O(n²) | O(n²) | = LCS(s, reverse(s)) |
| Palindrome partitioning (min cuts) | O(n²) | O(n²) | |
| **Bitmask DP / TSP** | O(2ⁿ·n²) | O(2ⁿ·n) | n ≤ 20 |
| Digit DP | O(digits · state · 10) | same | counting numbers ≤ N |
| Tree DP | O(n) | O(n) | one DFS, combine children |
| Rerooting | O(n) | O(n) | answer for every root |

> **The 0/1-knapsack space trick, stated precisely:** with a 1-D array `dp[w]`, iterate
> `w` from `W` down to `wt[i]`. Downward prevents reusing item *i* in the same pass. Upward gives
> you the **unbounded** version. Being able to explain this distinction is a frequent follow-up. ⭐

---

## 7. String algorithms

| Algorithm | Build | Query | Use |
|---|---|---|---|
| Naive matching | — | O(n·m) | |
| **KMP** | O(m) failure fn | O(n) | Single pattern ⭐ implement once by hand |
| Z-algorithm | O(n) | — | Prefix matching, pattern as `p#s` |
| Rabin-Karp (rolling hash) | O(m) | O(n) avg | Multiple patterns, 2 mods to avoid collisions |
| Aho-Corasick | O(Σ·total) | O(n + matches) | Many patterns at once |
| Trie | O(Σ·total) | O(L) | Prefix queries, autocomplete |
| Manacher | O(n) | — | All palindromic substrings |
| Suffix array | O(n log n) | O(m log n) | |
| Suffix automaton / tree | O(n) | O(m) | Advanced |
| Levenshtein (DP) | O(n·m) | — | |

---

## 8. Mathematics and number theory

| Operation | Complexity |
|---|---|
| gcd (Euclid) | O(log min(a,b)) |
| Extended Euclid / modular inverse | O(log m) |
| Modular exponentiation (fast power) | O(log e) |
| Sieve of Eratosthenes (primes ≤ n) | O(n log log n) |
| Linear sieve / smallest prime factor | O(n) |
| Primality — Miller-Rabin | O(k log³ n) |
| Factorise by trial division | O(√n) |
| Pollard's rho | O(n^¼) expected |
| nCr mod p (precomputed factorials) | O(n) build, O(1) query ⭐ |
| Matrix exponentiation (k×k) | O(k³ log n) |
| FFT / NTT convolution | O(n log n) |
| Fast Fibonacci (matrix power) | O(log n) |

---

## 9. Amortised analysis — the three to be able to justify ⭐

| Case | Claim | Argument |
|---|---|---|
| `vector::push_back` | O(1) amortised | Doubling: total copies over n pushes ≤ 2n (geometric series) |
| Union-Find with compression + rank | O(α(n)) ≈ O(1) | Tarjan's analysis; α(n) < 5 for any n you will ever see |
| Monotonic stack / two pointers | O(n) overall | Each element is pushed once and popped once |

---

## 10. Space complexity you forget to count ⚠️

```
Recursion depth          → O(depth) stack. DFS on a path graph of 10⁵ nodes WILL overflow.
Memoisation table        → the dominant term in most top-down DP.
Sorting                  → merge O(n), quicksort O(log n) stack, heapsort O(1).
Output                   → usually excluded, but say so rather than ignoring it.
Hash map of n entries    → ~32-64 bytes per entry in practice, not 8. Matters at n = 10⁷.
```

---

## Recall questions
1. `n = 10⁵`, and your solution is O(n²). Will it pass? What complexity do you need?
2. Why is `Arrays.sort` on `int[]` not stable in Java, but stable on `Integer[]`?
3. What is the complexity of building a heap from an unsorted array, and why is it not O(n log n)?
4. In 1-D 0/1 knapsack, which direction do you iterate the weight loop, and what happens if you flip it?
5. Name two algorithms Dijkstra cannot replace and say why.
6. What is `α(n)` and roughly how large does it get?
