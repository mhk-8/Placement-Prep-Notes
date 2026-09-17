# Graphs — Concepts

## 1. Core idea in 3 lines
A graph is just "things and connections", and most hard-looking OA problems are graphs in disguise — grids, dependency lists, word ladders, equation chains. Nearly all of them reduce to one of five algorithms: BFS, DFS, topological sort, Dijkstra, union-find. The skill is recognising the disguise and then asking the four classification questions before writing anything.

---

## 2. The four questions to ask first

1. **Directed or undirected?** Decides cycle detection, whether you add both edges, and whether topological sort applies.
2. **Weighted or unweighted?** Unweighted shortest path is BFS; weighted non-negative is Dijkstra; negative weights need Bellman-Ford.
3. **Cyclic or acyclic?** A DAG unlocks topological sort and DAG-DP; cycles require visited tracking.
4. **Connected?** Otherwise loop over all start vertices, or you will only explore one component.

Asking these aloud in an interview is itself worth marks.

---

## 3. Representations

| | Adjacency list | Adjacency matrix | Edge list |
|---|---|---|---|
| Space | O(V+E) | O(V²) | O(E) |
| Neighbours of u | O(deg u) | O(V) | O(E) |
| Is (u,v) an edge? | O(deg u) | **O(1)** | O(E) |
| Best for | sparse (almost always) | dense, Floyd-Warshall | Kruskal, union-find |

Default to an adjacency list, `defaultdict(list)`. A **grid is an implicit graph**: each cell is a vertex with up to four neighbours, and you never materialise the adjacency structure.

---

## 4. Traversal

**BFS** explores by distance from the source, so on an **unweighted** graph the first time you reach a vertex is via a shortest path. Queue-based, O(V+E). Use for: shortest path (unweighted), level-by-level processing, minimum number of steps/transformations.

**DFS** explores as deep as possible first. Recursive or explicit stack, O(V+E). Use for: connectivity, cycle detection, topological ordering, backtracking on graphs, "explore the whole region".

**Mark visited when you enqueue, not when you dequeue.** Marking at dequeue lets a vertex be enqueued many times before it is processed, which in the worst case blows up to O(V²).

**Multi-source BFS** starts with *all* sources already in the queue at distance 0. This computes, in one O(V+E) pass, the distance from every vertex to its *nearest* source. Rotting Oranges, 01 Matrix, Walls and Gates — all the same template, and doing them as V separate BFS runs is the common wrong answer.

**0-1 BFS** handles weights of only 0 or 1 using a deque: push 0-weight edges to the front, 1-weight edges to the back. O(V+E), beating Dijkstra's log factor.

---

## 5. Cycle detection

**Undirected:** DFS carrying the parent; a visited neighbour that is not the parent means a cycle. (With parallel edges, track the edge rather than the vertex.) Union-find also works: an edge joining two already-connected vertices closes a cycle.

**Directed:** three colours — white (unvisited), grey (on the current recursion stack), black (finished). Reaching a **grey** vertex is a back edge, hence a cycle. Reaching a black vertex is fine. Using a single "visited" set here is the classic error: it reports a cycle for a diamond-shaped DAG.

Kahn's algorithm gives the same answer: if the topological order contains fewer than V vertices, a cycle exists.

---

## 6. Topological sort

Only for a **DAG**. Produces an ordering where every edge points forwards.

**Kahn (BFS):** compute in-degrees, queue all zero-in-degree vertices, pop one, append it to the order, decrement its neighbours' in-degrees, enqueue any that hit zero. If the result is shorter than V, there is a cycle. O(V+E).

**DFS:** postorder, then reverse. Equivalent, but Kahn also detects cycles naturally and yields lexicographically-controllable orders if you use a heap instead of a queue.

Applications: Course Schedule I/II, build/dependency order, Alien Dictionary (derive edges from adjacent word pairs — and watch the invalid case where a word's prefix follows it), and DAG shortest/longest paths in O(V+E).

---

## 7. Shortest paths

| Algorithm | Handles | Time | Notes |
|---|---|---|---|
| BFS | unweighted | O(V+E) | first visit is optimal |
| 0-1 BFS | weights ∈ {0,1} | O(V+E) | deque |
| **Dijkstra** | non-negative weights | O((V+E) log V) | greedy + heap |
| **Bellman-Ford** | negative weights | O(V·E) | detects negative cycles |
| **Floyd-Warshall** | all pairs, V ≤ ~400 | O(V³) | DP over intermediate vertices |
| DAG DP | any weights, DAG only | O(V+E) | relax in topological order |

**Dijkstra's correctness rests on non-negativity:** once a vertex is popped with the minimum tentative distance, no later path can improve it, because adding edges never decreases the total. A negative edge breaks exactly that argument.

Implementation details that matter: use **lazy deletion** (`if d > dist[u]: continue`) rather than trying to decrease keys in the heap. Dijkstra also generalises — "maximum probability path" (multiply, take max), "minimum effort path" (minimise the maximum edge), "cheapest flights within k stops" (Bellman-Ford limited to k+1 relaxation rounds, because Dijkstra's greedy is wrong when a hop budget constrains the path).

**Floyd-Warshall** is three nested loops with **k outermost** — `dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])`. Putting k inside is a silently wrong answer.

---

## 8. Minimum spanning tree

**Kruskal:** sort edges by weight, add an edge if it joins two different components (union-find). O(E log E). Natural for edge lists and sparse graphs.
**Prim:** grow a tree from any vertex, always adding the cheapest edge leaving it (heap). O(E log V). Better on dense graphs.

Both are greedy and both are correct by the **cut property**: for any partition of the vertices, the lightest edge crossing the cut belongs to some MST.

Applications: Min Cost to Connect All Points, Connecting Cities With Minimum Cost, Optimize Water Distribution (with a virtual source vertex for the "build a well" option).

---

## 9. Union-Find (DSU)

Maintains disjoint sets under `find` and `union`. With **path compression** and **union by rank/size**, both are O(α(n)) — effectively constant.

Use for: dynamic connectivity, cycle detection in undirected graphs, Kruskal, counting components, Accounts Merge, Redundant Connection, Number of Islands II (islands appearing over time), and equation-consistency problems (Evaluate Division uses a weighted DSU).

Union-find cannot handle deletion of edges, and it gives you connectivity but not paths.

---

## 10. Advanced (P3)

**Tarjan / Kosaraju** for strongly connected components, **bridges** (an edge whose removal disconnects the graph: `low[v] > disc[u]`) and **articulation points**. Worth recognising; rarely required in an OA.

**Bipartite check:** 2-colour with BFS/DFS; a conflict means an odd cycle exists. Appears as "Possible Bipartition" and "Is Graph Bipartite".

---

## 11. Recall questions

1. What are the four classification questions?
2. Why mark visited at enqueue rather than dequeue?
3. What does multi-source BFS compute, and why is it one pass?
4. How does directed cycle detection differ from undirected, and why?
5. Describe Kahn's algorithm and how it detects a cycle.
6. Why does Dijkstra require non-negative weights?
7. When do you use Bellman-Ford instead of Dijkstra?
8. Why must `k` be the outermost loop in Floyd-Warshall?
9. State the cut property and name the two MST algorithms it justifies.
10. What are the two union-find optimisations and the resulting complexity?
