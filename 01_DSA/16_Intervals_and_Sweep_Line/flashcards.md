# Intervals & Sweep Line — Flashcards

## Questions

1. When do you sort by start, and when by end? Give the reason.
2. Give the merge-intervals algorithm and the one line people get wrong.
3. Why is Insert Interval O(n)?
4. State the exchange argument for end-time sorting.
5. Why does Minimum Arrows use `>` where activity selection uses `>=`?
6. Describe the sweep-line construction for maximum concurrency.
7. How do you encode the tie-break so that touching intervals do not count as overlapping?
8. Give three solutions to Meeting Rooms II with their complexities.
9. How does the two-pointer intersection of two interval lists work?
10. When is a difference array better than a sweep line?
11. In a difference array, when do you decrement at `e` and when at `e+1`?
12. What is coordinate compression and when is it needed?
13. How do you compute employee free time?
14. What sort order does Remove Covered Intervals need?
15. What edge cases should every interval solution be tested against?

---

## Answers

1. Start for merging (you must process intervals in opening order); end for scheduling and covering (finishing earliest leaves the most room for what follows).
2. Sort by start, then extend the last kept interval when `s <= last_end`. The line people get wrong is `last_end = max(last_end, e)` — using plain `e` shrinks the range when an interval is fully nested.
3. The input is already sorted and non-overlapping, so a single linear pass suffices: copy the ones entirely before, absorb the overlapping ones into a widened interval, copy the rest.
4. If an optimal solution's first chosen interval ends later than the greedy's earliest-ending one, swapping in the greedy choice frees at least as much time and conflicts with nothing else, so optimality is preserved.
5. Arrows treat a touching boundary as a hit, so a new arrow is needed only when the next balloon starts strictly after the current arrow position.
6. Emit `(start, +1)` and `(end, −1)` for each interval, sort the events, then scan keeping a running sum; the maximum of that sum is the peak concurrency.
7. Sort by `(coord, delta)` so that −1 sorts before +1 at the same coordinate; the interval closes before the next opens.
8. Min-heap of end times, O(n log n). Sweep line on ±1 events, O(n log n). Two sorted arrays of starts and ends walked with a pointer, O(n log n).
9. The intersection is `[max(starts), min(ends)]`, kept when non-empty; then advance whichever interval ends first, since it cannot intersect anything further.
10. When the coordinate space is small and dense: `diff[s] += v; diff[e] -= v` and one prefix-sum pass is O(n + range) with no sorting.
11. Decrement at `e` for half-open `[s, e)` and at `e+1` for inclusive `[s, e]`.
12. Mapping the sorted distinct endpoints to consecutive indices 0..m−1, so that structures sized by coordinate range become feasible when the raw coordinates are huge.
13. Flatten every person's intervals into one list, merge them, and report the gaps between consecutive merged intervals.
14. Start ascending, and end **descending** on ties — so a covering interval is always seen before the interval it covers.
15. Empty input, a single interval, all identical, zero-length intervals where start equals end, and fully nested intervals.
