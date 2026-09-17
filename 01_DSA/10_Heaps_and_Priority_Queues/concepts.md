# Heaps & Priority Queues — Concepts

## 1. Core idea in 3 lines
A binary heap is a complete binary tree stored in an array, maintaining only one invariant: every parent compares favourably to its children. That single weak invariant is enough to give O(1) access to the extreme element and O(log n) insert and extract, without paying for a full ordering. Whenever a problem says "top k", "k-th largest", "merge streams" or "always take the smallest next", a heap is the answer.

---

## 2. Structure and operations

A **complete** binary tree in an array: children of `i` are `2i+1` and `2i+2`, parent is `(i−1)//2`. No pointers, perfect cache behaviour, and the height is exactly ⌊log₂ n⌋.

**Min-heap invariant:** `a[parent] <= a[child]` for every node. Note what this does *not* say: siblings are unordered, and the array is not sorted. Only the root is guaranteed.

| Operation | Cost | How |
|---|---|---|
| `peek` | O(1) | `a[0]` |
| `push` | O(log n) | append at the end, sift **up** while smaller than the parent |
| `pop` | O(log n) | move the last element to the root, sift **down** to the smaller child |
| `heapify` (build from array) | **O(n)** | sift down from index `n//2 − 1` to 0 |
| search arbitrary | O(n) | no ordering to exploit |
| delete arbitrary | O(log n) | only with an index map; otherwise O(n) to find it |

**Why building is O(n), not O(n log n):** sifting down from height h costs O(h), and there are at most n/2^{h+1} nodes at height h. The sum Σ h·n/2^{h+1} converges to 2n. Nodes near the leaves — the vast majority — barely move. This is a standard interview question; the intuition ("most nodes are leaves and cost nothing") is what to say.

---

## 3. The patterns

### Top-K and k-th largest
Maintain a **min**-heap of size k. Push each element; if the size exceeds k, pop. The root is then the k-th largest, and the heap holds the k largest. **O(n log k)** time, O(k) space.

Counter-intuitive but important: for the k *largest*, you use a *min*-heap, because you must cheaply discard the smallest of your current candidates.

| Approach | Time | Space | When |
|---|---|---|---|
| Full sort | O(n log n) | O(1)–O(n) | Need the whole order |
| Min-heap of size k | O(n log k) | O(k) | Streaming, or k ≪ n |
| Quickselect | O(n) average, O(n²) worst | O(1) | Only the k-th, array in memory |
| Bucket by frequency | O(n) | O(n) | Frequencies bounded by n |

### Merging k sorted sequences
Push the head of each sequence into a min-heap; repeatedly pop the smallest and push its successor. O(N log k) for N total elements. Merge k Sorted Lists, Smallest Range Covering K Lists, Kth Smallest in a Sorted Matrix.

### Two heaps — running median
A **max**-heap for the lower half and a **min**-heap for the upper half, kept balanced so their sizes differ by at most 1. The median is the larger heap's root, or the average of both roots when sizes are equal. Insert: push to one side, then move its root to the other to rebalance. O(log n) per insert, O(1) per query. The same two-heap idea handles "IPO" and sliding-window median (the latter needing lazy deletion).

### Greedy scheduling
Whenever a greedy repeatedly needs "the next best option", a heap supplies it: Task Scheduler, Reorganize String, Meeting Rooms II (heap of end times), Minimum Cost to Connect Sticks, Last Stone Weight, Network Delay Time (Dijkstra).

### Lazy deletion
Heaps cannot delete an arbitrary element. When an entry becomes stale (Dijkstra's outdated distances, a sliding window's expired indices), leave it in and skip it on pop: `if d > dist[u]: continue`. This keeps the code simple at the cost of a larger heap.

---

## 4. Heap vs other structures

- **vs sorted array:** heap gives O(log n) insert; the sorted array gives O(n) insert but O(1) indexed access and O(log n) search.
- **vs balanced BST:** the BST gives ordered iteration, predecessor/successor and arbitrary deletion in O(log n); the heap is faster and simpler when only the extreme matters.
- **vs sorting:** if you need every element in order, sort. If you need the first k, or the data arrives as a stream, heap.

---

## 5. Language details that cost time in OAs

**Python:** `heapq` is a **min**-heap only. For a max-heap, negate the keys (`heappush(h, -x)`, then `-heappop(h)`). Tuples compare lexicographically, so push `(priority, tiebreaker, payload)` — without a tiebreaker, Python compares the payloads and raises `TypeError` on non-comparable objects. `heapq.nlargest(k, it)` and `nsmallest` exist and are O(n log k).

**C++:** `priority_queue<int>` is a **max**-heap by default. Min-heap: `priority_queue<int, vector<int>, greater<int>>`. Custom order: supply a comparator whose `operator()` returns true when the first argument has *lower* priority (i.e. the comparator is reversed relative to intuition). `make_heap`/`push_heap`/`pop_heap` operate on a vector directly.

**Java:** `PriorityQueue` is a **min**-heap. Max-heap: `new PriorityQueue<>(Collections.reverseOrder())` or a comparator.

---

## 6. Recall questions

1. State the heap invariant and what it does *not* guarantee.
2. Why is building a heap O(n) while n pushes cost O(n log n)?
3. For the k largest elements, do you use a min-heap or a max-heap, and why?
4. Give the complexity comparison of sort, heap and quickselect for "k-th largest".
5. Describe the two-heap median structure and the rebalancing rule.
6. What is lazy deletion and when is it used?
7. Why is searching a heap O(n)?
8. How do you merge k sorted lists with a heap, and what is the cost?
9. What are the array index formulas for parent and children?
10. How do you make a max-heap in Python, C++ and Java?
