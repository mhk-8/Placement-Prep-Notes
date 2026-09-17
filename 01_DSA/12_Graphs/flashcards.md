# Graphs — Flashcards

## Questions

1. What four questions do you ask before choosing a graph algorithm?
2. Why mark vertices visited at enqueue rather than at dequeue?
3. What does multi-source BFS compute and why does one pass suffice?
4. How do you detect a cycle in an undirected graph? In a directed one?
5. Why does a plain `visited` set fail for directed cycle detection?
6. Describe Kahn's algorithm and how it reports a cycle.
7. What is the complexity of BFS, Dijkstra, Bellman-Ford and Floyd-Warshall?
8. Why does Dijkstra require non-negative weights?
9. Why can Dijkstra not solve "cheapest path with at most k stops"?
10. Why must `k` be the outermost loop in Floyd-Warshall?
11. State the cut property.
12. Compare Kruskal and Prim: complexity and when each is preferable.
13. What are the two union-find optimisations and the resulting per-operation cost?
14. When is 0-1 BFS applicable and why is it better than Dijkstra there?
15. How do you check whether a graph is bipartite, and what does failure imply?

---

## Answers

1. Directed or undirected? Weighted or unweighted? Cyclic or acyclic? Connected or not?
2. Marking at dequeue allows the same vertex to be enqueued repeatedly before it is processed, inflating the queue to O(V²) work and breaking distance correctness.
3. The distance from every vertex to its **nearest** source. Seeding the queue with all sources at distance 0 makes the BFS frontier expand from all of them simultaneously, so one O(V+E) pass suffices.
4. Undirected: DFS tracking the parent — a visited non-parent neighbour is a cycle (or union-find: an edge joining two already-connected vertices). Directed: three colours, where reaching a **grey** (on-stack) vertex is a back edge.
5. Because reaching an already-finished vertex by a different path is legitimate in a DAG; only reaching a vertex still on the current recursion stack proves a cycle.
6. Queue all zero-in-degree vertices; pop, append to the order, decrement neighbours' in-degrees and enqueue those reaching zero. If the order has fewer than V vertices, a cycle exists.
7. BFS/DFS O(V+E); Dijkstra O((V+E) log V) with a binary heap; Bellman-Ford O(V·E); Floyd-Warshall O(V³).
8. Its greedy step finalises a vertex when it is popped with the minimum tentative distance, relying on the fact that extending a path can never shorten it. A negative edge violates exactly that.
9. The hop limit destroys optimal substructure — a longer-distance path with fewer hops may be the only feasible one. Bellman-Ford limited to k+1 relaxation rounds handles it.
10. `k` indexes the set of allowed intermediate vertices; the DP requires all paths using intermediates {0..k−1} to be final before k is admitted. An inner `k` computes garbage.
11. For any partition of the vertices into two non-empty sets, the minimum-weight edge crossing the partition belongs to some minimum spanning tree.
12. Kruskal O(E log E) with union-find, natural for edge lists and sparse graphs. Prim O(E log V) with a heap, better on dense graphs.
13. Path compression and union by rank (or size), giving O(α(n)) amortised — effectively constant.
14. When every edge weight is 0 or 1. A deque with front-insertion for 0-edges keeps the queue monotone in distance, giving O(V+E) instead of Dijkstra's O(E log V).
15. Two-colour it with BFS or DFS; a conflict between adjacent vertices means it is not bipartite, which is equivalent to the graph containing an odd-length cycle.
