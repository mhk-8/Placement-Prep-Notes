
# DSA Round Playbook

> **Scope.** The *behaviour* is in `00-Interview_Protocol.md`; the *content* is in `../../01_DSA/`.
> This file is the bridge: what to expect at each company tier, which patterns to have automatic,
> and the specific things to prepare given your background.

---

## 1. What to expect, by company tier ⭐⭐

| Tier | Companies | Band | Rounds |
|---|---|---|---|
| **Hard algorithmic** | Google, Sprinklr, Media.net, Codenation, Rubrik | Medium-Hard; observation-driven | 3-4 coding |
| **Standard product** | Amazon, Microsoft, Flipkart, Walmart, Uber, Atlassian, Adobe | Medium; pattern recognition | 2-3 coding + 1 design |
| **Systems / HPC** ⭐ | NVIDIA, AMD, Qualcomm, Samsung R&D, Arcesium, Oracle (kernel/DB teams) | Medium + **systems depth** | Coding + C++/OS/architecture |
| **Quant** | Optiver, Tower, Quadeye, Graviton, DE Shaw | Medium-Hard + probability | Coding + probability + puzzles |
| **Service** | TCS, Infosys, Wipro, Accenture, Cognizant | Easy-Medium | 1 coding + fundamentals |

⭐ **Your profile points hardest at the systems/HPC tier.** CUDA plus compilers plus C++ is exactly
the combination NVIDIA, AMD, Qualcomm, Samsung R&D and database/kernel teams look for, and it is
rare on campus. Target those deliberately rather than treating every company as identical.

---

## 2. The pattern checklist ⭐⭐⭐

You should be able to **recognise the pattern within 60 seconds** for any Medium problem. Tick the
ones you can implement from memory, without reference, in under 10 minutes.

**Arrays and strings**
```
☐ Two pointers (opposite ends)          ☐ Sliding window (fixed and variable)
☐ Prefix sums / difference arrays       ☐ Kadane's maximum subarray
☐ Dutch national flag / 3-way partition ☐ Cyclic sort / index-as-hash
☐ Merge intervals                       ☐ Binary search on a sorted array
☐ Binary search ON THE ANSWER ⭐⭐        ☐ Monotonic stack (next greater, largest rectangle)
```

**Hashing and counting**
```
☐ Frequency map + top-K with a heap     ☐ Group anagrams / canonical keys
☐ Prefix-sum + hash map (subarray sum = k)  ⭐ the pattern people miss
☐ Sliding window with a character count
```

**Linked lists, stacks, queues**
```
☐ Reverse (iterative and in k-groups)   ☐ Floyd cycle detection
☐ Merge two / k sorted lists            ☐ LRU cache (hash map + doubly linked list) ⭐
☐ Min stack                             ☐ Queue via two stacks
☐ Monotonic deque (sliding window max)
```

**Trees**
```
☐ All four traversals, recursive AND iterative   ☐ Level order / BFS by level
☐ Validate BST                          ☐ LCA (with and without parent pointers)
☐ Diameter / max path sum               ☐ Serialise and deserialise
☐ Construct from inorder + preorder     ☐ Trie (insert, search, prefix)
```

**Graphs**
```
☐ BFS / DFS on grids and adjacency lists ☐ Topological sort (Kahn and DFS)
☐ Cycle detection (directed and undirected) ☐ Union-Find with path compression + union by rank
☐ Dijkstra                              ☐ Bellman-Ford (and when Dijkstra fails)
☐ MST (Prim, Kruskal)                   ☐ Bipartite check / 2-colouring
☐ Connected components, flood fill      ☐ 0-1 BFS with a deque
```
⭐ You have a genuine advantage here — you have implemented Δ-stepping SSSP and worked with CSR.
Say so when a graph question comes up; it changes the interviewer's model of you.

**Dynamic programming**
```
☐ 1-D: climbing stairs, house robber, coin change, LIS
☐ Knapsack family: 0/1, unbounded, subset sum, partition equal subset
☐ 2-D: edit distance, LCS, grid paths with obstacles
☐ Interval DP: matrix chain, burst balloons
☐ DP on trees                           ☐ Bitmask DP (n ≤ 20)
☐ State-machine DP: stock problems (all five variants) ⭐
```

**Heaps, greedy, bit manipulation**
```
☐ Top-K / K closest / merge K sorted    ☐ Running median (two heaps) ⭐
☐ Interval scheduling, minimum platforms ☐ Task scheduler
☐ XOR tricks (single number)            ☐ Count set bits, subsets via bitmask
```

---

## 3. The five templates to have literally memorised ⭐⭐⭐

If you can write these five without thinking, a large fraction of Medium problems reduce to
filling in a condition.

```python
# 1. BINARY SEARCH ON THE ANSWER  — "minimise the maximum" / "maximise the minimum"
def solve(lo, hi):
    while lo < hi:
        mid = lo + (hi - lo) // 2          # avoids overflow in C++
        if feasible(mid): hi = mid         # mid works, try smaller
        else:             lo = mid + 1
    return lo

# 2. VARIABLE-SIZE SLIDING WINDOW
left = 0; best = 0
for right in range(n):
    add(arr[right])
    while not valid():                     # shrink until the window is legal again
        remove(arr[left]); left += 1
    best = max(best, right - left + 1)

# 3. BFS ON A GRID
from collections import deque
q = deque([(sr, sc)]); seen = {(sr, sc)}; dist = 0
while q:
    for _ in range(len(q)):                # process one LEVEL at a time
        r, c = q.popleft()
        for dr, dc in ((1,0),(-1,0),(0,1),(0,-1)):
            nr, nc = r+dr, c+dc
            if 0 <= nr < R and 0 <= nc < C and (nr,nc) not in seen and grid[nr][nc] != '#':
                seen.add((nr,nc)); q.append((nr,nc))
    dist += 1

# 4. UNION-FIND
parent = list(range(n)); rank = [0]*n
def find(x):
    while parent[x] != x:
        parent[x] = parent[parent[x]]      # path halving
        x = parent[x]
    return x
def union(a, b):
    ra, rb = find(a), find(b)
    if ra == rb: return False
    if rank[ra] < rank[rb]: ra, rb = rb, ra
    parent[rb] = ra
    if rank[ra] == rank[rb]: rank[ra] += 1
    return True

# 5. DIJKSTRA
import heapq
dist = [float('inf')]*n; dist[src] = 0
pq = [(0, src)]
while pq:
    d, u = heapq.heappop(pq)
    if d > dist[u]: continue               # stale entry — skip  ⭐ the line people forget
    for v, w in adj[u]:
        if d + w < dist[v]:
            dist[v] = d + w
            heapq.heappush(pq, (dist[v], v))
```

---

## 4. Language choice for you ⭐⭐

Your resume lists **C++, CUDA C++, C, Java, Python**. In an interview:

```
C++    : your natural choice for systems/HPC companies, and it signals depth for those roles.
         Use vector, unordered_map, sort, priority_queue, range-for. Do NOT hand-roll memory
         management you do not need — interviewers read raw new/delete as a risk, not a skill.
Python : faster to write, fewer bugs under pressure, and nobody penalises it for DSA rounds.
         Good default for pure algorithm rounds at product companies.
```
⚠️ **Do not switch languages mid-interview**, and do not pick C++ to look impressive if you will be
slower in it. State your choice at the start: *"I'll use C++ — shall I assume the standard library
is available?"*

**C++ gotchas that cost marks under pressure:**
```
- Integer overflow: use long long for sums and products of large ints  ⭐
- mid = (lo + hi) / 2 overflows; use lo + (hi - lo) / 2
- unordered_map has O(n) worst case; map is O(log n) guaranteed
- Passing a large vector by value copies it — pass const& (interviewers notice)
- Iterator invalidation when erasing inside a loop
```

---

## 5. Your specific preparation gaps ⚠️⭐⭐

An honest read of your resume against a DSA round:

| Strength | Why it helps |
|---|---|
| Graph algorithms | You implemented Δ-stepping and CSR by hand — deeper than most candidates |
| C++ and systems thinking | Natural fit for complexity and memory-layout discussion |
| TA for CS5800 (Advanced DS&A) ⭐ | You teach this material. Use it: *"I TA the advanced DS&A course, so I see the common failure modes"* is a strong, true line |

| Gap to close | Why it matters |
|---|---|
| **Dynamic programming** | Nothing on your resume exercises DP. It is the most-asked Medium-Hard topic, and interviewers will find the gap. **Prioritise this.** ⭐⭐⭐ |
| **String algorithms** | KMP, Z-algorithm, hashing — absent from your projects |
| **Raw interview volume under time pressure** | Your projects are deep and slow-burn; DSA rounds are 20-minute sprints. Different muscle |
| **Live coding without an IDE** | You work in VS Code with a compiler; interview editors have neither |

**Recommended split for the next six weeks:**
```
40%  Dynamic programming  — the gap
25%  Graphs and trees     — consolidate the strength
20%  Arrays/strings/hashing — volume and speed
15%  Mixed timed mocks
```

---

## 6. The 45-minute round, time-boxed ⭐⭐

```
0:00 - 0:05   Introduction, possibly one resume question
0:05 - 0:08   Problem statement + your clarifying questions
0:08 - 0:12   Examples worked by hand; brute force stated with complexity
0:12 - 0:18   Optimised approach; AGREE IT with the interviewer before coding  ⭐
0:18 - 0:33   Code it, narrating
0:33 - 0:38   Dry run with a variable table; state complexity
0:38 - 0:45   Follow-up question or variation; your questions for them
```

⚠️ **The hard rule: if it is 0:20 and you have no approach, write the brute force.** Working code
that is suboptimal scores far above elegant code that does not exist. You can then optimise if time
allows, which also demonstrates exactly the progression interviewers want to see.

---

## 7. Follow-up variations to expect ⭐

Interviewers rarely stop at the first solution. The standard escalations:

```
"What if the input doesn't fit in memory?"       → streaming, external sort, sampling
"What if it's a stream and you need it online?"  → running statistics, two heaps, reservoir
"What if there are concurrent readers/writers?"  → locking, or an immutable/CoW structure
"What if you had to do this a million times?"    → precompute, cache, index
"Can you do it in O(1) space?"                   → in-place, pointer tricks, XOR
"What if k is very large / very small?"          → changes heap vs sort vs quickselect
"Can you parallelise it?"  ⭐                     → YOUR QUESTION. Answer with real substance:
                                                    what's the dependency, what's the frontier,
                                                    where's the contention
```

⭐ **That last one is your home ground.** Most candidates give a vague "use threads". You can
discuss the actual dependency structure, work partitioning and synchronisation cost. If a graph or
array problem comes up and the interviewer asks about parallelising it, that is a chance to be
memorable — take it.

---

## 8. The week-before routine

```
Daily   : 3 problems — 1 easy (warm-up), 2 medium, all timed at 25 minutes each
Daily   : re-write one of the five templates from memory
3× week : one problem solved ALOUD and recorded (see 00-Interview_Protocol §12)
2× week : one mock with a peer, alternating interviewer and candidate ⭐
          — being the interviewer teaches you more than being the candidate
Weekly  : review the mistake log; re-solve anything you failed twice
```

---

## Recall questions

1. Which company tier fits your profile best, and why?
2. Write the binary-search-on-the-answer template from memory.
3. What is the hard rule at the 20-minute mark?
4. Name your two biggest DSA gaps and the recommended time split.
5. What is the one follow-up question you are unusually well placed to answer?
6. Which line do people forget in Dijkstra, and what does it do?
