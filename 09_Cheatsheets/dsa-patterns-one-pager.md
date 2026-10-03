
# DSA Patterns — Trigger → Pattern → Template

> **Use:** the 5 minutes before an OA, and the first 30 seconds of any coding interview.
> The skill this sheet trains is *recognition*, not implementation. Read the trigger column
> until the mapping is reflexive.

---

## 1. The 30-second triage ⭐⭐⭐

```
1. READ the constraints FIRST, before the problem statement makes sense.
   n ≤ 20 → bitmask.  n ≤ 500 → O(n³).  n ≤ 10⁵ → O(n log n).  n ≤ 10¹⁸ → binary search / math.
2. HAND-WORK the given example. The observation almost always lives there, not in the prose.
3. NAME the pattern out loud before writing code: "this is binary search on the answer".
4. STATE the complexity of your intended solution before implementing it.
5. If stuck after 2 minutes: brute force, state its complexity, then optimise. ⭐ Partial credit
   is real, and in interviews a stated brute force buys you a hint.
```

---

## 2. Trigger table ⭐⭐⭐

| You see this in the problem | Pattern | Complexity |
|---|---|---|
| Sorted array, find a pair/triplet | **Two pointers** | O(n) after sort |
| Contiguous subarray, fixed length k | **Fixed sliding window** | O(n) |
| Longest/shortest subarray satisfying P | **Variable sliding window** | O(n) |
| Subarray sum equals k (with negatives) | **Prefix sum + hash map** | O(n) |
| Range sum queries, no updates | **Prefix sums** | O(1) per query |
| Range sum queries **with** updates | **Fenwick / segment tree** | O(log n) |
| k largest / smallest / most frequent | **Heap of size k** | O(n log k) |
| Merge k sorted lists | **Min-heap of k heads** | O(N log k) |
| Median of a stream | **Two heaps** (max-heap low, min-heap high) | O(log n) insert |
| "Minimise the maximum" / "maximise the minimum" | **Binary search on the answer** ⭐ | O(n log range) |
| Monotone predicate, huge value range | **Binary search on the answer** | O(log range) |
| Next greater / smaller element | **Monotonic stack** | O(n) |
| Largest rectangle in histogram / in a matrix | **Monotonic stack** | O(n) |
| Sliding-window maximum | **Monotonic deque** | O(n) |
| Cycle in a linked list; find duplicate number | **Floyd's tortoise-and-hare** | O(n), O(1) space |
| Reverse / reorder a linked list in place | **Pointer manipulation, dummy head** | O(n) |
| k-th from the end, middle node | **Two pointers with a gap** | O(n) |
| All permutations / combinations / subsets | **Backtracking** | O(n!) / O(2ⁿ) |
| Place items under constraints (N-queens, sudoku) | **Backtracking + pruning** | exponential |
| Count paths / ways, overlapping subproblems | **DP** | see below |
| Optimal value, choices at each step, no greedy proof | **DP** | |
| Shortest path, unweighted | **BFS** | O(V+E) |
| Shortest path, weights ∈ {0,1} | **0-1 BFS (deque)** | O(V+E) |
| Shortest path, non-negative weights | **Dijkstra** | O((V+E) log V) |
| Shortest path, negative weights / cycle detection | **Bellman-Ford** | O(VE) |
| All-pairs shortest path, V ≤ 500 | **Floyd-Warshall** | O(V³) |
| Dependencies, ordering, "course schedule" | **Topological sort** | O(V+E) |
| Connected components, "are these two related?" | **Union-Find** or DFS | ~O(1) per op |
| Minimum cost to connect everything | **MST (Kruskal / Prim)** | O(E log E) |
| Grid, flood fill, islands, regions | **DFS / BFS on a grid** | O(R·C) |
| Grid, shortest number of steps | **BFS on a grid** | O(R·C) |
| Prefix / autocomplete / word dictionary | **Trie** | O(L) per op |
| Find a substring / pattern occurrences | **KMP / Z / rolling hash** | O(n+m) |
| Anagrams, character counts | **Frequency array / hash map** | O(n) |
| Intervals: merge, insert, min rooms | **Sort by start, then sweep** ⭐ | O(n log n) |
| "At most k" of something, optimise | **DP with k as a dimension**, or window | |
| n ≤ 20, subsets of a set | **Bitmask DP** | O(2ⁿ·n) |
| Tree, compute something for every node | **Tree DP** or **rerooting** | O(n) |
| Two strings, align / transform | **2-D DP** | O(n·m) |
| Exchange argument provable, local = global | **Greedy** | O(n log n) |
| Matrix rotate / spiral / transpose | **Index arithmetic, do it on paper first** | O(n²) |
| Stream, cannot store everything | **Reservoir sampling / heap / counters** | O(1) space-ish |
| LRU / LFU cache | **Hash map + doubly linked list** ⭐ | O(1) |

---

## 3. The templates to be able to write blind ⭐⭐

### Binary search on the answer
```cpp
// invariant: feasible(x) is monotone — false...false,true...true
long long lo = LOW, hi = HIGH, ans = -1;
while (lo <= hi) {
    long long mid = lo + (hi - lo) / 2;      // ⚠️ never (lo+hi)/2
    if (feasible(mid)) { ans = mid; hi = mid - 1; }   // minimising
    else                 lo = mid + 1;
}
```
> Write `feasible()` as a separate function. Half of all bugs here are an off-by-one in the
> boundary update, and a named predicate makes the monotonicity obvious.

### Variable sliding window
```cpp
int l = 0, best = 0;
for (int r = 0; r < n; ++r) {
    add(a[r]);
    while (!valid()) { remove(a[l]); ++l; }   // shrink until valid again
    best = max(best, r - l + 1);
}
```

### Monotonic stack (next greater element)
```cpp
stack<int> st;                        // holds indices, values decreasing
vector<int> nge(n, -1);
for (int i = 0; i < n; ++i) {
    while (!st.empty() && a[st.top()] < a[i]) { nge[st.top()] = i; st.pop(); }
    st.push(i);
}
```

### BFS on a grid
```cpp
int dr[] = {-1,1,0,0}, dc[] = {0,0,-1,1};
queue<pair<int,int>> q; q.push({sr,sc}); dist[sr][sc] = 0;
while (!q.empty()) {
    auto [r,c] = q.front(); q.pop();
    for (int k = 0; k < 4; ++k) {
        int nr = r+dr[k], nc = c+dc[k];
        if (nr<0||nr>=R||nc<0||nc>=C) continue;
        if (grid[nr][nc]=='#' || dist[nr][nc]!=-1) continue;
        dist[nr][nc] = dist[r][c] + 1; q.push({nr,nc});
    }
}
```

### Dijkstra
```cpp
priority_queue<pair<long long,int>, vector<pair<long long,int>>, greater<>> pq;
vector<long long> d(n, LLONG_MAX); d[s] = 0; pq.push({0,s});
while (!pq.empty()) {
    auto [du,u] = pq.top(); pq.pop();
    if (du > d[u]) continue;                  // ⭐ lazy deletion — the line people forget
    for (auto [v,w] : adj[u])
        if (d[u] + w < d[v]) { d[v] = d[u] + w; pq.push({d[v],v}); }
}
```

### Union-Find
```cpp
vector<int> p(n), r(n,0);
iota(p.begin(), p.end(), 0);
int find(int x){ return p[x]==x ? x : p[x]=find(p[x]); }      // path compression
bool uni(int a,int b){ a=find(a); b=find(b); if(a==b) return false;
    if(r[a]<r[b]) swap(a,b); p[b]=a; if(r[a]==r[b]) ++r[a]; return true; }
```

### Backtracking
```cpp
void rec(int i, vector<int>& cur) {
    if (i == n) { record(cur); return; }
    cur.push_back(a[i]); rec(i+1, cur); cur.pop_back();   // take
    rec(i+1, cur);                                        // skip
}
```

### Topological sort (Kahn)
```cpp
queue<int> q; for (int i=0;i<n;++i) if(!indeg[i]) q.push(i);
vector<int> order;
while(!q.empty()){ int u=q.front(); q.pop(); order.push_back(u);
    for(int v: adj[u]) if(--indeg[v]==0) q.push(v); }
bool has_cycle = order.size() != n;       // ⭐ free cycle detection
```

### Intervals (merge)
```cpp
sort(iv.begin(), iv.end());
vector<pair<int,int>> out;
for (auto [s,e] : iv)
    if (!out.empty() && s <= out.back().second) out.back().second = max(out.back().second, e);
    else out.push_back({s,e});
```

### LRU cache
```
hash map: key → list iterator        doubly linked list: most-recent at front
get(k)  : found → splice node to front, return value
put(k,v): exists → update + splice; else push_front, map insert;
          if size > cap → erase map[list.back().key], pop_back
```

---

## 4. DP recipe ⚠️ (your priority topic)

```
1. STATE      : what does dp[i] / dp[i][j] mean? Write the sentence down. If you cannot write
                the sentence, you do not have a state. ⭐
2. TRANSITION : dp[i] = f(dp[smaller things]). Enumerate the choices at step i.
3. BASE CASE  : the smallest subproblem, and the empty case.
4. ORDER      : iterate so that every dependency is already computed.
5. ANSWER     : which cell is it? Not always dp[n].
6. OPTIMISE   : can a dimension be dropped (rolling array)?
```

### The eight families — know one canonical problem for each
| Family | Canonical problem | State |
|---|---|---|
| 1-D linear | House robber, LIS, decode ways | `dp[i]` = best up to i |
| Knapsack | 0/1, subset sum, target sum | `dp[i][w]` = using first i, capacity w |
| 2-D / two sequences | Edit distance, LCS | `dp[i][j]` = prefixes of length i, j |
| Interval | Matrix chain, burst balloons | `dp[l][r]` = answer on [l,r] |
| Tree | Diameter, independent set | `dp[v][state]` = subtree of v |
| Bitmask | TSP, assignment | `dp[mask][i]` = visited mask, at i |
| Digit | Count numbers ≤ N with property | `dp[pos][state][tight]` |
| State machine | Stock buy/sell variants | `dp[i][holding][transactions]` |

---

## 5. Greedy — only when you can prove it ⚠️

```
Valid when: (a) an EXCHANGE ARGUMENT works — swapping any optimal choice for the greedy one
                does not make the solution worse; or
            (b) the problem has the matroid / optimal-substructure property.

Classic correct greedies: activity selection (earliest finish time) · Huffman coding ·
    fractional knapsack · Kruskal/Prim · Dijkstra · minimum platforms · gas station.

⚠️ Classic WRONG greedy: 0/1 knapsack by value/weight ratio.
    Capacity 10. Items (w6,v12), (w5,v9), (w5,v9). Ratios 2.0, 1.8, 1.8.
    Greedy takes item 1 → 12. Optimal takes items 2+3 → 18.
```

> **In an interview, never say "greedy works here" without a one-sentence exchange argument.**
> If you cannot produce one in 20 seconds, it is DP.

---

## 6. The traps that cost marks ⚠️

```
□ Integer overflow — use long long for products, prefix sums, and binary-search midpoints
□ (lo+hi)/2 overflow — always lo + (hi-lo)/2
□ Off-by-one in window shrink and in binary-search boundaries
□ Recursion depth — n = 10⁵ on a path graph overflows the stack; convert DFS to iterative
□ Dijkstra without the lazy-deletion `if (du > d[u]) continue;`
□ Modifying a container while iterating it
□ Forgetting mod in intermediate products: (a*b) % M with a,b < M still needs 64-bit
□ Coin change: loop order decides combinations vs permutations
□ n == 0, n == 1, all-equal elements, all-negative array, single-node tree, empty string
□ Hash map worst case O(n) — if the problem is adversarial, sort instead
□ Reading input slowly in C++ — ios::sync_with_stdio(false); cin.tie(nullptr);
```

---

## 7. What to say while coding ⭐

```
"The constraints are n ≤ 10⁵, so I need O(n log n) — that rules out the O(n²) DP."
"Let me hand-work the example before I commit to an approach."
"This is 'minimise the maximum', which is almost always binary search on the answer."
"Brute force is O(n²). Let me state that, then optimise with a monotonic stack."
"Edge cases: empty input, single element, all duplicates. Let me trace the first."
```

---

## Recall questions
1. "Minimise the largest sum among k subarrays" — which pattern, and why?
2. Which line do people omit from Dijkstra, and what breaks without it?
3. Give the 0/1-knapsack counter-example to ratio greedy, with numbers.
4. You need range sums *with* point updates. Prefix sums or Fenwick? Why?
5. Write the five steps of the DP recipe from memory.
6. `n ≤ 18` and the problem is about subsets. What is your first guess at the approach?
