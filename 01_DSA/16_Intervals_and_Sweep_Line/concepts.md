# Intervals & Sweep Line — Concepts

## 1. Core idea in 3 lines
Interval problems are almost entirely decided by one choice: **what you sort by**. Sort by start to merge, by end to schedule. When you need to know "how many things are active at once", stop thinking about intervals and convert them into +1/−1 events along a line — the sweep.

---

## 2. The sorting rule

| Goal | Sort by | Why |
|---|---|---|
| **Merge** overlapping intervals | **start** | Merging needs intervals in the order they open |
| **Maximum non-overlapping** count | **end** | Finishing earliest leaves the most room |
| Minimum removals to make non-overlapping | **end** | Complement of the above |
| Minimum arrows / points to cover all | **end** | Same greedy |
| Maximum concurrency (rooms, CPU) | **events** | Not an interval greedy at all — sweep |

Getting this backwards is the most common interval bug, and it is worth saying the reason aloud rather than recalling the rule: sorting by start for scheduling lets one long early interval block everything after it.

---

## 3. Merging

**Merge Intervals.** Sort by start; keep a current interval; if the next one starts at or before the current end, extend the end to the maximum of the two; otherwise emit and restart. O(n log n).

**Insert Interval** into an already-sorted, non-overlapping list is O(n): copy everything ending before the new one starts, absorb everything overlapping into a widened interval, emit it, copy the rest.

**Interval List Intersections** walks two sorted lists with two pointers: the intersection is `[max(starts), min(ends)]`, valid when that is non-empty; advance whichever interval ends first.

---

## 4. Scheduling greedies

**Activity selection / Non-overlapping Intervals:** sort by end, take an interval whenever its start is ≥ the last taken end. The exchange argument: if an optimal solution's first activity ends later than the greedy's, swapping in the greedy one frees at least as much time and conflicts with nothing else.

**Minimum Arrows to Burst Balloons:** identical, except a touching boundary counts as a hit, so the comparison is `start > last_end` rather than `>=`. That single character is the whole difference between the two problems — read the statement carefully on inclusivity.

---

## 5. Sweep line

Convert each interval `[s, e)` into two events: `(s, +1)` and `(e, −1)`. Sort the events by coordinate, then scan, maintaining a running counter. The counter is the number of intervals active at each point; its maximum is the peak concurrency.

**Tie-breaking at equal coordinates decides the answer.** If `[1,2]` and `[2,3]` are *not* considered overlapping, process the `−1` before the `+1` at coordinate 2 — encode it as sorting `(coord, delta)` so that −1 sorts first. If touching *does* count as overlapping, do the reverse. Always state which convention the problem wants.

**Meeting Rooms II** has three equivalent solutions, and knowing all three is worth one sentence each in an interview:
1. Sweep line on ±1 events — O(n log n), most general.
2. Min-heap of end times: pop any meeting that has finished before the current start, then push the new end; the heap size is the room count — O(n log n).
3. Two sorted arrays of starts and ends walked with a pointer — same complexity, least machinery.

**Other sweeps:** My Calendar I/II/III (booking with a limit on overlaps), Employee Free Time (merge all, report the gaps), The Skyline Problem (sweep with a max-heap of active heights), Car Pooling (±passengers), Minimum Number of Platforms.

**When the coordinate space is small and dense**, a difference array (`01_Arrays_and_Strings`) beats a sweep: `diff[s] += 1; diff[e] -= 1`, then prefix-sum once. O(n + range) with no sorting. Corporate Flight Bookings and Car Pooling with bounded stops are exactly this.

---

## 6. Interval trees and beyond

For repeated "which intervals contain point x" queries against a changing set, an interval tree or a segment tree gives O(log n + k). Rarely needed in an OA; know the name and that sorting plus binary search handles the static case.

**Coordinate compression** — map the sorted distinct endpoints to 0..m−1 — turns a sweep over huge coordinates into one over a small index range, enabling a difference array or a segment tree.

---

## 7. Recall questions

1. When do you sort by start and when by end? Give the reason, not just the rule.
2. Give the merge-intervals algorithm and its complexity.
3. Why is Insert Interval O(n) rather than O(n log n)?
4. State the exchange argument for sorting by end time.
5. Why does the arrows problem use `>` where activity selection uses `>=`?
6. Describe the sweep-line construction for maximum concurrency.
7. How does tie-breaking at equal coordinates change the answer?
8. Give three solutions to Meeting Rooms II.
9. When is a difference array preferable to a sweep?
10. What is coordinate compression and why is it used?
