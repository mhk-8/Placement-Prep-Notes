# Graphs — Pitfalls

## Visited handling
- **Marking visited at dequeue instead of enqueue.** A vertex can then be enqueued many times before processing, degrading BFS to O(V²) and, in grid problems, producing wrong distances.
- Sharing one `visited` set across searches that should be independent (per-path DFS, or per-source runs).
- In grid problems, forgetting to mark the *source* cell before the loop starts.
- Not resetting state between components.

## Directedness
- Adding both `adj[u].append(v)` and `adj[v].append(u)` for a **directed** graph, or only one for an undirected one.
- Using the undirected parent trick for directed cycle detection. Directed graphs need the grey/black distinction; a plain `visited` set falsely reports a cycle for a diamond DAG.
- Forgetting that a self-loop is a cycle in both cases.

## Connectivity
- Running BFS/DFS only from vertex 0 and missing other components. Loop over all vertices.
- Assuming a graph with n vertices and n−1 edges is a tree — it is only a tree if it is also connected and acyclic.
- Forgetting isolated vertices when counting components.

## Topological sort
- Applying it to a graph with cycles and not checking `len(order) == n`.
- Building in-degrees from the wrong direction.
- Alien Dictionary: missing the invalid case where a longer word precedes its own prefix (`["abc", "ab"]`), which has no valid ordering.
- Enqueuing a vertex before its in-degree actually reaches zero.

## Dijkstra
- Using it with **negative** edge weights — the greedy invariant fails silently and returns wrong distances.
- Omitting the `if d > dist[u]: continue` stale check, which degrades performance and can produce incorrect results with some update patterns.
- Using it for "shortest path with at most k edges" — the hop constraint breaks optimal substructure; use Bellman-Ford with k+1 rounds.
- Pushing `(node, dist)` instead of `(dist, node)`, so the heap orders by node id.

## Bellman-Ford / Floyd-Warshall
- Relaxing in-place within a round for the k-stops variant, letting a path use more than k edges. Relax from a snapshot of the previous round.
- Putting `k` in the inner loop of Floyd-Warshall. It must be the **outermost** loop.
- Missing the V-th round that detects a negative cycle.

## Union-Find
- Omitting path compression or union by rank/size, making it O(n) per operation in the worst case.
- Unioning without checking whether the roots are already equal, so the component count is wrong.
- Comparing `par[a] == par[b]` instead of `find(a) == find(b)`.
- Trying to *delete* edges — DSU cannot do that.

## Grids
- Missing bounds checks before indexing.
- Using 4 directions where 8 are required, or vice versa.
- Mixing up `(row, col)` and `(x, y)` between the direction array and the indexing.
- Mutating the input grid when the caller needs it intact.

## Recursion
- Recursive DFS on a 10⁵-vertex path graph — stack overflow in both Python and C++. Use the iterative form or Kahn.
