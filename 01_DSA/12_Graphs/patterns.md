# Graphs — Templates

## 0. Building the graph
```python
from collections import defaultdict, deque
adj = defaultdict(list)
for u, v in edges:
    adj[u].append(v)
    adj[v].append(u)          # undirected ONLY
```

## 1. BFS — unweighted shortest path
```python
def bfs(adj, src):
    dist = {src: 0}
    q = deque([src])
    while q:
        u = q.popleft()
        for v in adj[u]:
            if v not in dist:            # mark on ENQUEUE
                dist[v] = dist[u] + 1
                q.append(v)
    return dist
```

## 2. DFS — recursive and iterative
```python
def dfs(u, seen):
    seen.add(u)
    for v in adj[u]:
        if v not in seen: dfs(v, seen)

def dfs_iter(src):
    st, seen = [src], {src}
    while st:
        u = st.pop()
        for v in adj[u]:
            if v not in seen: seen.add(v); st.append(v)
```

## 3. Grid BFS / DFS
```python
DIRS = ((1,0), (-1,0), (0,1), (0,-1))

def num_islands(grid):
    R, C, count = len(grid), len(grid[0]), 0
    for r in range(R):
        for c in range(C):
            if grid[r][c] != '1': continue
            count += 1
            q = deque([(r, c)]); grid[r][c] = '0'       # sink on enqueue
            while q:
                x, y = q.popleft()
                for dx, dy in DIRS:
                    nx, ny = x + dx, y + dy
                    if 0 <= nx < R and 0 <= ny < C and grid[nx][ny] == '1':
                        grid[nx][ny] = '0'
                        q.append((nx, ny))
    return count
```

## 4. Multi-source BFS (Rotting Oranges / 01 Matrix)
```python
q = deque()
dist = [[-1]*C for _ in range(R)]
for r in range(R):
    for c in range(C):
        if grid[r][c] == SOURCE:
            dist[r][c] = 0; q.append((r, c))      # ALL sources start at 0
while q:
    x, y = q.popleft()
    for dx, dy in DIRS:
        nx, ny = x + dx, y + dy
        if 0 <= nx < R and 0 <= ny < C and dist[nx][ny] == -1 and passable(nx, ny):
            dist[nx][ny] = dist[x][y] + 1
            q.append((nx, ny))
```

## 5. Cycle detection
```python
# Undirected — DFS with parent
def has_cycle_undirected(n, adj):
    seen = set()
    def go(u, parent):
        seen.add(u)
        for v in adj[u]:
            if v == parent: continue
            if v in seen or go(v, u): return True
        return False
    return any(u not in seen and go(u, -1) for u in range(n))

# Directed — three colours
WHITE, GREY, BLACK = 0, 1, 2
def has_cycle_directed(n, adj):
    color = [WHITE] * n
    def go(u):
        color[u] = GREY
        for v in adj[u]:
            if color[v] == GREY: return True          # back edge
            if color[v] == WHITE and go(v): return True
        color[u] = BLACK
        return False
    return any(color[u] == WHITE and go(u) for u in range(n))
```

## 6. Topological sort — Kahn
```python
def topo_sort(n, adj):
    indeg = [0] * n
    for u in range(n):
        for v in adj[u]: indeg[v] += 1
    q = deque(u for u in range(n) if indeg[u] == 0)
    order = []
    while q:
        u = q.popleft(); order.append(u)
        for v in adj[u]:
            indeg[v] -= 1
            if indeg[v] == 0: q.append(v)
    return order if len(order) == n else []          # [] means a cycle
```
Use a **heap** instead of a deque for the lexicographically smallest valid order.

## 7. Dijkstra
```python
import heapq
def dijkstra(n, adj, src):                # adj[u] = [(v, w), ...]
    INF = float('inf')
    dist = [INF] * n; dist[src] = 0
    pq = [(0, src)]
    while pq:
        d, u = heapq.heappop(pq)
        if d > dist[u]: continue           # stale entry — lazy deletion
        for v, w in adj[u]:
            nd = d + w
            if nd < dist[v]:
                dist[v] = nd
                heapq.heappush(pq, (nd, v))
    return dist
```

## 8. Bellman-Ford (negative weights, negative-cycle detection)
```python
def bellman_ford(n, edges, src):
    INF = float('inf')
    dist = [INF] * n; dist[src] = 0
    for _ in range(n - 1):                 # V-1 rounds
        changed = False
        for u, v, w in edges:
            if dist[u] + w < dist[v]:
                dist[v] = dist[u] + w; changed = True
        if not changed: break
    for u, v, w in edges:                  # one more round → negative cycle
        if dist[u] + w < dist[v]: return None
    return dist
```
**Cheapest Flights Within K Stops** = Bellman-Ford limited to `k+1` rounds, relaxing from a *snapshot* of the previous round's distances.

## 9. Floyd-Warshall
```python
dist = [[INF]*n for _ in range(n)]
for i in range(n): dist[i][i] = 0
for u, v, w in edges: dist[u][v] = min(dist[u][v], w)
for k in range(n):                          # k MUST be outermost
    for i in range(n):
        for j in range(n):
            if dist[i][k] + dist[k][j] < dist[i][j]:
                dist[i][j] = dist[i][k] + dist[k][j]
```

## 10. Union-Find
```python
class DSU:
    def __init__(self, n):
        self.par = list(range(n))
        self.sz = [1] * n
        self.count = n
    def find(self, x):
        while self.par[x] != x:
            self.par[x] = self.par[self.par[x]]     # path halving
            x = self.par[x]
        return x
    def union(self, a, b):
        ra, rb = self.find(a), self.find(b)
        if ra == rb: return False                    # already connected
        if self.sz[ra] < self.sz[rb]: ra, rb = rb, ra
        self.par[rb] = ra; self.sz[ra] += self.sz[rb]
        self.count -= 1
        return True
```

## 11. Kruskal MST
```python
def kruskal(n, edges):                       # edges: (w, u, v)
    dsu = DSU(n); edges.sort()
    total, used = 0, 0
    for w, u, v in edges:
        if dsu.union(u, v):
            total += w; used += 1
            if used == n - 1: break
    return total if used == n - 1 else -1     # -1: graph is disconnected
```

## 12. Prim MST
```python
def prim(n, adj):
    visited = [False]*n; pq = [(0, 0)]; total = 0; cnt = 0
    while pq and cnt < n:
        w, u = heapq.heappop(pq)
        if visited[u]: continue
        visited[u] = True; total += w; cnt += 1
        for v, wt in adj[u]:
            if not visited[v]: heapq.heappush(pq, (wt, v))
    return total if cnt == n else -1
```

## 13. Bipartite check
```python
def is_bipartite(n, adj):
    color = [0] * n                           # 0 unvisited, 1 / -1 the two sides
    for s in range(n):
        if color[s]: continue
        color[s] = 1; q = deque([s])
        while q:
            u = q.popleft()
            for v in adj[u]:
                if color[v] == color[u]: return False
                if not color[v]: color[v] = -color[u]; q.append(v)
    return True
```

## 14. 0-1 BFS
```python
dq = deque([src]); dist = [INF]*n; dist[src] = 0
while dq:
    u = dq.popleft()
    for v, w in adj[u]:
        if dist[u] + w < dist[v]:
            dist[v] = dist[u] + w
            (dq.appendleft if w == 0 else dq.append)(v)
```

## C++ notes
- `vector<vector<pair<int,int>>> adj(n);` for weighted graphs.
- `priority_queue<pair<int,int>, vector<pair<int,int>>, greater<>> pq;` for Dijkstra's min-heap.
- Recursive DFS on 10⁵ vertices can overflow the stack — prefer the iterative form or Kahn.
