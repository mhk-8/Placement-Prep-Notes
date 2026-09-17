# Trees & BST — Templates

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val, self.left, self.right = val, left, right
```

## 1. Recursive traversals
```python
def preorder(n):  return [] if not n else [n.val] + preorder(n.left) + preorder(n.right)
def inorder(n):   return [] if not n else inorder(n.left) + [n.val] + inorder(n.right)
def postorder(n): return [] if not n else postorder(n.left) + postorder(n.right) + [n.val]
```

## 2. Iterative traversals
```python
def preorder_iter(root):
    if not root: return []
    st, out = [root], []
    while st:
        n = st.pop(); out.append(n.val)
        if n.right: st.append(n.right)         # right first → left pops first
        if n.left:  st.append(n.left)
    return out

def inorder_iter(root):
    st, cur, out = [], root, []
    while st or cur:
        while cur: st.append(cur); cur = cur.left
        cur = st.pop(); out.append(cur.val); cur = cur.right
    return out

def postorder_iter(root):                       # reversed preorder (N-R-L)
    if not root: return []
    st, out = [root], []
    while st:
        n = st.pop(); out.append(n.val)
        if n.left:  st.append(n.left)
        if n.right: st.append(n.right)
    return out[::-1]
```

## 3. Level order
```python
from collections import deque
def level_order(root):
    if not root: return []
    q, out = deque([root]), []
    while q:
        level = []
        for _ in range(len(q)):                 # freeze the level size
            n = q.popleft(); level.append(n.val)
            if n.left:  q.append(n.left)
            if n.right: q.append(n.right)
        out.append(level)
    return out
```
**Right side view** = last element of each level. **Zigzag** = reverse alternate levels.

## 4. Height, diameter, balanced — one postorder shape
```python
def height(n): return 0 if not n else 1 + max(height(n.left), height(n.right))

def diameter(root):
    best = 0
    def depth(n):
        nonlocal best
        if not n: return 0
        L, R = depth(n.left), depth(n.right)
        best = max(best, L + R)                 # TRACK the path through n
        return 1 + max(L, R)                    # RETURN what the parent needs
    depth(root)
    return best

def is_balanced(root):
    def check(n):
        if not n: return 0
        L = check(n.left)
        if L == -1: return -1
        R = check(n.right)
        if R == -1 or abs(L - R) > 1: return -1  # -1 propagates failure
        return 1 + max(L, R)
    return check(root) != -1
```

## 5. Binary Tree Maximum Path Sum
```python
def max_path_sum(root):
    best = float('-inf')
    def gain(n):
        nonlocal best
        if not n: return 0
        L = max(gain(n.left), 0)                # clamp negatives to 0
        R = max(gain(n.right), 0)
        best = max(best, n.val + L + R)         # path THROUGH n
        return n.val + max(L, R)                # path going UP from n
    gain(root)
    return best
```

## 6. Validate BST — bounds passed down
```python
def is_valid_bst(root, lo=float('-inf'), hi=float('inf')):
    if not root: return True
    if not (lo < root.val < hi): return False
    return (is_valid_bst(root.left,  lo, root.val) and
            is_valid_bst(root.right, root.val, hi))
```

## 7. BST insert / delete
```python
def insert(root, v):
    if not root: return TreeNode(v)
    if v < root.val: root.left = insert(root.left, v)
    else:            root.right = insert(root.right, v)
    return root

def delete(root, key):
    if not root: return None
    if key < root.val:   root.left = delete(root.left, key)
    elif key > root.val: root.right = delete(root.right, key)
    else:
        if not root.left:  return root.right      # 0 or 1 child
        if not root.right: return root.left
        succ = root.right                          # inorder successor
        while succ.left: succ = succ.left
        root.val = succ.val
        root.right = delete(root.right, succ.val)
    return root
```

## 8. Kth smallest in a BST
```python
def kth_smallest(root, k):
    st, cur = [], root
    while st or cur:
        while cur: st.append(cur); cur = cur.left
        cur = st.pop(); k -= 1
        if k == 0: return cur.val
        cur = cur.right
```

## 9. LCA
```python
def lca_bst(root, p, q):
    while root:
        if p.val < root.val and q.val < root.val:   root = root.left
        elif p.val > root.val and q.val > root.val: root = root.right
        else: return root

def lca_binary(root, p, q):
    if not root or root is p or root is q: return root
    L = lca_binary(root.left,  p, q)
    R = lca_binary(root.right, p, q)
    if L and R: return root                    # split point
    return L or R
```

## 10. Construct from preorder + inorder
```python
def build(preorder, inorder):
    idx = {v: i for i, v in enumerate(inorder)}
    self_pos = 0
    def go(lo, hi):
        nonlocal self_pos
        if lo > hi: return None
        v = preorder[self_pos]; self_pos += 1
        node = TreeNode(v)
        m = idx[v]
        node.left  = go(lo, m - 1)
        node.right = go(m + 1, hi)
        return node
    return go(0, len(inorder) - 1)
```

## 11. Serialize / deserialize
```python
def serialize(root):
    out = []
    def go(n):
        if not n: out.append('#'); return
        out.append(str(n.val)); go(n.left); go(n.right)
    go(root)
    return ','.join(out)

def deserialize(data):
    it = iter(data.split(','))
    def go():
        v = next(it)
        if v == '#': return None
        n = TreeNode(int(v)); n.left = go(); n.right = go()
        return n
    return go()
```

## 12. Path Sum II (root-to-leaf paths)
```python
def path_sum(root, target):
    res, path = [], []
    def go(n, remain):
        if not n: return
        path.append(n.val); remain -= n.val
        if not n.left and not n.right and remain == 0:
            res.append(path[:])
        go(n.left, remain); go(n.right, remain)
        path.pop()
    go(root, target)
    return res
```

## C++ notes
- Return `TreeNode*` and use `nullptr` for the base case.
- `INT_MIN`/`INT_MAX` as BST validation bounds fails when a node holds exactly those values — use `long long` bounds or nullable pointers.
- A recursive traversal of a 10⁵-node skewed tree can overflow the stack; use the iterative form.
