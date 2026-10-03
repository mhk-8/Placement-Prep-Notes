
# Sprinklr / Media.net / Codenation — Prep Plan, Question Bank and Debriefs

> ⭐ **Preparation note:** this plan is ~80% identical to `../Google/01-prep-and-questions.md`.
> If you are preparing for both, do the Google plan and treat these companies as additional
> attempts at the same skill. Do not run two separate plans.

---

## 1. Topic priorities ⭐⭐

| Priority | Topic | Source | Status |
|---|---|---|---|
| **P1** | **DP** ⚠️⚠️ — 1-D, knapsack, 2-D, tree, bitmask, digit | `../../01_DSA/` | ☐ |
| **P1** | **Constraints → complexity**, memorised | `../Google/00-process-and-oa.md` §3 | ☐ |
| **P1** | Graphs — Dijkstra, 0-1 BFS, MST, topological sort, union-find, SCC | `../../01_DSA/` | ☐ |
| **P1** | Binary search on the answer | `../../01_DSA/` | ☐ |
| **P2** | **String algorithms** ⚠️ — KMP, Z-algorithm, hashing, tries | `../../01_DSA/` | ☐ |
| **P2** | Number theory + combinatorics mod p — nCr, sieve, modular inverse | `../../06_Online_Assessments/02_Aptitude_and_Quant/Probability_and_Combinatorics.md` | ☐ |
| **P2** | Segment tree / BIT ⭐ more likely here than at Google | `../../01_DSA/` | ☐ |
| **P3** | Greedy with exchange arguments | `../../01_DSA/` | ☐ |

---

## 2. The plan ⭐⭐⭐

**If you are also preparing for Google:** follow `../Google/01-prep-and-questions.md` §2 and add
the two topics Google weights less — **segment trees** and **string algorithms**.

**Standalone two weeks, if these are the only hard-algorithmic companies on your list:**

| Date | Task | Done |
|---|---|---|
| D-14 | Research protocol; **confirm there is no CGPA bar** ⭐; check which Sprinklr track | ☐ |
| D-13 | Constraints table memorised; 2 Codeforces Div 2 A/B problems | ☐ |
| D-12 | DP ⚠️: 1-D family (LIS, coin change, house robber, word break) | ☐ |
| D-11 | DP ⚠️: knapsack family (0/1, unbounded, subset sum, partition) | ☐ |
| D-10 | Graphs: Dijkstra with state, 0-1 BFS, union-find with path compression | ☐ |
| D-9 | DP ⚠️: 2-D (edit distance, LCS, grid paths) | ☐ |
| D-8 | **String algorithms** ⚠️: KMP, string hashing, trie — implement each once | ☐ |
| D-7 | Binary search on the answer: 4 problems | ☐ |
| D-6 | DP ⚠️: bitmask (TSP-style, assignment) | ☐ |
| D-5 | Segment tree / BIT: implement both, then 2 range-query problems ⭐ | ☐ |
| D-4 | Combinatorics mod p: nCr with modular inverse, inclusion-exclusion | ☐ |
| D-3 | **Timed contest: Codeforces Div 2 or AtCoder ABC, full duration** ⭐⭐ | ☐ |
| D-2 | Review every problem failed in D-3; write the correct solutions out | ☐ |
| D-1 | Pre-interview routine only. Re-read the constraints table. Nothing new ⚠️ | ☐ |

⭐ **The D-3 timed contest is the most important single item.** These companies' tests *are*
contests; practising the format matters as much as the topics.

---

## 3. Seeded question bank

### Problem shapes
```
"Count the number of ways / arrangements / subsequences, mod 1e9+7"
    → combinatorics or DP. Know nCr mod p with modular inverse.
"You may perform operation X at most k times; optimise Y"
    → binary search on the answer, or DP with k as a dimension
"Minimise the maximum / maximise the minimum"
    → binary search on the answer ⭐ almost always
"Given a tree, compute something for every node"
    → tree DP, or rerooting technique
"Range queries with updates"
    → segment tree or BIT ⭐
"Find all occurrences of a pattern / longest repeated substring"
    → KMP, Z-algorithm, or string hashing ⚠️ your gap
"Subset / mask over n ≤ 20"
    → bitmask DP
```

### DP — the full list to have done ⚠️⭐⭐⭐
```
1-D           : LIS (n log n), coin change, house robber (+circular), word break, decode ways,
                max subarray, jump game
KNAPSACK      : 0/1, unbounded, subset sum, partition equal subset, target sum, bounded
2-D           : edit distance, LCS, longest common substring, grid paths with obstacles,
                minimum path sum, distinct subsequences
INTERVAL      : matrix chain, burst balloons, palindrome partitioning, stone game
TREE DP       : diameter, max path sum, independent set, rerooting
BITMASK       : TSP, assignment, minimum set cover, count Hamiltonian paths
DIGIT DP      : count numbers ≤ N with a digit property
STATE MACHINE : stock problems — all five variants
ON STRINGS    : regex matching, wildcard matching, palindromic substrings
```

### Graphs
```
BFS/DFS · Dijkstra (and with an extra state dimension) · 0-1 BFS with a deque ·
Bellman-Ford and negative cycles · Floyd-Warshall · MST (Prim, Kruskal) ·
topological sort (Kahn and DFS) · union-find with path compression and union by rank ·
SCC (Kosaraju or Tarjan) · bipartite check · bridges and articulation points
```

### String algorithms ⚠️ (your gap)
```
□ KMP — build the failure function, then match. Implement it once by hand ⭐
□ Z-algorithm
□ Polynomial string hashing (and why you use two mods to avoid collisions)
□ Trie — insert, search, prefix count
□ Longest palindromic substring (expand around centre, and Manacher's if committing)
□ Suffix array basics (rare, but appears)
```

### Number theory / combinatorics
```
□ Sieve of Eratosthenes; smallest prime factor sieve
□ Modular exponentiation (fast power)
□ Modular inverse (Fermat's little theorem for prime mod)
□ nCr mod p with precomputed factorials ⭐
□ gcd/lcm, extended Euclid
□ Inclusion-exclusion
□ Counting with symmetry (Burnside, rare)
```

### Interview rounds
```
- More hard problems, live. Narrate continuously
- "Walk me through your OA solution" — be able to explain and improve it ⭐
- Occasionally a design or product-engineering question (Sprinklr tracks)
```

---

## 5. Debriefs ⭐⭐⭐

Full template: `../../07_Interviews/06_Post_Interview_Debriefs/Debrief_Template.md`

### Round 1 — `<date>` — `<type>`
```
INTERVIEWER        :
STRUCTURE / TIMING :
QUESTIONS ASKED (verbatim):
  1.
  2.
  3.
CODING PROBLEM     :
  My approach     :
  Correct approach (write it out NOW):
WHAT I FUMBLED, AND THE RIGHT ANSWER:
  1.
  2.
TONE / STYLE FOR THE NEXT ROUND:
OUTCOME            :
THREE ACTIONS:
  1.
  2.
  3.
```

### Round 2 — `<date>` — `<type>`
```
(same structure)
```

### Round 3 — `<date>` — `<type>`
```
(same structure)
```

---

## 6. Questions I collected myself ⭐⭐⭐

| Date | Source (senior / my attempt) | Round | Question | Topic |
|---|---|---|---|---|
| | | | | |
| | | | | |
| | | | | |

---

## 7. Corrections to make to `00-process-and-oa.md`

```
□
□
```


---

## 8. The 30-second pre-test refresh ⭐

```
□ CONSTRAINTS FIRST. n≤20 → bitmask. n≤500 → O(n³). n≤10⁶ → O(n log n). n≤10¹⁸ → binary search
□ Partial scoring is common → submit a working brute force before optimising
□ "Minimise the maximum" → binary search on the answer, almost always
□ mod 1e9+7 appears → combinatorics or DP; have nCr mod p ready
□ Hand-work the examples BEFORE coding — the observation lives there
□ long long everywhere; watch overflow in intermediate products
□ My advantage: real graph-algorithm implementation experience. Say so if graphs come up
```
