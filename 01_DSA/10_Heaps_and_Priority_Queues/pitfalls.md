# Heaps & Priority Queues — Pitfalls

## Direction confusion
- Using a **max**-heap for "k largest". You need a **min**-heap of size k, so the smallest candidate is the one you can discard in O(log k).
- Python's `heapq` is min-only. Forgetting to negate — and forgetting to negate *back* on pop — is a silent wrong answer.
- C++ `priority_queue` is max by default; Java's `PriorityQueue` is min by default. Getting these backwards between languages is common.
- A C++ custom comparator must return true when the first argument has **lower** priority — i.e. `>` gives a min-heap. It reads backwards.

## Tuple comparison
- `heappush(h, (dist, node_object))` raises `TypeError` when two distances tie and Python falls through to comparing the objects. Add a unique counter: `(dist, next(counter), obj)`.
- Pushing mutable objects and then mutating them — the heap order silently becomes invalid.

## Complexity claims
- Saying heap construction is O(n log n). Bottom-up `heapify` is **O(n)**; it is n successive pushes that cost O(n log n).
- Saying "find an arbitrary element in a heap is O(log n)". It is O(n) — there is no ordering to search.
- Using a heap where a full sort is needed anyway, or where counting sort is linear.
- Sorting the whole array when only the top k is needed: O(n log n) versus O(n log k).

## Stale entries
- Forgetting the `if d > dist[u]: continue` guard in Dijkstra, which processes outdated entries and can produce wrong distances or blow up the runtime.
- Sliding-window median or window problems without lazy deletion, where expired elements silently remain.

## Two-heap median
- Not rebalancing after every insert, letting the sizes drift by more than 1.
- Reading the median from the wrong heap when the sizes are unequal.
- Integer division when averaging two roots — the median of an even-sized set may be fractional.

## Implementation
- `sift_down` comparing against only one child instead of the smaller of the two.
- Building the heap by sifting **up** from 0 (that is the O(n log n) way); the O(n) build sifts **down** from `n//2 − 1` to 0.
- Off-by-one in `2i+1` / `2i+2` / `(i−1)//2`.
- Popping from an empty heap without checking.
