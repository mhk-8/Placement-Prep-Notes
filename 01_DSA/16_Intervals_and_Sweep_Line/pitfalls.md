# Intervals & Sweep Line — Pitfalls

## The sort key
- Sorting by **start** for "maximum non-overlapping" — one long early interval then blocks everything. Sort by end.
- Sorting by **end** for merging — the merge logic requires intervals in opening order.
- Remove Covered Intervals needs start ascending **and** end descending on ties, so that a covering interval is seen before the one it covers.
- Forgetting to sort at all because the input "looked sorted" in the examples.

## Boundary semantics
- `[1,2]` and `[2,3]`: overlapping or not? Merge Intervals usually merges them; Meeting Rooms usually does not; Minimum Arrows treats a touch as a hit. **Ask, or state your assumption out loud.**
- Using `>=` where `>` is needed — the single character that separates arrows from activity selection.
- In a sweep, processing `+1` before `−1` at the same coordinate when touching should not count (or vice versa). Encode the convention in the sort key.

## Merging
- `out[-1][1] = e` instead of `max(out[-1][1], e)`. A fully nested interval then shrinks the merged range. This is the quiet killer in Merge Intervals.
- Mutating the input list when the caller needs it.
- Building the output with `append` but comparing against the input's previous element rather than the output's last element.

## Sweep line
- Not sorting the events.
- Forgetting that the counter can go negative if events are unbalanced (usually a sign of an off-by-one in event generation).
- Using a difference array over a huge coordinate range — 10⁹ entries. Compress the coordinates or use a sweep.
- In a difference array, decrementing at `e` versus `e+1`: use `e` for half-open `[s, e)` and `e+1` for inclusive `[s, e]`. Pick one and be consistent.

## Complexity
- Claiming O(n) when the sort makes it O(n log n).
- Using an O(n) insertion into a Python list inside a loop (`My Calendar I`), which is O(n²) overall — acceptable for small n, but say so.
- Re-sorting inside a loop.

## Edge cases
- Empty input.
- A single interval.
- All intervals identical.
- Intervals where start == end (zero-length).
- Completely nested intervals.
