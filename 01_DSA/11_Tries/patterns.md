# Tries — Templates

## 1. Dict-based trie (shortest to write in an OA)
```python
class Trie:
    def __init__(self):
        self.root = {}
        self.END = '#'

    def insert(self, word):
        node = self.root
        for ch in word:
            node = node.setdefault(ch, {})
        node[self.END] = True

    def search(self, word):
        node = self._walk(word)
        return node is not None and self.END in node

    def starts_with(self, prefix):
        return self._walk(prefix) is not None

    def _walk(self, s):
        node = self.root
        for ch in s:
            if ch not in node: return None
            node = node[ch]
        return node
```

## 2. Class-based trie (clearer in an interview)
```python
class TrieNode:
    __slots__ = ('children', 'is_word')
    def __init__(self):
        self.children = {}
        self.is_word = False

class Trie:
    def __init__(self): self.root = TrieNode()

    def insert(self, word):
        node = self.root
        for ch in word:
            if ch not in node.children: node.children[ch] = TrieNode()
            node = node.children[ch]
        node.is_word = True

    def search(self, word):
        node = self._walk(word)
        return node is not None and node.is_word      # flag check REQUIRED

    def starts_with(self, prefix):
        return self._walk(prefix) is not None          # no flag check

    def _walk(self, s):
        node = self.root
        for ch in s:
            node = node.children.get(ch)
            if node is None: return None
        return node
```

## 3. Array-backed children (lowercase only, fastest)
```python
class Node:
    __slots__ = ('ch', 'is_word')
    def __init__(self):
        self.ch = [None] * 26
        self.is_word = False

# index with: ord(c) - 97
```

## 4. Collect all words under a prefix (autocomplete)
```python
def words_with_prefix(trie, prefix):
    node = trie._walk(prefix)
    if node is None: return []
    out = []
    def dfs(n, path):
        if n.is_word: out.append(prefix + ''.join(path))
        for ch in sorted(n.children):          # sorted → lexicographic order
            path.append(ch); dfs(n.children[ch], path); path.pop()
    dfs(node, [])
    return out
```

## 5. Wildcard search — `.` matches any character
```python
def search_wildcard(self, word):
    def dfs(node, i):
        if i == len(word): return node.is_word
        ch = word[i]
        if ch == '.':
            return any(dfs(child, i + 1) for child in node.children.values())
        child = node.children.get(ch)
        return child is not None and dfs(child, i + 1)
    return dfs(self.root, 0)
```

## 6. Word Search II — trie + grid DFS
```python
def find_words(board, words):
    root = {}
    for w in words:                              # build the trie
        node = root
        for ch in w: node = node.setdefault(ch, {})
        node['$'] = w                            # store the word itself

    R, C, res = len(board), len(board[0]), []
    def dfs(r, c, node):
        ch = board[r][c]
        nxt = node.get(ch)
        if nxt is None: return                   # PRUNE: not a valid prefix
        word = nxt.pop('$', None)
        if word: res.append(word)                # found; removing '$' de-dups
        board[r][c] = '#'
        for dr, dc in ((1,0), (-1,0), (0,1), (0,-1)):
            nr, nc = r + dr, c + dc
            if 0 <= nr < R and 0 <= nc < C and board[nr][nc] != '#':
                dfs(nr, nc, nxt)
        board[r][c] = ch
        if not nxt: node.pop(ch)                 # prune the dead branch

    for r in range(R):
        for c in range(C):
            dfs(r, c, root)
    return res
```

## 7. Binary trie — maximum XOR pair
```python
class BinTrie:
    def __init__(self, bits=32):
        self.root, self.bits = {}, bits

    def insert(self, num):
        node = self.root
        for i in range(self.bits - 1, -1, -1):       # MSB first
            b = (num >> i) & 1
            node = node.setdefault(b, {})

    def max_xor(self, num):
        node, best = self.root, 0
        for i in range(self.bits - 1, -1, -1):
            b = (num >> i) & 1
            want = 1 - b                              # prefer the opposite bit
            if want in node: best |= (1 << i); node = node[want]
            else:            node = node[b]
        return best

def find_maximum_xor(nums):
    t = BinTrie()
    for x in nums: t.insert(x)
    return max(t.max_xor(x) for x in nums)
```

## 8. Delete from a trie (prune empty nodes)
```python
def delete(root, word):
    def go(node, i):
        if i == len(word):
            if not node.is_word: return False
            node.is_word = False
            return len(node.children) == 0        # prunable?
        ch = word[i]
        child = node.children.get(ch)
        if child is None: return False
        if go(child, i + 1):
            del node.children[ch]
            return not node.is_word and len(node.children) == 0
        return False
    go(root, 0)
```

## C++ notes
```cpp
struct Node { Node* ch[26] = {}; bool word = false; };
// index: word[i] - 'a'
// or: unordered_map<char, Node*> for arbitrary alphabets
```
- Allocate nodes from a pre-sized `vector<Node>` pool rather than with `new` per node — far faster and avoids leaks.
- 26 pointers per node is 208 bytes on 64-bit; a 10⁵-character trie is ~20 MB. Check the memory limit.
