
# Google — Prep Plan, Question Bank and Debriefs

---

## 1. Topic priorities ⭐⭐

| Priority | Topic | Source | Status |
|---|---|---|---|
| **P1** | **DP** ⚠️⚠️ — 1-D, knapsack, 2-D, tree DP, bitmask DP | `../../01_DSA/` | ☐ |
| **P1** | **Binary search on the answer** ⭐ — until automatic | `../../01_DSA/` | ☐ |
| **P1** | **The constraints→complexity table**, memorised | `00-process-and-oa.md` §3 | ☐ |
| **P1** | **Live coding in a plain document** — no autocomplete ⚠️ | `../../07_Interviews/01_Technical_Round_Prep/00-Interview_Protocol.md` | ☐ |
| **P2** | Graphs — Dijkstra, 0-1 BFS, topological sort, union-find, MST | `../../01_DSA/` | ☐ |
| **P2** | Greedy with an exchange argument | `../../01_DSA/` | ☐ |
| **P2** | Combinatorics + modular arithmetic (nCr mod p, inclusion-exclusion) | `../../06_Online_Assessments/02_Aptitude_and_Quant/Probability_and_Combinatorics.md` | ☐ |
| **P3** | Segment tree / BIT | `../../01_DSA/` | ☐ |
| **P3** | Googleyness STAR stories | `../../07_Interviews/04_HR_and_Behavioral/02-STAR_Story_Bank.md` | ☐ |

⚠️ **This is the one company on your list where the preparation is genuinely front-loaded on a gap.**
Eight weeks is a realistic figure, not two. If Google's date is close and DP is still weak, prepare
for it anyway (the work transfers) but set your expectations accordingly.

---

## 2. The plan — eight weeks, not two ⭐⭐⭐

| Week | Focus |
|---|---|
| **1** | Complexity intuition; the constraints table; prefix sums, two pointers, sorting/greedy |
| **2** | **Binary search on the answer** — 15 problems until it is automatic ⭐ |
| **3** | Graphs: BFS/DFS, Dijkstra, 0-1 BFS, topological sort, union-find |
| **4** | **DP part 1** ⚠️: 1-D, knapsack family, LIS, coin change, interval DP |
| **5** | **DP part 2** ⚠️: 2-D, tree DP, bitmask DP, digit DP |
| **6** | Number theory + combinatorics with modular arithmetic |
| **7** | **Kick Start archive rounds, timed** ⭐ — the closest available proxy |
| **8** | Two full 90-minute 2-problem mocks + live-coding practice in a plain document |

**The final two weeks, dated:**

| Date | Task | Done |
|---|---|---|
| D-14 | Research protocol; confirm the role and the round structure | ☐ |
| D-13 | Kick Start round, timed, 3 hours | ☐ |
| D-12 | Review every problem you failed; write the correct solution out | ☐ |
| D-11 | DP: 3 bitmask problems | ☐ |
| D-10 | Binary search on the answer: 3 problems | ☐ |
| D-9 | **Live-coding practice in Google Docs** — 2 problems, no IDE ⭐ | ☐ |
| D-8 | Kick Start round, timed | ☐ |
| D-7 | Graphs: Dijkstra with a state dimension; 0-1 BFS | ☐ |
| D-6 | Combinatorics: nCr mod p, inclusion-exclusion | ☐ |
| D-5 | **Mock: 2 problems in 90 min, narrating aloud throughout** ⭐⭐ | ☐ |
| D-4 | Review; redo the two weakest DP patterns | ☐ |
| D-3 | **Mock: live coding in a plain doc with someone watching** ⭐ | ☐ |
| D-2 | Googleyness stories; 2 questions to ask | ☐ |
| D-1 | Pre-interview routine only. Re-read the constraints table. Nothing new ⚠️ | ☐ |

---

## 3. Seeded question bank

### The seven-step attack method ⭐⭐⭐
```
1. Read the CONSTRAINTS first (30 s)        → narrows the solution family immediately
2. Read the statement twice (2 min)         → write the ask in one sentence
3. Work the given examples BY HAND (3 min)  → this is where the observation appears
4. Construct your own tiny example (2 min)  → n=1, n=2, all-equal, extremes
5. State a brute force + its complexity     → always have a codeable fallback
6. Look for the property that collapses it  → monotonicity, invariant, optimal substructure,
                                               exchange argument, symmetry
7. Code it, then test the degenerate cases
```
⚠️ **Do not start coding before step 4.** Twenty minutes coding the wrong idea is a lost problem.

### Recurring Google problem shapes
```
"You can do operation X at most k times; maximise/minimise Y"
    → binary search on the answer, or DP with k as a dimension
"Count the number of ways / subsequences / paths, mod 1e9+7"
    → combinatorics or DP
"Given a grid and a rule, find the minimum cost to reach the end"
    → BFS / Dijkstra / DP with augmented state
"Partition the array so that some quantity is balanced"
    → binary search on the answer, or DP
"Find the k-th smallest/largest something"
    → heap, binary search on value, or order statistics
```

### DP problems to have done ⚠️⭐⭐⭐
```
1-D          : climbing stairs variants, house robber (+circular), coin change, LIS (n log n),
               max subarray, decode ways, word break
KNAPSACK     : 0/1, unbounded, subset sum, partition equal subset, target sum
2-D          : edit distance, LCS, longest common substring, grid paths with obstacles,
               unique paths, minimum path sum
INTERVAL     : matrix chain multiplication, burst balloons, palindrome partitioning
TREE DP      : diameter, max path sum, house robber III, independent set
BITMASK      : TSP, assignment problem, minimum subset cover, count Hamiltonian paths
STATE MACHINE: best time to buy/sell stock — all five variants ⭐
DIGIT DP     : count numbers with a property up to N
```

### Live-coding specifics ⚠️⭐⭐
```
□ Narrate CONTINUOUSLY — silence is scored badly at Google more than anywhere else
□ Ask clarifying questions about input size, duplicates, invalid input BEFORE coding
□ State the complexity before and after coding
□ Write in a plain text editor with no syntax highlighting at least FIVE times before
  the interview. It is startlingly harder than an IDE.  ⭐
□ Take hints explicitly and extend them
```

### Googleyness
```
- A time you worked with someone difficult
- A time you had to make progress with ambiguous requirements  ⭐ your M.Tech project
- A time you changed your mind based on new information
- A time you helped someone else succeed  → the TA role ⭐
- How do you decide what to work on when everything seems important?
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

## 8. The 30-second pre-interview refresh ⭐

```
□ CONSTRAINTS FIRST, always. n≤20 → bitmask. n≤10⁶ → O(n log n). n≤10¹⁸ → binary search/maths
□ Hand-work every provided example before coding — the observation lives there
□ Keep a brute force in reserve; code it if 25 minutes remain with no idea
□ NARRATE CONTINUOUSLY — silence costs more here than anywhere
□ long long overflow; modular arithmetic; n = 0/1 edge cases
□ My advantage: I have implemented graph algorithms from first principles — say so
```
