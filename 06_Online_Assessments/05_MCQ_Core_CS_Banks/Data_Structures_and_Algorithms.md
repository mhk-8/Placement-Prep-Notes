
# Data Structures and Algorithms — MCQ Bank

> **Why it matters.** Almost every technical MCQ section includes 5-10 questions on complexity,
> data-structure properties and algorithm behaviour. These are pure recall and are the cheapest
> marks in the paper.
>
> For the *coding* side of DSA, see `../../01_DSA/` in this vault. This file is the **MCQ layer**.

---

# Part 1 — The reference tables

## 1.1 Complexity of common operations ⭐⭐⭐

| Structure | Access | Search | Insert | Delete | Notes |
|---|---|---|---|---|---|
| Array | `O(1)` | `O(n)` | `O(n)` | `O(n)` | Contiguous; cache-friendly |
| Sorted array | `O(1)` | `O(log n)` | `O(n)` | `O(n)` | Binary search |
| Dynamic array | `O(1)` | `O(n)` | `O(1)` **amortised** | `O(n)` | Doubling on resize ⭐ |
| Singly linked list | `O(n)` | `O(n)` | `O(1)` at head | `O(1)` given the node's predecessor | No random access |
| Doubly linked list | `O(n)` | `O(n)` | `O(1)` | `O(1)` given the node | |
| Stack | — | — | `O(1)` | `O(1)` | LIFO |
| Queue | — | — | `O(1)` | `O(1)` | FIFO |
| Hash table | — | `O(1)` avg, `O(n)` worst ⚠️ | `O(1)` avg | `O(1)` avg | Worst case on total collision |
| BST (unbalanced) | `O(n)` worst | `O(n)` worst | `O(n)` | `O(n)` | Degenerates to a list ⚠️ |
| Balanced BST (AVL/RB) | `O(log n)` | `O(log n)` | `O(log n)` | `O(log n)` | Guaranteed |
| Binary heap | `O(1)` for min/max | `O(n)` | `O(log n)` | `O(log n)` | Not searchable ⭐ |
| Trie | — | `O(L)` | `O(L)` | `O(L)` | `L` = key length, independent of `n` ⭐ |
| B / B+ tree | `O(log n)` | `O(log n)` | `O(log n)` | `O(log n)` | Disk-oriented, high fan-out |

## 1.2 Sorting algorithms ⭐⭐⭐

| Algorithm | Best | Average | Worst | Space | Stable? | In-place? |
|---|---|---|---|---|---|---|
| Bubble | `O(n)`* | `O(n²)` | `O(n²)` | `O(1)` | ✅ | ✅ |
| Selection | `O(n²)` | `O(n²)` | `O(n²)` | `O(1)` | ❌ ⚠️ | ✅ |
| Insertion | `O(n)` | `O(n²)` | `O(n²)` | `O(1)` | ✅ | ✅ |
| **Merge** | `O(n log n)` | `O(n log n)` | `O(n log n)` | **`O(n)`** ⚠️ | ✅ | ❌ |
| **Quick** | `O(n log n)` | `O(n log n)` | **`O(n²)`** ⚠️ | `O(log n)` | ❌ | ✅ |
| **Heap** | `O(n log n)` | `O(n log n)` | `O(n log n)` | `O(1)` | ❌ | ✅ |
| Counting | `O(n+k)` | `O(n+k)` | `O(n+k)` | `O(k)` | ✅ | ❌ |
| Radix | `O(nk)` | `O(nk)` | `O(nk)` | `O(n+b)` | ✅ | ❌ |
| Bucket | `O(n+k)` | `O(n+k)` | `O(n²)` | `O(n)` | ✅ | ❌ |

`*` with an early-exit swapped flag.

⭐ **The facts most often asked:**
- **Quicksort's worst case is `O(n²)`** — on already-sorted input with a naive first/last pivot.
  Randomised or median-of-three pivoting makes it vanishingly unlikely.
- **Merge sort is stable and guaranteed `O(n log n)`** but needs `O(n)` extra space.
- **Heap sort is `O(n log n)` in all cases and in-place, but not stable.**
- **Selection sort is the one simple sort that is NOT stable.** ⚠️
- The comparison-sort **lower bound is `Ω(n log n)`** (decision-tree argument: `n!` leaves need
  depth `log₂(n!) = Θ(n log n)`). Counting/radix/bucket beat it only because they are not
  comparison sorts. ⭐⭐

## 1.3 Tree facts ⭐⭐

```
Binary tree with n nodes         : height ranges from ⌊log₂n⌋ (complete) to n−1 (skewed)
Complete binary tree, n nodes    : height = ⌊log₂ n⌋
Full binary tree                 : every node has 0 or 2 children
Perfect binary tree of height h  : 2^(h+1) − 1 nodes;  2^h leaves
Max nodes at level l             : 2^l  (root at level 0)
Leaf count vs degree-2 count     : L = D2 + 1   ⭐ (in a binary tree)
n-node binary trees (shapes)     : Catalan number  C(2n, n)/(n+1)

TRAVERSALS:
  Inorder   (L, Root, R)  → on a BST yields SORTED order ⭐⭐
  Preorder  (Root, L, R)  → used to serialise/copy a tree
  Postorder (L, R, Root)  → used to delete a tree, and to evaluate an expression tree
  Level-order             → BFS with a queue
```
⚠️ **To reconstruct a unique binary tree you need inorder + (preorder OR postorder).**
Preorder + postorder alone is **not** sufficient (it is for a full binary tree). ⭐

## 1.4 Graph algorithms ⭐⭐

| Algorithm | Purpose | Complexity | Notes |
|---|---|---|---|
| BFS | Shortest path in an **unweighted** graph | `O(V+E)` | Queue |
| DFS | Connectivity, cycles, topological sort | `O(V+E)` | Stack/recursion |
| **Dijkstra** | Single-source shortest path | `O((V+E) log V)` with a heap | ⚠️ **fails with negative edges** |
| **Bellman-Ford** | Single-source with negative edges | `O(VE)` | Detects negative cycles ⭐ |
| Floyd-Warshall | All-pairs shortest path | `O(V³)` | DP; handles negative edges (no negative cycles) |
| Prim | MST | `O(E log V)` | Grows one tree |
| Kruskal | MST | `O(E log E)` | Sorts edges + union-find |
| Topological sort | Ordering of a DAG | `O(V+E)` | Only for a **DAG** ⚠️ |
| Union-Find | Connectivity, cycle detection | `O(α(n))` ≈ `O(1)` | Path compression + union by rank |

⭐ **Representation trade-off:** adjacency **matrix** costs `O(V²)` space with `O(1)` edge lookup;
adjacency **list** costs `O(V+E)` with `O(degree)` lookup. Use a list for sparse graphs (the usual
case), a matrix for dense ones.

---

# Part 2 — MCQ Bank (20 questions with explanations)

---

**Q1.** What is the worst-case time complexity of quicksort?
```
(a) O(n log n)    (b) O(n²)    (c) O(n)    (d) O(log n)
```
<details><summary>Answer: (b) O(n²)</summary>

The worst case occurs when every partition is maximally unbalanced — one side empty, the other of
size `n−1`. With a naive first- or last-element pivot this happens on **already-sorted or
reverse-sorted** input, which is a realistic scenario, not a pathological one.

The recurrence becomes `T(n) = T(n−1) + O(n) = O(n²)`.

**Mitigations:** randomised pivot, median-of-three, or introsort (switch to heap sort once the
recursion depth exceeds `2 log n`) — which is what `std::sort` does. ⭐
</details>

---

**Q2.** Which sorting algorithm is **stable** and guaranteed `O(n log n)` in the worst case?
```
(a) Quick sort    (b) Heap sort    (c) Merge sort    (d) Selection sort
```
<details><summary>Answer: (c) Merge sort</summary>

Merge sort is stable (when the merge step takes from the left half on ties) and its recurrence
`T(n) = 2T(n/2) + O(n)` gives `O(n log n)` in **all** cases.

**Why not the others:** quicksort is `O(n²)` worst-case and unstable; heap sort is `O(n log n)`
guaranteed but **unstable**; selection sort is `O(n²)` and also unstable.

⚠️ The price merge sort pays is `O(n)` auxiliary space — the reason heap sort is chosen when memory
is tight and quicksort when average speed matters most.
</details>

---

**Q3.** The **inorder** traversal of a binary search tree produces:
```
(a) Random order    (b) Sorted ascending order    (c) Level order    (d) Reverse sorted
```
<details><summary>Answer: (b) sorted ascending</summary>

Inorder visits `left → root → right`. The BST property guarantees everything in the left subtree
is smaller and everything in the right subtree is larger, so the visit order is ascending.

⭐ This is why "validate a BST" can be done by checking that an inorder traversal is strictly
increasing, and why "k-th smallest element in a BST" is an inorder traversal stopped after `k`
nodes.
</details>

---

**Q4.** What is the time complexity of searching in a **hash table** in the **worst** case?
```
(a) O(1)    (b) O(log n)    (c) O(n)    (d) O(n log n)
```
<details><summary>Answer: (c) O(n)</summary>

If every key hashes to the same bucket, the structure degenerates to a single linked list of length
`n`, and a search must scan all of it.

**Average case is `O(1)`** given a good hash function and a bounded load factor. Modern
implementations mitigate the worst case by **treeifying** long buckets: Java's `HashMap` converts a
bucket to a red-black tree beyond 8 entries, giving `O(log n)` worst case instead of `O(n)`. ⭐
</details>

---

**Q5.** Which data structure is used for **BFS**?
```
(a) Stack    (b) Queue    (c) Priority queue    (d) Array
```
<details><summary>Answer: (b) Queue</summary>

BFS explores level by level, which requires FIFO order — a **queue**.

**Contrast:** DFS uses a **stack** (explicit or the call stack); **Dijkstra** uses a **priority
queue** (min-heap), because it must always expand the currently closest unvisited vertex. ⭐
</details>

---

**Q6.** In a **max-heap** with `n` elements, where is the **minimum** element?
```
(a) At the root    (b) At the last position    (c) In one of the leaves    (d) In the middle
```
<details><summary>Answer: (c) in one of the leaves</summary>

A max-heap guarantees only that **every parent ≥ its children**. There is no ordering among
siblings or across subtrees, so the minimum could be **any leaf** — but it must be a leaf, because
any internal node is ≥ its children and therefore not minimal.

⚠️ **(b) is the planted trap:** the last array position is a leaf, but not necessarily the smallest
one. Finding the minimum requires scanning all `⌈n/2⌉` leaves — `O(n)`.
</details>

---

**Q7.** Which algorithm cannot handle **negative edge weights**?
```
(a) Bellman-Ford    (b) Floyd-Warshall    (c) Dijkstra    (d) BFS on an unweighted graph
```
<details><summary>Answer: (c) Dijkstra</summary>

Dijkstra's correctness depends on the greedy invariant that once a vertex is finalised, no shorter
path to it can be found later. A negative edge can reduce the distance to an already-finalised
vertex, breaking that invariant.

**Bellman-Ford** relaxes all edges `V−1` times and therefore handles negative weights, and a `V`-th
pass that still relaxes something proves a **negative cycle** exists. **Floyd-Warshall** also
handles negative edges (a negative value on the diagonal reveals a negative cycle). ⭐
</details>

---

**Q8.** The number of edges in a **complete undirected graph** with `n` vertices is:
```
(a) n    (b) n(n−1)    (c) n(n−1)/2    (d) n²
```
<details><summary>Answer: (c) n(n−1)/2</summary>

Every unordered pair of distinct vertices is joined: `C(n, 2) = n(n−1)/2`.

For a complete **directed** graph the answer is `n(n−1)` — each pair contributes two arcs.

⭐ Same formula as handshakes among `n` people, and as comparisons in a naive all-pairs loop.
</details>

---

**Q9.** A **stack** is used to implement which of the following?
```
(a) BFS    (b) Function-call management    (c) Round-robin scheduling    (d) Printer queue
```
<details><summary>Answer: (b) function-call management</summary>

The **call stack** stores activation records (return address, parameters, local variables) in LIFO
order, which exactly matches the nesting of function calls and returns.

**Other classic stack applications:** expression evaluation and infix→postfix conversion,
parenthesis matching, undo, DFS, backtracking, and browser history.
(a), (c) and (d) are all **queue** applications.
</details>

---

**Q10.** What is the height of a **complete** binary tree with `n` nodes?
```
(a) n    (b) log₂ n    (c) ⌊log₂ n⌋    (d) n/2
```
<details><summary>Answer: (c) ⌊log₂ n⌋</summary>

A complete binary tree fills every level except possibly the last, which fills left to right. Its
height (counting edges from the root) is `⌊log₂ n⌋`.

Check: `n = 7` gives `⌊log₂7⌋ = 2` ✓ (a perfect tree of 3 levels: 1 + 2 + 4 = 7 nodes, height 2).

⚠️ Contrast with a **skewed** binary tree, whose height is `n − 1` — which is exactly why an
unbalanced BST degenerates to `O(n)` operations. ⭐
</details>

---

**Q11.** Which traversal of a binary tree is used to **delete** the tree?
```
(a) Preorder    (b) Inorder    (c) Postorder    (d) Level order
```
<details><summary>Answer: (c) Postorder</summary>

Postorder visits `left → right → root`, so both children are freed **before** the parent. Any other
order would free a node while its children are still reachable only through it — a leak (or a
dangling pointer if you keep the reference).

⭐ Postorder is also used to **evaluate an expression tree**, for the same reason: operands must be
computed before the operator.
</details>

---

**Q12.** What is the **amortised** time complexity of appending to a dynamic array (e.g. Python
`list.append`, C++ `vector::push_back`)?
```
(a) O(1)    (b) O(n)    (c) O(log n)    (d) O(n log n)
```
<details><summary>Answer: (a) O(1) amortised</summary>

Most appends are `O(1)`. When the array is full it doubles, which costs `O(n)` to copy — but that
cost is spread over the `n` cheap appends that preceded it.

**The accounting argument:** `n` appends trigger copies of sizes `1, 2, 4, …, n`, summing to
`2n − 1 = O(n)` total work, so `O(1)` per operation on average.

⚠️ **Doubling is essential.** Growing by a *constant* amount gives `O(n)` amortised, because the
copy costs sum to `O(n²)`. ⭐
</details>

---

**Q13.** In a **B+ tree**, where are the actual data records stored?
```
(a) Only in internal nodes    (b) Only in leaf nodes
(c) In both    (d) In the root
```
<details><summary>Answer: (b) only in leaf nodes</summary>

In a B+ tree the internal nodes hold **only keys** (as routing information) while all records live
in the **leaves**, which are additionally **linked together** in a sorted chain.

**Why this design wins for databases ⭐⭐:**
- Internal nodes hold only keys, so the fan-out is higher and the tree is shallower — fewer disk
  reads per lookup.
- The linked leaves make **range scans** and `ORDER BY` extremely efficient: find the start, then
  walk the chain.

(A plain **B tree** stores records in internal nodes too, which lowers fan-out.)
</details>

---

**Q14.** Which of the following is **not** a stable sorting algorithm?
```
(a) Bubble sort    (b) Insertion sort    (c) Merge sort    (d) Selection sort
```
<details><summary>Answer: (d) Selection sort</summary>

Selection sort swaps the minimum of the unsorted region into position, and that swap can jump an
element over an equal one, reversing their relative order.
```
[4a, 4b, 1]  →  swap 1 with the first 4a  →  [1, 4b, 4a]     order of the two 4s is reversed ⚠️
```
Bubble, insertion and merge sort all preserve the relative order of equal elements.

⭐ **Why stability matters:** when sorting records by a secondary key after a primary one, a stable
sort preserves the earlier ordering — which is how multi-key sorts are built.
</details>

---

**Q15.** The time complexity of building a heap from an unsorted array of `n` elements is:
```
(a) O(n log n)    (b) O(n)    (c) O(log n)    (d) O(n²)
```
<details><summary>Answer: (b) O(n)</summary>

The bottom-up `heapify` (Floyd's algorithm) is **`O(n)`**, not `O(n log n)`.

**The reason:** most nodes are near the bottom and sift down very little. Summing the work:
```
Σ (nodes at height h) × h  =  Σ (n/2^(h+1)) × h  =  n · Σ h/2^(h+1)  =  n · O(1)  =  O(n)
```
because `Σ h/2^h` converges to 2.

⚠️ Inserting `n` elements one at a time **is** `O(n log n)`. The distinction between "build heap"
and "n insertions" is exactly what the question tests. ⭐
</details>

---

**Q16.** Which data structure best supports **prefix search** ("find all words starting with
'pre'")?
```
(a) Hash table    (b) Binary search tree    (c) Trie    (d) Heap
```
<details><summary>Answer: (c) Trie</summary>

A trie stores strings character by character along paths from the root, so finding all words with a
given prefix is: walk the prefix (`O(L)`), then collect everything in that subtree.

**Why not the others:** a **hash table** destroys ordering and prefix structure — it can only do
exact lookups. A **BST** can do prefix search on sorted strings but with `O(log n · L)` comparisons
and no shared-prefix compression. A **heap** has no search capability at all.

⭐ Tries are the standard answer for autocomplete, spell-check and IP routing tables (where a
compressed variant, the radix/Patricia trie, is used).
</details>

---

**Q17.** What is the space complexity of the **recursive** merge sort?
```
(a) O(1)    (b) O(log n)    (c) O(n)    (d) O(n log n)
```
<details><summary>Answer: (c) O(n)</summary>

The dominant cost is the **`O(n)` auxiliary array** used by the merge step. The recursion stack
adds `O(log n)`, which is subsumed.

**Contrast:** quicksort needs only `O(log n)` for its recursion stack (with tail-call optimisation
on the larger side), which is why it is considered in-place while merge sort is not.

(An in-place merge sort exists but is complex and slower in practice.) ⭐
</details>

---

**Q18.** Which traversal pair uniquely determines a binary tree?
```
(a) Preorder + Postorder    (b) Inorder + Preorder
(c) Inorder + Level order   (d) Preorder + Level order
```
<details><summary>Answer: (b) Inorder + Preorder</summary>

**Inorder is essential** because it is the only traversal that reveals the **left/right split**
around the root. Preorder (or postorder) then identifies which node is the root of each subtree.

**Why not (a):** preorder + postorder cannot distinguish a node with only a left child from one
with only a right child. Example: both `A→B` as a left child and `A→B` as a right child give
preorder `AB` and postorder `BA`. ⚠️ (It *is* sufficient for a **full** binary tree, where every
node has 0 or 2 children.)

**(c)** inorder + level order also works, though it is rarely asked; **(d)** does not, for the same
reason as (a). ⭐
</details>

---

**Q19.** Which of the following problems **cannot** be solved by a greedy algorithm (optimally)?
```
(a) Minimum spanning tree    (b) Fractional knapsack
(c) 0/1 knapsack             (d) Huffman coding
```
<details><summary>Answer: (c) 0/1 knapsack</summary>

**0/1 knapsack requires dynamic programming**; no greedy rule is optimal for it.

**Counter-example to the natural greedy (highest value/weight ratio first).** Capacity = 10:
```
Item 1 : weight 6,  value 12   ratio 2.0
Item 2 : weight 5,  value  9   ratio 1.8
Item 3 : weight 5,  value  9   ratio 1.8

GREEDY  : takes Item 1 (best ratio). Remaining capacity 4 -- neither other item fits.
          Total value = 12
OPTIMAL : takes Items 2 and 3 (weight 5 + 5 = 10 exactly).
          Total value = 18
```
The greedy choice is irrevocable: once Item 1 is taken, the 4 units of leftover capacity are
wasted because items cannot be split. DP over `(item, remaining capacity)` explores keeping *and*
skipping each item, which is what recovers the optimum.

**Why the others are greedy-solvable:**
- **MST** -- Prim and Kruskal are greedy and provably optimal, by the cut property.
- **Fractional knapsack** -- greedy by ratio *is* optimal here, precisely because the last item can
  be split to fill the remaining capacity exactly, so no capacity is ever wasted.
- **Huffman coding** -- repeatedly merging the two least-frequent symbols is provably optimal.

⭐ The fractional-vs-0/1 distinction is one of the most commonly asked algorithm MCQs, and the
one-line reason is worth memorising: **splitting items removes the wasted-capacity problem that
defeats greedy.**
</details>

---

**Q20.** The lower bound on the number of comparisons for any **comparison-based** sort is:
```
(a) Ω(n)    (b) Ω(n log n)    (c) Ω(n²)    (d) Ω(log n)
```
<details><summary>Answer: (b) Ω(n log n)</summary>

**The decision-tree argument:** any comparison sort can be modelled as a binary decision tree whose
leaves are the `n!` possible permutations. A binary tree with `n!` leaves has height at least
`log₂(n!)`, and by Stirling's approximation `log₂(n!) = Θ(n log n)`.

Since the height is the worst-case number of comparisons, no comparison sort can beat `Ω(n log n)`.

⭐ **Counting, radix and bucket sort beat this bound** because they do not compare elements — they
use the values themselves as indices, which requires assumptions about the key domain (bounded
integers, fixed-width keys). That caveat is the expected follow-up.
</details>

---

## Scoring

| Correct | Read as |
|---|---|
| 18-20 | DSA theory is solid |
| 14-17 | Re-memorise the complexity tables |
| 10-13 | Re-read Part 1 and `../../01_DSA/complexity-cheatsheet.md` |
| < 10 | Half a day on the tables; these are free marks |

---

## Recall questions

1. Give the complexity table for array, linked list, hash table, BST and heap.
2. Which sorts are stable? Which are in-place? Which are `O(n log n)` guaranteed?
3. State the comparison-sort lower bound and its proof sketch.
4. Why is build-heap `O(n)` while `n` insertions are `O(n log n)`?
5. Which traversal pairs uniquely determine a binary tree, and why must inorder be one of them?
6. Why does Dijkstra fail on negative edges, and what replaces it?
7. Why do databases use B+ trees rather than B trees or BSTs?
8. Explain the amortised `O(1)` of dynamic array append and why doubling is required.
9. Contrast fractional and 0/1 knapsack, and say which technique each needs.
10. Where is the minimum element of a max-heap, and what does finding it cost?
