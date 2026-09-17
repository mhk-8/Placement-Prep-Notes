# Recursion & Backtracking — Pitfalls

## The reference bug
- `res.append(path)` instead of `res.append(path[:])`. Every stored result points at the same list, which is empty by the end. **The most common backtracking bug.** In C++, `res.push_back(path)` copies, so the bug does not occur there — but passing `path` *by value* into the recursion instead does the opposite damage (O(n) copies per node).

## Un-choose
- Forgetting `path.pop()` after the recursive call.
- Undoing in the wrong order when several structures were modified (pop the path, then remove from the sets, mirroring the order of the choose step).
- In Word Search, forgetting to restore `board[r][c]` on the failure path.
- Using `set.remove` instead of `discard` and hitting a KeyError on an already-removed value.

## Duplicates
- Omitting the `i > start` guard, which also blocks legitimate deeper reuse (so `[1,1]` never appears in Subsets II).
- Forgetting to sort before applying any duplicate skip — the rule relies on equal elements being adjacent.
- Using the Subsets II rule for Permutations II; permutations need `not used[i-1]`, not `i > start`.

## Base cases and termination
- Missing base case → infinite recursion → stack overflow.
- A base case that does not actually shrink the input on some branch.
- Checking the base case *after* indexing, so the out-of-range access happens first.
- Returning `None` implicitly on a branch that should return `False`.

## Stack depth
- Python's 1000-frame default limit. `sys.setrecursionlimit(300000)` — but note that the OS stack can still blow; on very deep recursion, go iterative.
- C++: ~10⁵–10⁶ frames. A recursive DFS on a 10⁵-node path graph is a real risk.
- Deep recursion with large local arrays multiplies the per-frame cost.

## Pruning
- Using `continue` where `break` is correct after sorting — Combination Sum can stop the loop entirely once `cands[i] > remain`.
- Pruning that is not actually safe (cutting a branch that could still reach a valid solution).
- No pruning at all on N-Queens or Sudoku, making an otherwise-correct solution TLE.

## Complexity
- Forgetting the O(n) copy factor: subsets is O(n·2ⁿ), not O(2ⁿ).
- Attempting backtracking when n is 10⁵ — read the constraints.
- Not recognising that a *count-only* version collapses to DP, and enumerating exponentially when memoisation would be polynomial.

## Grid DFS
- Not bounds-checking before indexing.
- Marking visited but never restoring, which turns backtracking into a one-shot search.
- Using a shared `visited` set across independent starting cells when it should be per-path.
