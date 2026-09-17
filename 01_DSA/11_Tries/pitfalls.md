# Tries — Pitfalls

## The end-of-word flag
- Omitting it, so `search` behaves like `startsWith` — inserting `"cat"` then searching `"ca"` wrongly returns true.
- Checking the flag in `startsWith` (it must not be checked there).
- Using a sentinel key like `'#'` or `'$'` in a dict-based trie and then iterating `node.children` without excluding it — the sentinel gets treated as a character.

## Structure
- Reusing one `TrieNode()` default argument or a shared mutable children dict across nodes.
- `node = node.children[ch]` without first checking membership, raising KeyError instead of returning false.
- Assuming lowercase-only and indexing `children[26]` with a digit, space or uppercase character — an out-of-range write that corrupts adjacent memory in C++.

## Word Search II
- Not restoring `board[r][c]` on the way out.
- Running one grid search per word (O(k · R·C · 4^L)) instead of one DFS per cell against the whole trie.
- Not removing the word marker after a hit, so the same word is reported many times.
- Never pruning dead branches, losing most of the speed advantage.
- Descending the trie and the grid out of step — the trie node must advance by exactly the character just consumed from the grid.

## Binary trie
- Iterating bits least-significant first. Maximum XOR is a greedy on the **most** significant bit, so the insertion and query must both go MSB → LSB.
- Choosing too few bits: 32 for values up to ~2×10⁹; using 31 silently truncates.
- Querying an empty trie, or falling back to the same-bit child without checking it exists.

## Memory and complexity
- 26 child pointers per node on a large word list can exhaust the memory limit. Use a hash map for children, or a radix tree.
- Claiming trie search is O(1). It is O(L) — independent of the *number* of keys, not of key length.
- Building a trie for a problem that only needs exact-match lookups, where a hash set is smaller and faster.

## Wildcard search
- Using a loop instead of recursion at `.` — you must explore *all* children and return true if any succeeds.
- Forgetting that an all-wildcard query is O(Σ^L) and can TLE; mention it rather than being surprised.
