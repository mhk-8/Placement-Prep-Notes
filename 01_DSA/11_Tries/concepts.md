# Tries — Concepts

## 1. Core idea in 3 lines
A trie stores strings by their shared prefixes: the path from the root to a node spells a prefix, so all words sharing that prefix live in one subtree. Lookup costs O(L) in the key's length, independent of how many keys are stored — which is what a hash map cannot offer for *prefix* queries. Narrow pattern, but when it applies nothing else is close.

---

## 2. Structure

Each node holds a map (or fixed array) from character to child, plus a flag marking the end of a word. The root represents the empty prefix.

```
insert("cat"), insert("car"), insert("dog")

        root
       /    \
      c      d
      |      |
      a      o
     / \     |
    t*  r*   g*          (* = end of word)
```

| Operation | Time | Notes |
|---|---|---|
| Insert | O(L) | L = word length |
| Search (exact) | O(L) | must also check the end flag |
| Prefix search | O(L) | no end-flag check |
| Delete | O(L) | unset the flag; prune childless nodes bottom-up |
| Space | O(total characters × Σ) | Σ = alphabet size |

**The end-of-word flag is essential.** Without it, `search("ca")` succeeds after inserting `"cat"`, which is wrong — that is `startsWith`, not `search`. Confusing the two is the standard bug.

**Array vs map children:** a fixed `children[26]` is fastest for lowercase-only input but wastes memory for sparse tries; a hash map handles arbitrary alphabets and sparse data. For OA purposes, `defaultdict(dict)` or a plain dict is usually simplest.

---

## 3. Trie vs hash map

| Query | Hash map | Trie |
|---|---|---|
| Exact lookup | O(L) to hash, O(1) buckets | O(L) |
| **All words with prefix P** | O(n·L) — scan everything | **O(L + output)** |
| Lexicographic ordering | ✗ | ✓ (DFS in character order) |
| Wildcard / fuzzy match | ✗ | ✓ (branch at the wildcard) |
| Memory | lower | higher (one node per prefix character) |
| Longest common prefix of a set | O(n·L) | root-to-first-branch path |

**The deciding question:** does the problem ask about *prefixes* or about *whole keys*? Only prefixes justify the extra memory.

---

## 4. The applications

### Autocomplete / prefix search
Walk to the prefix node, then DFS the subtree collecting words. To return the top-k by frequency, store a best-k list or a max-frequency value at each node during insertion — this is how real autocomplete avoids traversing the whole subtree per keystroke.

### Wildcard matching (Design Add and Search Words)
At a `.`, recurse into **every** child. Worst case O(Σ^L) for an all-wildcard query, but O(L) when the wildcards are few. The recursion must branch, not loop.

### Word Search II — trie + grid DFS
The standard motivating example. Searching a grid for each of k words independently costs O(k · R·C · 4^L). Instead, build a trie of all words and run **one** DFS from each cell, descending the trie in lockstep with the grid path and pruning the moment the current path is not a trie prefix. That prune is where all the savings come from.

Two refinements that matter: store the whole word at its terminal node (so you can emit it without reconstructing the path), and **delete the word from the trie once found**, which prunes duplicates and progressively shrinks the search.

### Binary trie — maximum XOR
Insert numbers as fixed-width bit strings, most significant bit first. To maximise `x XOR y`, greedily descend towards the *opposite* bit of x at each level, falling back to the same bit when that child is absent. O(32) per query. This solves Maximum XOR of Two Numbers, and combined with prefix XORs it solves Maximum XOR Subarray.

### Other uses
Longest common prefix of a word set (the root-to-first-branch path), word-break dictionaries, spell-check with edit distance ≤ 1 (branch on skip/replace/insert), IP routing tables (longest-prefix match), and T9 prediction.

---

## 5. Compressed variants

- **Radix tree / Patricia trie:** merge chains of single-child nodes into one edge labelled with a substring. Same asymptotics, far less memory on sparse data.
- **Suffix trie / suffix tree / suffix automaton:** all suffixes of one string, for substring queries. Well beyond OA scope; know the name.
- **Ternary search tree:** three children per node (less, equal, greater) — a memory/speed compromise between a trie and a BST.

---

## 6. Recognising a trie problem

Trigger words: **prefix, autocomplete, dictionary, starts with, word search over a word list, common prefix, maximum XOR**. Also any problem where you would otherwise re-scan a whole word list per query.

If the problem only ever asks about complete words with no prefix structure, a hash set is simpler and lighter — say so rather than building a trie for its own sake.

---

## 7. Recall questions

1. Why is trie lookup independent of the number of stored keys?
2. What breaks if you omit the end-of-word flag?
3. When is a trie better than a hash map, and when worse?
4. What is the space complexity of a trie, and what drives it?
5. How is a wildcard `.` handled, and what is the worst-case cost?
6. Why does Word Search II use one DFS per cell instead of one search per word?
7. Why delete a word from the trie once it has been found?
8. Describe the binary trie method for maximum XOR.
9. How would you support top-k autocomplete efficiently?
10. What is a radix tree and what does it save?
