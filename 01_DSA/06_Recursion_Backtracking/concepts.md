# Recursion & Backtracking — Concepts

## 1. Core idea in 3 lines
Recursion solves a problem by reducing it to smaller instances of itself; backtracking is recursion that *builds* a candidate incrementally and abandons a branch the moment it cannot lead to a solution. Every DP, tree and graph algorithm you will meet is recursion with something added. The discipline that makes it reliable is naming the **state**, the **choice** and the **constraint** before writing a line.

---

## 2. Recursion fundamentals

Three things define a correct recursion:
1. **Base case** — the smallest input, answered directly. Missing or wrong base cases are the number-one cause of stack overflow.
2. **Recursive case** — a strictly smaller subproblem. "Strictly smaller" must be true on *every* path, or it never terminates.
3. **Combination** — how subresults compose into the answer.

**The recursion tree** is the mental model for complexity: branching factor ^ depth. Naive Fibonacci branches 2 at depth n → O(2ⁿ). Subsets branch 2 at depth n → O(2ⁿ) and that is optimal, because there are 2ⁿ subsets.

**Stack cost is real.** Depth d costs O(d) stack frames. Python's default limit is 1000; C++ typically overflows somewhere around 10⁵–10⁶ frames. A recursive DFS on a 10⁵-node path graph will crash unless you raise the limit or convert to an explicit stack.

**Head vs tail recursion:** work done before the recursive call (top-down, like preorder) vs after it (bottom-up, like postorder). Many tree problems are "which one" questions — diameter and height are postorder because they need the children's answers first.

---

## 3. Backtracking

### The skeleton
```
choose → explore → un-choose
```
The un-choose step (popping what you pushed) is what makes one mutable path buffer safe to reuse across the whole search, rather than copying at every node.

### The three things to name before coding
- **State:** what does the current path represent? (`index i, current subset, remaining target`)
- **Choices:** what can be appended at this point?
- **Constraint:** what makes a partial candidate already invalid?

If you cannot say these three in one sentence each, you are not ready to code.

### Complexity
| Problem | Count | Cost per solution | Total |
|---|---|---|---|
| Subsets | 2ⁿ | O(n) to copy | O(n·2ⁿ) |
| Permutations | n! | O(n) | O(n·n!) |
| Combinations nCk | nCk | O(k) | O(k·nCk) |
| N-Queens | — | — | ~O(n!) with pruning |

The "O(n)" factor for copying the path is why `res.append(path[:])` and not `res.append(path)` — appending the reference stores the same mutable list, which ends up empty after the final un-choose. This is the single most common backtracking bug.

### Pruning
Pruning is what makes exponential search tractable:
- **Feasibility pruning:** abandon when the partial candidate already violates a constraint (a queen attacks; remaining sum is negative).
- **Bound pruning:** abandon when even the best possible completion cannot beat the current best.
- **Ordering:** try the most constrained choice first (Sudoku: fill the cell with fewest candidates).
- **Sorting the input** enables early `break` instead of `continue` — in Combination Sum, once `a[i] > remaining`, all later candidates are too large.

### Handling duplicates
Sort the input, then at each level skip a candidate equal to the previous one *at the same level*:
```python
if i > start and a[i] == a[i-1]: continue
```
The `i > start` is essential: it permits the duplicate to be used at a *deeper* level (so `[1,1]` is generated) while forbidding two branches at the *same* level that would produce identical subsets.

**Subsets vs permutations duplicate handling differs:** for permutations you track a `used[]` array and skip when `a[i] == a[i-1] and not used[i-1]`.

---

## 4. The canonical problems and what each teaches

| Problem | Teaches |
|---|---|
| Subsets | Include/exclude branching; the copy-on-append rule |
| Subsets II | Duplicate skipping at the same level |
| Permutations | `used[]` tracking, or swap-based generation |
| Combination Sum | Reuse allowed → recurse with the *same* index |
| Combination Sum II | Each element once → recurse with `i+1`; duplicate skipping |
| Generate Parentheses | Constraint-driven choices (`open < n`, `close < open`) |
| Letter Combinations | Cartesian product over per-digit choice sets |
| Palindrome Partitioning | Backtracking + a validity test at each cut |
| Word Search | Grid DFS with an in-place visited marker and restore |
| N-Queens | Diagonal encoding (`r+c` and `r−c`), O(1) attack checks |
| Sudoku Solver | Constraint propagation and most-constrained-first ordering |

**Word Search's in-place marking** — overwrite the cell with a sentinel, recurse, restore — is backtracking applied to the grid itself, and it avoids an O(n·m) visited set per call.

**N-Queens diagonal trick:** cells on the same ↘ diagonal share `r − c`; on the same ↙ diagonal they share `r + c`. Three sets (`cols`, `diag1`, `diag2`) give O(1) conflict checks instead of O(n) scans.

---

## 5. Recursion → iteration

Sometimes required (stack depth) and sometimes asked as a follow-up:
- **Tail recursion → loop:** direct.
- **General recursion → explicit stack:** push the frame's state; simulate the call.
- **Subsets → bitmask:** for `mask` in `0..2ⁿ−1`, include `a[i]` when bit `i` is set. Cleaner than recursion and the standard answer when n ≤ 20.
- **Permutations → `next_permutation`** (C++) or Heap's algorithm.

---

## 6. Recognising backtracking in an OA

**n ≤ 20–25 in the constraints is the tell.** It means exponential is intended — backtracking or bitmask DP. Combined with "find all", "enumerate", "list every", or "is there any arrangement such that", backtracking is almost certainly the answer.

If the problem asks only for a **count** or an **optimum** rather than the actual arrangements, check whether DP with memoisation collapses the exponential search first — it usually does.

---

## 7. Recall questions

1. What three things must you name before writing a backtracking function?
2. Why `res.append(path[:])` rather than `res.append(path)`?
3. Explain the `i > start and a[i] == a[i-1]` duplicate skip and why `i > start` matters.
4. How does duplicate handling differ between Subsets II and Permutations II?
5. What is the complexity of generating all subsets, and why is it optimal?
6. Give two kinds of pruning and an example of each.
7. How do you encode the two diagonals in N-Queens for O(1) checks?
8. How does Word Search avoid allocating a visited set at every call?
9. What constraint governs the choices in Generate Parentheses?
10. What constraint value in a problem statement signals backtracking?
