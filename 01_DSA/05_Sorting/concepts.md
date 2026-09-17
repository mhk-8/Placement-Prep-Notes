# Sorting — Concepts

## 1. Core idea in 3 lines
You will almost never implement a sort in an OA, but you will constantly *use* one as a subroutine and be asked about one in MCQs. The properties that matter are complexity, stability, in-place-ness, and which algorithm your language's library actually uses. "Sort it first" is a legitimate opening move — just state the added O(n log n).

---

## 2. The comparison sorts

| Algorithm | Best | Average | Worst | Space | Stable | In-place | Idea |
|---|---|---|---|---|---|---|---|
| Bubble | O(n) | O(n²) | O(n²) | O(1) | ✅ | ✅ | Swap adjacent out-of-order pairs |
| Insertion | O(n) | O(n²) | O(n²) | O(1) | ✅ | ✅ | Insert each element into a sorted prefix |
| Selection | O(n²) | O(n²) | O(n²) | O(1) | ❌ | ✅ | Repeatedly select the minimum |
| **Merge** | O(n log n) | O(n log n) | O(n log n) | O(n) | ✅ | ❌ | Divide, sort halves, merge |
| **Quick** | O(n log n) | O(n log n) | **O(n²)** | O(log n) | ❌ | ✅ | Partition around a pivot, recurse |
| **Heap** | O(n log n) | O(n log n) | O(n log n) | O(1) | ❌ | ✅ | Build a heap, pop repeatedly |

**Insertion sort is O(n) on nearly-sorted input** and has tiny constants, which is why real library sorts fall back to it for subarrays below ~16 elements.

**Merge sort** is the stable, predictable choice and the only one of the three fast sorts with a guaranteed O(n log n) *and* stability — at the cost of O(n) auxiliary space. It is also the natural choice for linked lists (no random access needed, and it can be done with O(1) extra space there) and for external sorting of data that does not fit in memory.

**Quicksort** is fastest in practice because of cache locality and in-place partitioning, but it degrades to O(n²) when the pivot is consistently extreme — e.g. always choosing the first element on already-sorted input. Mitigations: randomised pivot, median-of-three, or introsort's switch to heapsort once the recursion gets too deep.

**Heapsort** has the only combination of guaranteed O(n log n) *and* O(1) space, but poor cache behaviour makes it slower than quicksort in practice.

### The lower bound
Any comparison-based sort needs Ω(n log n) comparisons: the decision tree has n! leaves, so its height is at least log₂(n!) = Θ(n log n).

---

## 3. The non-comparison sorts

| Algorithm | Time | Space | Stable | Applicable when |
|---|---|---|---|---|
| Counting | O(n + k) | O(k) | ✅ | Small integer range k |
| Radix | O(d(n + k)) | O(n + k) | ✅ | Fixed-width keys, d digits |
| Bucket | O(n + k) avg, O(n²) worst | O(n) | ✅ | Values uniformly distributed |

They beat the Ω(n log n) bound because they do not compare elements — they use the key's *value* as an index. Counting sort with k = 10⁹ is useless; with k = 100 it is optimal.

**Radix sort must use a stable inner sort** (normally counting sort), processing least-significant digit first. Without stability the earlier digits' ordering is destroyed.

---

## 4. Stability — why it matters

A sort is stable if equal elements keep their relative input order. It matters whenever you sort by several keys in sequence: to sort by department then by salary, sort by salary first, then stably by department.

| Language | Library sort | Stable? |
|---|---|---|
| C++ | `std::sort` — introsort (quick + heap + insertion) | ❌ |
| C++ | `std::stable_sort` — merge | ✅ |
| Java | primitives: dual-pivot quicksort | ❌ |
| Java | objects: Timsort | ✅ |
| Python | `sorted` / `list.sort` — Timsort | ✅ |

**Timsort** is a merge sort that detects existing sorted runs and merges them, so it is O(n) on already-sorted data and O(n log n) worst case. This "library sort is stable in Java for objects but not for primitives" asymmetry is a favourite MCQ.

---

## 5. Sorting as a subroutine

### Sort + greedy
Interval scheduling (sort by end time), minimum arrows, non-overlapping intervals, task assignment. See `14_Greedy` and `16_Intervals`.

### Sort + two pointers
3Sum, 4Sum, closest pair to a target — sorting converts an O(n²) hash approach into an O(n) scan per anchor with much cleaner duplicate handling.

### Custom comparators
"Largest Number": sort strings by `a+b > b+a` — a comparator that is not a simple key. A comparator must define a **strict weak ordering**; an inconsistent one (e.g. one that says `a < b` and `b < a`) causes undefined behaviour and can crash `std::sort` outright.

### Quickselect — k-th element in O(n) average
Partition like quicksort, but recurse into only the side containing position k. Average O(n) (n + n/2 + n/4 + … = 2n), worst O(n²) — fixed by a randomised pivot. Compare with a heap at O(n log k) and a full sort at O(n log n).

### Merge sort for counting
**Counting inversions:** during the merge, when an element from the right half is taken before `mid − i + 1` remaining elements of the left half, all of those form inversions. O(n log n). The same skeleton solves "count of smaller numbers after self" and "reverse pairs".

### Cyclic sort
When values are a permutation of 1..n, place each value at its index by swapping. O(n) time, O(1) space — the intended solution for "find the missing/duplicate number" with those constraints.

---

## 6. Which to choose

| Need | Choice |
|---|---|
| General sort | Library sort |
| Stable sort | `stable_sort` / Timsort |
| Guaranteed O(n log n), O(1) space | Heapsort |
| k-th smallest only | Quickselect, O(n) average |
| Top-k only | Heap, O(n log k) |
| Small integer range | Counting sort, O(n + k) |
| Linked list | Merge sort |
| Nearly sorted | Insertion sort / Timsort, O(n) |
| Data larger than memory | External merge sort |

---

## 7. Recall questions

1. Which sorts are stable, and which are in-place?
2. Why is quicksort O(n²) in the worst case, and how is it mitigated?
3. Prove the Ω(n log n) comparison lower bound in one sentence.
4. Why can counting sort beat that bound?
5. Why must radix sort's inner sort be stable?
6. What algorithm does your language's library sort use, and is it stable?
7. What is Timsort and why is it O(n) on sorted input?
8. Explain quickselect and its average complexity.
9. How does merge sort count inversions?
10. What is a strict weak ordering and what happens if a comparator violates it?
