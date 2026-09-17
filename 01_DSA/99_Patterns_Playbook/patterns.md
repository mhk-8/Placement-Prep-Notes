# The 22 Patterns — Trigger → Template

> One screen per pattern family. The full templates are in `01_DSA/templates.md`; this is the recall layer.

---

### 1. Prefix sums
**Trigger:** many range-sum queries, no updates · "sum of subarray"
**Core:** `pre[i+1] = pre[i] + a[i]`; `sum(l..r) = pre[r+1] - pre[l]`
**Cost:** O(n) build, O(1) query · **Folder:** `01`

### 2. Difference array
**Trigger:** many range *updates*, one final read
**Core:** `diff[l] += v; diff[r+1] -= v`, then prefix-sum once
**Cost:** O(1) per update · **Folder:** `01`

### 3. Kadane
**Trigger:** "maximum sum contiguous subarray"
**Core:** `cur = max(x, cur + x); best = max(best, cur)` — init from `a[0]`
**Cost:** O(n) · **Folder:** `01`

### 4. Hash map — seen / count / complement
**Trigger:** "has this appeared", "how many times", "find the pair summing to X"
**Core:** check before inserting
**Cost:** O(n) average · **Folder:** `02`

### 5. Prefix sum + hash map
**Trigger:** "count subarrays with sum k", **negatives allowed**
**Core:** `cnt += seen[run - k]; seen[run] += 1`, seeded with `seen[0] = 1`
**Cost:** O(n) · **Folder:** `02`

### 6. Two pointers (converging)
**Trigger:** **sorted** array, find a pair/triplet
**Core:** move the pointer that cannot improve the objective
**Cost:** O(n) after sorting · **Folder:** `03`

### 7. Sliding window
**Trigger:** "contiguous subarray/substring" + a constraint
**Core:** extend right; shrink left while invalid (longest) or while valid (shortest)
**Extra:** `exactly(K) = atMost(K) − atMost(K−1)`; `res += right − left + 1` counts windows
**Cost:** O(n) · **Folder:** `03`

### 8. Fast/slow pointers
**Trigger:** linked list cycle, middle, n-th from end
**Core:** `slow += 1; fast += 2`; reset to head to find the cycle entry
**Cost:** O(n), O(1) space · **Folder:** `07`

### 9. Binary search (lower_bound form)
**Trigger:** sorted + "find", "first ≥ x", "insertion position"
**Core:** `while lo < hi: if pred(mid): hi = mid else: lo = mid + 1`
**Cost:** O(log n) · **Folder:** `04`

### 10. Binary search on the answer
**Trigger:** **"minimum possible maximum"**, "maximum possible minimum", "smallest X such that…"
**Core:** range of answers + a monotone `feasible(x)` greedy check
**Cost:** O(n log range) · **Folder:** `04`

### 11. Sort + greedy
**Trigger:** intervals, scheduling, pairing
**Core:** sort by **end** to schedule, by **start** to merge
**Cost:** O(n log n) · **Folders:** `14`, `16`

### 12. Sweep line
**Trigger:** "how many are active at once", overlaps, skyline
**Core:** ±1 events, sort, running counter; tie-break decides touching
**Cost:** O(n log n) · **Folder:** `16`

### 13. Monotonic stack
**Trigger:** "next/previous greater or smaller", histogram, rain water, stock span
**Core:** stack of **indices**; pop while the order is violated
**Cost:** O(n) amortised · **Folder:** `08`

### 14. Monotonic deque
**Trigger:** "maximum/minimum in every window of size k"
**Core:** deque of indices, values decreasing; pop the back while ≤, pop the front when out of window
**Cost:** O(n) · **Folder:** `08`

### 15. Heap / top-K
**Trigger:** "top k", "k-th largest", "merge k streams", "always take the best next"
**Core:** **min**-heap of size k for the k largest; heap of heads to merge
**Cost:** O(n log k) · **Folder:** `10`

### 16. Two heaps
**Trigger:** running median
**Core:** max-heap for the low half, min-heap for the high half, sizes within 1
**Cost:** O(log n) insert · **Folder:** `10`

### 17. Backtracking
**Trigger:** "all subsets/permutations/combinations", constraint placement, **n ≤ 20**
**Core:** choose → explore → un-choose; `res.append(path[:])`
**Cost:** O(n·2ⁿ) or O(n·n!) · **Folder:** `06`

### 18. BFS
**Trigger:** shortest path **unweighted**, level processing, minimum steps, grids
**Core:** queue; mark visited on **enqueue**; multi-source = seed all at 0
**Cost:** O(V+E) · **Folder:** `12`

### 19. DFS / cycle detection
**Trigger:** connectivity, components, "explore the region", cycles
**Core:** undirected → track the parent; directed → grey/black colouring
**Cost:** O(V+E) · **Folder:** `12`

### 20. Topological sort
**Trigger:** prerequisites, build order, dependency chains, "is it possible to finish"
**Core:** Kahn — in-degrees, queue the zeros; `len(order) < n` means a cycle
**Cost:** O(V+E) · **Folder:** `12`

### 21. Dijkstra / Union-Find / MST
**Trigger:** weighted shortest path · dynamic connectivity · minimum connection cost
**Core:** heap with lazy deletion · path compression + union by size · Kruskal or Prim
**Cost:** O(E log V) · ~O(1) · O(E log E) · **Folder:** `12`

### 22. Dynamic programming
**Trigger:** "number of ways", "min/max cost", "can you make exactly", choices with consequences
**Core:** state → transition → base case → order → answer location
**Key distinctions:** 0/1 knapsack loops capacity **descending**, unbounded **ascending**; coins outer = combinations, target outer = permutations
**Cost:** states × work per state · **Folder:** `13`

---

## Tiebreakers

| Confusion | Rule |
|---|---|
| Window or two pointers? | Window = contiguous span + constraint. Two pointers = converging on sorted data. |
| Window or DP? | Window needs shrinking-from-the-left to be safe. Negatives or non-contiguity → DP. |
| Greedy or DP? | Exchange argument in 30 seconds → greedy. Otherwise DP. |
| BFS or Dijkstra? | Equal weights → BFS. 0/1 → deque BFS. Arbitrary non-negative → Dijkstra. |
| Heap or sort? | Only the top k → heap, O(n log k). Whole order → sort. |
| Memo or tabulate? | Under time pressure, memoise. |
| Fenwick or segment tree? | Sums only → Fenwick (shorter). Min/max or range updates → segment tree. |
