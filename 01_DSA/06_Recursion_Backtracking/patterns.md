# Recursion & Backtracking — Templates

## 1. The universal skeleton
```python
res = []
def backtrack(state, path):
    if is_solution(state):
        res.append(path[:])              # COPY
        return
    for choice in choices(state):
        if not valid(state, choice): continue
        apply(state, choice); path.append(choice)      # choose
        backtrack(next_state(state, choice), path)     # explore
        path.pop(); undo(state, choice)                # un-choose
```

## 2. Subsets
```python
def subsets(a):
    res, path = [], []
    def go(start):
        res.append(path[:])              # every node is a valid subset
        for i in range(start, len(a)):
            path.append(a[i])
            go(i + 1)
            path.pop()
    go(0)
    return res
```

**Subsets with duplicates** — sort first:
```python
a.sort()
def go(start):
    res.append(path[:])
    for i in range(start, len(a)):
        if i > start and a[i] == a[i-1]: continue      # same-level skip
        path.append(a[i]); go(i + 1); path.pop()
```

**Subsets via bitmask** (n ≤ 20):
```python
n = len(a)
for mask in range(1 << n):
    sub = [a[i] for i in range(n) if mask >> i & 1]
    res.append(sub)
```

## 3. Permutations
```python
def permute(a):
    res, path = [], []
    used = [False] * len(a)
    def go():
        if len(path) == len(a): res.append(path[:]); return
        for i in range(len(a)):
            if used[i]: continue
            used[i] = True;  path.append(a[i])
            go()
            path.pop();      used[i] = False
    go()
    return res
```

**Permutations with duplicates** — sort, then:
```python
if used[i] or (i > 0 and a[i] == a[i-1] and not used[i-1]): continue
```
The `not used[i-1]` forces duplicates to be consumed in left-to-right order, so each distinct multiset arrangement is produced once.

## 4. Combination Sum
```python
def combination_sum(cands, target):           # unlimited reuse
    cands.sort()
    res, path = [], []
    def go(start, remain):
        if remain == 0: res.append(path[:]); return
        for i in range(start, len(cands)):
            if cands[i] > remain: break        # sorted → prune the rest
            path.append(cands[i])
            go(i, remain - cands[i])           # i, not i+1: reuse allowed
            path.pop()
    go(0, target)
    return res
```
**Combination Sum II** (each element once, duplicates in input): recurse with `i + 1` and add the `if i > start and cands[i] == cands[i-1]: continue` skip.

## 5. Generate Parentheses
```python
def generate(n):
    res, path = [], []
    def go(open_, close):
        if len(path) == 2 * n: res.append(''.join(path)); return
        if open_ < n:      path.append('('); go(open_ + 1, close); path.pop()
        if close < open_:  path.append(')'); go(open_, close + 1); path.pop()
    go(0, 0)
    return res
```

## 6. Word Search — grid DFS with in-place marking
```python
def exist(board, word):
    R, C = len(board), len(board[0])
    def dfs(r, c, k):
        if k == len(word): return True
        if not (0 <= r < R and 0 <= c < C) or board[r][c] != word[k]: return False
        tmp, board[r][c] = board[r][c], '#'          # mark
        found = (dfs(r+1, c, k+1) or dfs(r-1, c, k+1) or
                 dfs(r, c+1, k+1) or dfs(r, c-1, k+1))
        board[r][c] = tmp                             # restore
        return found
    return any(dfs(r, c, 0) for r in range(R) for c in range(C))
```

## 7. N-Queens
```python
def solve_n_queens(n):
    res, cols, d1, d2, pos = [], set(), set(), set(), []
    def go(r):
        if r == n:
            res.append(['.' * c + 'Q' + '.' * (n-c-1) for c in pos]); return
        for c in range(n):
            if c in cols or (r - c) in d1 or (r + c) in d2: continue
            cols.add(c); d1.add(r - c); d2.add(r + c); pos.append(c)
            go(r + 1)
            pos.pop(); d2.discard(r + c); d1.discard(r - c); cols.discard(c)
    go(0)
    return res
```

## 8. Palindrome Partitioning
```python
def partition(s):
    res, path = [], []
    def ok(l, r):
        while l < r:
            if s[l] != s[r]: return False
            l += 1; r -= 1
        return True
    def go(start):
        if start == len(s): res.append(path[:]); return
        for end in range(start, len(s)):
            if not ok(start, end): continue
            path.append(s[start:end+1]); go(end + 1); path.pop()
    go(0)
    return res
```

## 9. Recursion → explicit stack (DFS)
```python
stack = [(start, 0)]                       # (node, child index)
seen = {start}
while stack:
    node, i = stack.pop()
    if i < len(adj[node]):
        stack.append((node, i + 1))
        nxt = adj[node][i]
        if nxt not in seen: seen.add(nxt); stack.append((nxt, 0))
```

## C++ notes
- Pass the path by reference (`vector<int>& path`) and push into `res` by value — passing by value at every level is O(n) copying per node.
- `next_permutation(a.begin(), a.end())` generates permutations in lexicographic order iteratively; the array must start sorted for a full enumeration.
- Deep recursion can overflow the default 1 MB stack; an iterative version or a larger stack is needed for depths beyond ~10⁵.
