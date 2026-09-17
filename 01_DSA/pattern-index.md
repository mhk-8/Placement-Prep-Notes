# Pattern Index — Symptom → Pattern

> The lookup table you scan in the first 5 minutes of an OA. Read the constraints, find the row, apply the template from `templates.md`.
> **Code convention in this vault:** templates are written in Python (shortest to read); each `patterns.md` carries a *C++ notes* section for the STL calls that differ.

---

## 1. Constraints → intended complexity

The constraint line is the strongest hint in the problem. Read it before the story.

| n (or total input) | Intended complexity | Likely pattern |
|---|---|---|
| n ≤ 10–12 | O(n!) | Permutation backtracking |
| n ≤ 20–25 | O(2ⁿ) or O(2ⁿ·n) | Subset backtracking, **bitmask DP**, meet-in-the-middle |
| n ≤ 100 | O(n³) | Floyd-Warshall, interval DP, matrix chain |
| n ≤ 500–1 000 | O(n²) | 2-D DP, string DP, brute pair scan |
| n ≤ 10⁵ | **O(n log n)** | Sort, binary search, heap, set/map, DSU, Dijkstra |
| n ≤ 10⁶ | **O(n)** | Two pointers, sliding window, prefix sums, hashing, counting sort |
| n ≤ 10⁹ | O(log n) or O(√n) | Binary search **on the answer**, maths, fast exponentiation |
| n ≤ 10¹⁸ | O(log n) | Number theory, matrix exponentiation, digit DP |

**Corollary:** if your idea is O(n²) and n = 10⁵, it is the wrong idea — do not start typing it. Roughly 10⁸ simple operations per second is the working budget.

---

## 2. Symptom → pattern

| The problem says… | Reach for | Folder |
|---|---|---|
| "subarray sum equals k", "count subarrays with…" | Prefix sum + hash map | `01`, `02` |
| "sum of range [l, r]", many queries, no updates | Prefix sums | `01` |
| "range update, range query" | Fenwick / segment tree | `17` |
| "contiguous subarray", "substring", with a constraint | **Sliding window** | `03` |
| "longest/shortest substring such that…" | Variable-size window | `03` |
| "exactly K distinct" | at-most(K) − at-most(K−1) | `03` |
| array is **sorted**, find a pair/triplet | Two pointers | `03` |
| "find the pair that sums to X", unsorted | Hash map | `02` |
| "detect a cycle in a linked list", "find the middle" | Fast/slow pointers | `07` |
| sorted array + "find", "first ≥ x", "insert position" | Binary search | `04` |
| **"minimum possible maximum"**, "maximum possible minimum", "k-th smallest" | **Binary search on the answer** | `04` |
| "median of two sorted arrays" | Binary search on partition | `04` |
| "top K", "K-th largest", "K closest" | Heap (O(n log k)) or quickselect | `10` |
| "merge K sorted …" | Min-heap of K heads | `10` |
| "median of a data stream" | Two heaps | `10` |
| "next greater / smaller element" | **Monotonic stack** | `08` |
| "largest rectangle", "trapping rain water", "stock span" | Monotonic stack | `08` |
| "maximum in every window of size k" | Monotonic deque | `08` |
| "valid parentheses", "evaluate expression", "undo" | Stack | `08` |
| "all permutations / combinations / subsets" | Backtracking | `06` |
| "place N items subject to constraints" (N-Queens, Sudoku) | Backtracking + pruning | `06` |
| "prefix", "autocomplete", "dictionary of words" | **Trie** | `11` |
| "maximum XOR pair/subarray" | Binary trie | `11` |
| grid + "shortest path", "number of islands", "rotting oranges" | BFS / DFS on grid | `12` |
| "shortest path, unweighted" | **BFS** | `12` |
| "shortest path, non-negative weights" | Dijkstra | `12` |
| "shortest path, negative weights" | Bellman-Ford | `12` |
| "all-pairs shortest path", n ≤ 400 | Floyd-Warshall | `12` |
| "prerequisites", "build order", "course schedule" | **Topological sort** | `12` |
| "connected components", "are x and y in the same group", "redundant connection" | **Union-Find** | `12` |
| "minimum cost to connect all" | MST (Kruskal / Prim) | `12` |
| "number of ways to…", "count the paths" | DP (counting) | `13` |
| "minimum/maximum cost to…" with overlapping choices | DP (optimisation) | `13` |
| "can we reach / partition / make exactly X" | Subset-sum DP | `13` |
| "longest increasing subsequence" | DP O(n²) or patience + binary search O(n log n) | `13` |
| two strings compared → edit/align/match | String DP (2-D) | `13` |
| "burst / merge / split on a range" | Interval DP | `13` |
| "visit all nodes exactly once", n ≤ 20 | Bitmask DP | `13` |
| "maximum profit with at most k transactions" | State-machine DP | `13` |
| "pick items to maximise X" and a local choice is provably safe | Greedy | `14` |
| "minimum number of X to cover / remove" on intervals | Greedy by end time | `16` |
| "merge overlapping", "insert interval", "meeting rooms" | Intervals / sweep line | `16` |
| "count of points covered at time t" | Sweep line with ±1 events | `16` |
| "appears once while others appear twice" | XOR | `15` |
| "count set bits", "power of two", "subsets via mask" | Bit manipulation | `15` |
| "design a cache / data structure with O(1) ops" | Hash map + DLL / heap | `17` |

---

## 3. Disambiguation — when two patterns both seem to fit

| Confusion | Resolution |
|---|---|
| Sliding window **or** two pointers? | Window = contiguous span with a constraint. Two pointers = converging from both ends, usually on sorted data. |
| Sliding window **or** DP? | Window works only when shrinking from the left is *safe* (the constraint is monotone). If a discarded element could be needed later, it is DP. |
| Greedy **or** DP? | Can you prove the local choice with an exchange argument in 30 seconds? Greedy. Otherwise DP — a correct slow answer beats an elegant wrong one. |
| BFS **or** Dijkstra? | All edge weights equal → BFS. Weights 0/1 → 0-1 BFS with a deque. Arbitrary non-negative → Dijkstra. |
| DFS **or** BFS? | Shortest path / level structure → BFS. Connectivity, cycles, topological order, "explore fully" → DFS. |
| Heap **or** sorting? | Need only the top k → heap, O(n log k). Need the whole order → sort. Need only the k-th → quickselect, O(n) average. |
| Hash map **or** sorted map? | Need ordering, floor/ceil, or range queries → tree map. Otherwise hash map. |
| Recursion **or** iteration? | n up to ~10⁵ with linear depth risks a stack overflow (Python defaults to 1000). Convert to an explicit stack. |
| Memoisation **or** tabulation? | Under time pressure, memoised recursion — it mirrors the recurrence and is harder to get wrong. Tabulate only when you need the space optimisation. |

---

## 4. The five-question triage, before writing any code

1. **What exactly is being asked?** Restate it in one sentence in your own words. (Catches the `misread` failure mode.)
2. **What do the constraints permit?** → intended complexity, from §1.
3. **What is the brute force, and what does it cost?** Always have one; it is your fallback submission and your starting point for the interview.
4. **Which symptom in §2 does this match?** Name the pattern aloud.
5. **What are the edge cases?** Empty, single element, all identical, all negative, duplicates, overflow, already sorted, n = 1.

Skipping step 1 is the single most common cause of a wasted OA question.
