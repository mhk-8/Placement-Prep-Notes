# Tries — Flashcards

## Questions

1. What does a root-to-node path in a trie represent?
2. Why is trie lookup independent of the number of stored keys?
3. What goes wrong if the end-of-word flag is omitted?
4. What is the difference in implementation between `search` and `startsWith`?
5. Give the time and space complexity of insert, search and the structure overall.
6. When is a trie strictly better than a hash map?
7. When is a hash map the better choice?
8. How is a `.` wildcard handled, and what is its worst-case cost?
9. Why does Word Search II build one trie and DFS from each cell, rather than searching per word?
10. Why remove the word marker from the trie once a word is found?
11. Describe the binary-trie greedy for maximum XOR.
12. How many bits should a binary trie use for values up to 10⁹?
13. How do you make autocomplete return the top-k results efficiently?
14. What is a radix (Patricia) tree and what does it optimise?
15. What is the memory cost per node with 26 child pointers on a 64-bit machine?

---

## Answers

1. The prefix spelled by the characters along that path; every word sharing that prefix lies in that node's subtree.
2. Traversal follows one child per character of the query, so the cost is O(L) in the key's length; the number of other stored keys never enters the path.
3. `search` degenerates into `startsWith` — any prefix of a stored word is reported as present.
4. Both walk the trie to the end of the string; `search` additionally requires `node.is_word` to be true, `startsWith` does not.
5. Insert and search are O(L). Space is O(total characters stored × alphabet factor) — one node per distinct prefix character.
6. For prefix queries, lexicographic enumeration, wildcard or fuzzy matching, and longest-prefix matching — all of which a hash map cannot do without scanning everything.
7. When only exact whole-key lookups are needed: less memory, simpler code, comparable speed.
8. Recurse into every child at the wildcard position and return true if any branch succeeds. Worst case O(Σ^L) for an all-wildcard query; near O(L) when wildcards are rare.
9. Searching per word repeats the grid traversal k times. One trie lets a single DFS explore all words at once and prune the moment the current path stops being any word's prefix.
10. It prevents duplicate reports of the same word and progressively shrinks the trie, which prunes later searches.
11. Insert all numbers MSB-first as bit paths. For each x, descend preferring the child holding the opposite of x's current bit — that sets the bit in the XOR — and fall back to the same-bit child when the preferred one is absent.
12. 32. Using 31 truncates values above 2³¹−1.
13. Store, at each node, either a running top-k list or the maximum frequency in its subtree, maintained during insertion, so a query reads the answer at the prefix node instead of traversing the subtree.
14. A trie in which chains of single-child nodes are collapsed into one edge labelled with a substring. Same asymptotics, far less memory on sparse key sets.
15. 26 pointers × 8 bytes = 208 bytes plus the flag and padding — so a trie over 10⁵ characters approaches 20 MB.
