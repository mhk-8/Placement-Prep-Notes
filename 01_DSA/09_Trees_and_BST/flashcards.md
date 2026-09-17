# Trees & BST — Flashcards

## Questions

1. Which traversal produces sorted output on a BST, and why?
2. When do you choose preorder over postorder?
3. In the diameter problem, what does the recursive function return and what does it track?
4. Why is maximum path sum clamped with `max(0, …)`?
5. Why is checking `left.val < node.val < right.val` insufficient for BST validation?
6. Give a small counterexample to that naive check.
7. Describe BST deletion in all three cases.
8. How do you find the LCA in a BST? In a general binary tree?
9. Which pairs of traversals uniquely determine a binary tree?
10. Why do preorder + postorder fail to determine one?
11. How do you serialize a binary tree so it can be rebuilt?
12. What are the parent and children indices in an array-encoded complete binary tree?
13. What is the height range of a binary tree with n nodes?
14. Why is minimum depth not simply the postorder minimum of the two child depths?
15. What does Morris traversal achieve and how?

---

## Answers

1. Inorder (left, node, right) — the BST invariant places everything smaller on the left and everything larger on the right, so an in-order visit emits keys in increasing order.
2. When information flows **downward** — bounds for BST validation, accumulated root-to-node path sums, depth. Postorder is for when the answer needs the children's results.
3. It returns the height (what a parent needs) and tracks `L + R` in an outer variable (the answer being measured).
4. A subtree contributing a negative sum should be excluded rather than subtracted, so its contribution is floored at zero.
5. The invariant applies to the whole subtree, not just the immediate children; a deep node can violate an ancestor's bound while satisfying its parent.
6. Root 10, left 5, right 15 with 15's left child 6. Every parent-child pair is locally fine, but 6 sits in the root's right subtree while being less than 10.
7. Leaf: remove. One child: splice the child into the parent's link. Two children: copy the inorder successor's key into the node, then delete that successor from the right subtree (it has at most one child).
8. BST: descend from the root, going left while both targets are smaller and right while both are larger; the first split is the LCA. General tree: postorder — return the node if it is either target; if both sides return non-null, this node is the LCA, else return whichever is non-null.
9. Preorder + inorder, and postorder + inorder. For a BST, preorder alone suffices.
10. They cannot distinguish a node with only a left child from one with only a right child.
11. Preorder with explicit null markers (e.g. `#`), consumed in the same order on deserialization; level order with markers also works.
12. Children of i are 2i+1 and 2i+2; the parent of i is (i−1)//2.
13. Between ⌊log₂ n⌋ (perfectly balanced) and n−1 (a path).
14. A node with one null child is not a leaf, so taking the minimum would return a path that stops at a null. You must take the non-null child's depth, or use BFS with an early exit.
15. Inorder traversal in O(1) space, by temporarily setting the inorder predecessor's right pointer to the current node (a thread) and removing it on the way back.
