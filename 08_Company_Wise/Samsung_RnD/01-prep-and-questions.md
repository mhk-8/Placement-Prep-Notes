
# Samsung R&D — Prep Plan, Question Bank and Debriefs

---

## 1. Topic priorities ⭐⭐

| Priority | Topic | Source | Status |
|---|---|---|---|
| **P1** | **BFS/DFS with augmented state** — the signature pattern | `../../01_DSA/` graphs | ☐ |
| **P1** | **Bitmask DP** (n ≤ 20) ⚠️ your gap | `../../01_DSA/` DP | ☐ |
| **P1** | Backtracking with pruning — N-queens, sudoku, subset/permutation generation | `../../01_DSA/` | ☐ |
| **P1** | **Hand-rolled containers** if STL is restricted ⚠️ verify first | see §3 | ☐ |
| **P2** | Simulation discipline — reading a long spec and implementing it exactly | `../../06_Online_Assessments/01_OA_Patterns_by_Company/Microsoft.md` §3 | ☐ |
| **P2** | Core CS: OS, DBMS, networks (interview round) | `../../06_Online_Assessments/05_MCQ_Core_CS_Banks/` | ☐ |
| **P2** | C/C++ depth | `../../07_Interviews/01_Technical_Round_Prep/03-CS_Fundamentals_Rapid_Fire.md` §6 | ☐ |
| **P3** | Aptitude, if the track includes it | `../../06_Online_Assessments/02_Aptitude_and_Quant/` | ☐ |

---

## 2. The two-week plan ⭐⭐⭐

| Date | Task | Done |
|---|---|---|
| D-14 | Research protocol; **verify the STL restriction** ⚠️; confirm which team/track | ☐ |
| D-13 | BFS with state: 3 problems (keys-and-doors, portals, limited moves) | ☐ |
| D-12 | Bitmask DP: read the theory, then 3 problems ⚠️ | ☐ |
| D-11 | Backtracking: N-queens, word search, subset sum with pruning | ☐ |
| D-10 | **If STL restricted:** hand-write a dynamic array, queue, min-heap and quicksort in C++ | ☐ |
| D-9 | One full 3-hour timed session on a single hard problem ⭐ | ☐ |
| D-8 | More BFS/DFS with state; Dijkstra with an extra state dimension | ☐ |
| D-7 | Bitmask DP: 3 more problems (TSP-style, assignment, subset cover) | ☐ |
| D-6 | Core CS: OS and DBMS MCQ banks | ☐ |
| D-5 | **Second full 3-hour timed session** ⭐ | ☐ |
| D-4 | Networks MCQ bank; C++ rapid-fire | ☐ |
| D-3 | Mock: one hard problem with someone watching | ☐ |
| D-2 | SSSP project 5-minute version; prepare the CGPA answer; 2 questions to ask | ☐ |
| D-1 | Pre-interview routine only. Nothing new ⚠️ | ☐ |

⭐ **The two three-hour sessions are the most important items on this list.** The format is unusual
and the main failure mode is pacing — people rush the first hour and then have no time to handle
edge cases. Practise the format, not just the topics.

---

## 3. Seeded question bank

### The coding test — problem shapes to drill ⭐⭐⭐
```
GRID BFS WITH STATE
  - Shortest path collecting all keys, where doors need matching keys  → state (r, c, keymask)
  - Grid with teleporters, one-way cells, or a limited number of wall-breaks
  - Minimum moves with a direction component → state (r, c, dir)
  - Multi-source BFS (fire spreading, rotting oranges at scale)
  - Shortest path with a fuel/budget dimension → state (r, c, fuel)

BITMASK DP
  - Visit all N targets at minimum cost (TSP-style, N ≤ 20)
  - Assignment problem — N tasks to N workers
  - Minimum number of subsets covering a set
  - Count ways to tile / partition with constraints

BACKTRACKING WITH PRUNING
  - Place K items on a grid subject to adjacency constraints
  - Partition into groups with balanced sums
  - Generate all valid configurations and pick the best

SIMULATION
  - A machine/robot follows a long rule set for N steps; report the final state
  - Multiple entities moving with collision rules
```

⭐ **The method for every one of these:** before coding, write down the **state** in one line and
the **transition** in one line. If the state is only `(row, col)` and the problem mentions keys,
fuel, direction or steps-remaining, your state is wrong.

### If the STL is restricted ⚠️
```
Be able to hand-write, in C++, in under 10 minutes each:
  □ a dynamic array (push_back with doubling)
  □ a queue (circular buffer)
  □ a min-heap (push, pop, sift up/down)
  □ quicksort and merge sort
  □ a simple hash map (open addressing or chaining)
  □ a struct-based linked list
```

### Technical interview — DSA
```
- Standard Medium problems; graphs and DP weighted
- Reverse a linked list; detect a cycle
- Binary tree traversals, LCA, diameter
- Implement an LRU cache
- Design-ish: "how would you store and query X efficiently?"
```

### Core CS
```
OS      : process vs thread, deadlock, scheduling, virtual memory, paging, thrashing
DBMS    : normalisation, ACID, isolation levels, indexing, joins
Networks: OSI layers, TCP vs UDP, three-way handshake, HTTP, DNS
C/C++   : pointers, virtual destructor, RAII, memory management, output prediction
```

### Project deep dive
```
- "Walk me through the Δ-stepping implementation."  ⭐ lead here
- "Why does Δ-stepping parallelise when Dijkstra doesn't?"
- "What was your worklist design and why?"
- "How did you handle duplicate enqueues?"
- "What's the memory bound and why does it matter?"
```

### Behavioural
```
- Why Samsung? Which team and why?
- Why is your M.Tech CGPA 7.5?  ⚠️ likely here
- Why Mechanical → CS?
- Tell me about a problem that took you a long time to solve
- Are you comfortable relocating to Bengaluru / Noida?
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
□ THE SIGNATURE SHAPE: BFS/DFS or DP over an AUGMENTED state. Write the state and the
  transition down before coding.
□ If the state is only (r, c) and the problem mentions keys/fuel/direction/steps → state is wrong
□ Bitmask DP when n ≤ 20
□ Three hours: spend the first 20 minutes on examples and state design, not on code
□ Reserve the last 30 minutes for edge cases — that is what the format rewards
□ My lead project here is the SSSP one (graph traversal built from first principles)
```
