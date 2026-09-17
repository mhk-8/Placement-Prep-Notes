# Trees & BST — Pitfalls

## The return-vs-track confusion
- In diameter and maximum-path-sum problems, returning the tracked quantity instead of what the parent needs. The function returns a **downward path** (height or single-branch gain); the answer lives in a separate variable updated with `L + R`.
- Forgetting to clamp negative gains with `max(0, …)` in maximum path sum, so a negative subtree drags the answer down.

## BST validation
- Comparing only against the immediate children instead of propagating bounds. `[10, 5, 15, null, null, 6, 20]` passes the naive check and is not a BST.
- Using `INT_MIN`/`INT_MAX` as initial bounds in C++/Java when node values can equal them — use nullable bounds or 64-bit.
- Allowing equality (`<=`) when the problem requires strict ordering, or vice versa. Ask about duplicates.

## Traversals
- Level order: iterating `for _ in range(len(q))` but appending to `q` inside the loop *after* reading `len` — correct only if `len` was captured before the loop starts. Capture it.
- Iterative preorder: pushing left before right, which reverses the output order.
- Postorder via reversed preorder: forgetting the final `[::-1]`.
- Confusing "depth" and "height", and the ±1 convention for a single node. State your convention.

## Null handling
- Not checking `if not node` at the top of every recursive function.
- Minimum depth: treating a node with one null child as a leaf. It is not — the minimum depth must go through an actual leaf.
- Assuming a tree is non-empty.

## BST operations
- Deletion: replacing with the *predecessor's* value but then deleting from the wrong subtree, or forgetting to recursively delete the successor node afterwards.
- Insert: not returning the (possibly new) subtree root, so the link is never attached.
- Assuming a BST is balanced. It may be a path, making every operation O(n).

## Reconstruction
- Rebuilding with a linear `index()` search per node → O(n²). Precompute an index map.
- Trying to rebuild from preorder + postorder — that pair is not unique for general binary trees.

## Depth
- Recursion on a skewed 10⁵-node tree overflows the stack in both Python and C++. Use the iterative traversal, or raise the limit and hope.
- Path-collection problems: appending `path` rather than `path[:]` (same bug as backtracking).
