# Recursion & Backtracking — Flashcards

## Questions

1. What three things must be named before writing a backtracking function?
2. What is the choose/explore/un-choose skeleton?
3. Why must you append a copy of the path rather than the path itself?
4. Give the same-level duplicate-skip condition for Subsets II and explain `i > start`.
5. What is the duplicate rule for Permutations II, and why is it different?
6. What is the complexity of generating all subsets? All permutations?
7. Name two kinds of pruning with an example of each.
8. In Combination Sum, why recurse with `i` rather than `i + 1`?
9. How are the two diagonals encoded in N-Queens?
10. How does Word Search mark visited cells without a separate visited structure?
11. What are the two choice constraints in Generate Parentheses?
12. What constraint size in a problem statement signals backtracking or bitmask DP?
13. How do you generate all subsets iteratively?
14. What is Python's default recursion limit and why does raising it not always suffice?
15. When should a backtracking problem be solved with DP instead?

---

## Answers

1. The **state** (what the current path represents), the **choices** available at this point, and the **constraint** that makes a partial candidate invalid.
2. Apply a choice and push it; recurse; then pop and undo — so one mutable buffer serves the whole search tree.
3. The path is mutated in place after the append; storing the reference means every result ends up as the same (eventually empty) list.
4. `if i > start and a[i] == a[i-1]: continue`. `i > start` allows the duplicate to be used deeper in the same branch (producing `[1,1]`) while preventing two sibling branches at the same level from generating identical subsets.
5. `if used[i] or (i > 0 and a[i] == a[i-1] and not used[i-1]): continue`. Permutations use every element, so the constraint is about the *order* duplicates are consumed, forcing left-to-right.
6. Subsets: O(n·2ⁿ) — 2ⁿ subsets, O(n) to copy each. Permutations: O(n·n!).
7. Feasibility pruning (abandon when a constraint is already violated — a queen attacks, remaining target is negative) and bound pruning (abandon when even the best completion cannot beat the current best).
8. Because each candidate may be reused an unlimited number of times; `i + 1` would forbid reuse.
9. Cells on the same ↘ diagonal share `r − c`; on the same ↙ diagonal they share `r + c`. Three sets give O(1) conflict checks.
10. It overwrites the cell with a sentinel character before recursing and restores the original value afterwards.
11. Add `(` while `open < n`; add `)` while `close < open`.
12. n ≤ 20–25 — it signals that O(2ⁿ) is intended.
13. Iterate `mask` from 0 to 2ⁿ−1 and include `a[i]` whenever bit i of the mask is set.
14. 1000. Raising it with `sys.setrecursionlimit` does not enlarge the actual OS stack, so very deep recursion can still crash the interpreter; an iterative version is the safe fix.
15. When the problem asks only for a count or an optimum rather than the enumeration itself — overlapping subproblems then collapse the exponential tree to polynomial work.
