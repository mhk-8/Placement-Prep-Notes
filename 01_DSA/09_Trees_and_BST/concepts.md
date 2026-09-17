# Trees & BST — Concepts

## 1. Core idea in 3 lines
A tree is recursion made visible: almost every tree problem is "solve the left subtree, solve the right subtree, combine". The decisive question for any tree problem is *when* the node is processed relative to its children — preorder (top-down, passing information down) or postorder (bottom-up, returning information up). A BST adds the ordering invariant, which turns search into binary search.

---

## 2. Vocabulary and facts

- **Height** of a node = edges on the longest downward path to a leaf; **depth** = edges from the root. A single node has height 0 (some texts say 1 — state your convention).
- **Complete** binary tree: every level full except possibly the last, filled left to right. This is what heaps use, and what permits array encoding: children of `i` are `2i+1`, `2i+2`; parent is `(i−1)//2`.
- **Full** binary tree: every node has 0 or 2 children. **Perfect:** all leaves at the same depth, 2^h+1 − 1 nodes.
- **Balanced:** heights of the two subtrees differ by at most 1 at every node → height O(log n).
- A binary tree with n nodes has n−1 edges and height between ⌊log₂ n⌋ (balanced) and n−1 (skewed).
- Number of distinct binary tree shapes with n nodes = Catalan(n) = C(2n,n)/(n+1).

---

## 3. Traversals

| Order | Visit | Use |
|---|---|---|
| **Preorder** (N-L-R) | before children | Copy/serialize a tree, pass constraints down (e.g. valid-BST bounds) |
| **Inorder** (L-N-R) | between children | **Sorted order in a BST** — the single most useful fact here |
| **Postorder** (L-R-N) | after children | Height, diameter, deletion, anything needing children's answers |
| **Level order** (BFS) | by depth | Views, level sums, minimum depth, zigzag |

**Iterative versions** are a standard follow-up:
- Preorder: a stack; push right then left.
- Inorder: push left spine, pop, visit, move right.
- Postorder: either two stacks, or do reversed-preorder (N-R-L) and reverse the output.
- Level order: a queue, freezing `len(queue)` at the start of each level.

**Morris traversal** gives inorder in O(1) space by temporarily threading each node's inorder predecessor's right pointer to it, then undoing the thread. Rarely required, occasionally asked as "can you do it in O(1) space".

**Reconstruction:** preorder + inorder, or postorder + inorder, determine a binary tree uniquely. Preorder + postorder do **not** (they cannot distinguish a single left child from a single right child). For a BST, preorder alone suffices, because inorder is implied by sorting.

---

## 4. The two shapes of tree recursion

### Bottom-up (postorder) — return a value
```
def solve(node):
    if not node: return base
    L, R = solve(node.left), solve(node.right)
    update_global_if_needed(L, R, node)
    return value_for_parent(L, R, node)
```
Use when the answer at a node depends on its subtrees: height, diameter, balanced check, maximum path sum, count of good nodes, LCA.

**The diameter trick:** the function *returns* height but *updates a global* with `L + R`. Confusing the returned value with the tracked answer is the classic bug — the return must be what the parent needs (height), not what you are measuring (diameter). The same shape solves Binary Tree Maximum Path Sum, where the return is "best single downward path" while the global tracks "best path through this node", and negative contributions are clamped with `max(0, …)`.

### Top-down (preorder) — pass state down
```
def solve(node, state):
    if not node: return
    new_state = update(state, node)
    solve(node.left, new_state); solve(node.right, new_state)
```
Use for: valid-BST bounds, path sums accumulated from the root, path printing, depth tracking.

---

## 5. BST

**Invariant:** for every node, all keys in the left subtree < node.key < all keys in the right subtree. This must hold for the **entire** subtree, not just the immediate children — checking only `left.val < node.val < right.val` is the most common wrong answer to "validate BST".

Correct validation: recurse with `(low, high)` bounds, or do an inorder traversal and check it is strictly increasing.

| Operation | Balanced | Skewed |
|---|---|---|
| Search / insert / delete | O(log n) | O(n) |
| Min / max | O(log n) | O(n) |
| Inorder traversal | O(n) | O(n) |

**Deletion** has three cases: leaf (remove); one child (splice); two children (replace the key with the inorder successor — the smallest node in the right subtree — then delete that successor, which has at most one child).

**k-th smallest:** inorder traversal with a counter, stopping early — O(h + k). Augmenting each node with a subtree size gives O(h).

**LCA in a BST** is much simpler than in a general tree: descend from the root, going left while both targets are smaller, right while both are larger; the first node that splits them (or equals one of them) is the LCA. O(h), no extra space.

**LCA in a general binary tree:** postorder — if the current node is either target, return it; otherwise recurse both sides. If both sides return non-null, the current node is the LCA; else return whichever is non-null.

**Self-balancing trees** (AVL, red-black) keep height O(log n) via rotations. Know the idea and that `std::map` and `TreeMap` are red-black trees; the rotation mechanics are rarely required.

---

## 6. Serialization

Preorder with explicit null markers is the simplest scheme: emit `#` for null, and deserialize by consuming tokens in the same order. Level-order with null markers also works. The key insight is that **null markers make the structure recoverable from a single traversal**, which is otherwise impossible.

---

## 7. Recall questions

1. Which traversal yields sorted order in a BST, and why?
2. When do you choose preorder over postorder?
3. Explain the diameter trick — what is returned versus what is tracked.
4. Why is `left.val < node.val < right.val` insufficient to validate a BST?
5. Describe BST deletion in all three cases.
6. How is LCA found in a BST, and how in a general binary tree?
7. Which pairs of traversals uniquely determine a binary tree, and which do not?
8. How do you serialize and deserialize a binary tree?
9. What are the array indices of the children and parent in a complete binary tree?
10. What is Morris traversal and what does it buy you?
