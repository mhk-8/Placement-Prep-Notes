
# Microsoft — Prep Plan, Question Bank and Debriefs

---

## 1. Topic priorities ⭐⭐

| Priority | Topic | Source | Status |
|---|---|---|---|
| **P1** | **Edge-case discipline** — write 4 tests before optimising | `../../06_Online_Assessments/01_OA_Patterns_by_Company/Microsoft.md` §2 | ☐ |
| **P1** | Strings — parse, transform, compress, validate | `../../01_DSA/` | ☐ |
| **P1** | **Linked lists** ⭐ — reverse in k-groups, cycle, merge, LRU cache | `../../01_DSA/` | ☐ |
| **P1** | Arrays with twists; hash map + frequency | `../../01_DSA/` | ☐ |
| **P2** | Precise simulation — read a long spec, implement exactly | see §3 | ☐ |
| **P2** | Trees and BST; stacks; matrix traversal | `../../01_DSA/` | ☐ |
| **P2** | **DP** ⚠️ — edit distance, LCS, coin change | `../../01_DSA/` | ☐ |
| **P2** | ⚠️ **Hand-writing code on paper**, if Group Fly runs | see §3 | ☐ |
| **P3** | CS fundamentals MCQ (some variants) | `../../06_Online_Assessments/05_MCQ_Core_CS_Banks/` | ☐ |
| **P3** | Design for the AA round | `../../04_System_Design/` | ☐ |

---

## 2. The two-week plan ⭐⭐⭐

| Date | Task | Done |
|---|---|---|
| D-14 | Research protocol; **check whether Group Fly runs** ⚠️; do Codility's demo task | ☐ |
| D-13 | Linked lists: reverse in k-groups, cycle detect+remove, merge, reorder — from memory | ☐ |
| D-12 | LRU cache from memory, twice | ☐ |
| D-11 | Strings: 4 problems. Parse/transform/compress | ☐ |
| D-10 | **Edge-case drill:** take 5 solved problems and add empty/single/all-equal/max tests ⭐ | ☐ |
| D-9 | Arrays with twists: 4 problems | ☐ |
| D-8 | Trees: validate BST, LCA, level order, right side view | ☐ |
| D-7 | DP ⚠️: edit distance, LCS, coin change | ☐ |
| D-6 | **Paper-coding session** if Group Fly applies — 2 full programs by hand ⭐ | ☐ |
| D-5 | Simulation problems: 2 long-spec problems implemented exactly | ☐ |
| D-4 | **Mock: 3 problems in 90 min on Codility, with custom tests run** ⭐ | ☐ |
| D-3 | CS fundamentals rapid-fire; C++ depth | ☐ |
| D-2 | Project 60-second versions; 2 questions to ask | ☐ |
| D-1 | Pre-interview routine only. Nothing new ⚠️ | ☐ |

---

## 3. Seeded question bank

### Coding (Medium)
```
LINKED LISTS ⭐ (over-represented at Microsoft)
  □ Reverse a linked list — iterative and in k-groups
  □ Detect and REMOVE a cycle (Floyd, then find the entry point)
  □ Merge two / k sorted lists
  □ Reorder list; palindrome check; intersection of two lists
  □ LRU cache (hash map + doubly linked list)   ⭐ write from memory
  □ Copy a list with random pointers

STRINGS
  □ Longest palindromic substring; longest substring without repeats
  □ String compression; remove adjacent duplicates
  □ Valid parentheses variants; expression evaluation
  □ Format/parse conversions (version numbers, IP addresses, roman numerals)

ARRAYS
  □ Rotate array; rearrange by sign/parity
  □ Missing / duplicate number (cyclic sort, XOR, Floyd)
  □ Max/min after k operations
  □ Container with most water; trapping rain water

TREES / MATRIX / STACK
  □ Validate BST; LCA; level order; diameter
  □ Spiral order; rotate image; set matrix zeroes
  □ Next greater element; largest rectangle in histogram

SIMULATION ⭐ the Microsoft signature
  □ "A robot/machine follows these rules for N steps — report the final state"
  □ "Apply this text-formatting specification exactly"
  Method: re-read the statement twice, write the rules as numbered comments,
          implement one rule per line. Do NOT code from memory of the first read.
```

### Edge cases to test on every problem ⭐⭐⭐
```
□ empty input           □ single element        □ all elements identical
□ maximum constraint    □ negative numbers      □ integer overflow (int vs long long)
□ already sorted / reverse sorted               □ duplicates
```
⚠️ Most lost marks at Microsoft are edge-case marks, not algorithm marks.

### Group Fly (if it runs) ⭐
```
Approximate marking: correct approach ~40% · complete compilable-looking code ~30% ·
edge cases ~20% · readability and complexity annotation ~10%

□ Write the signature, then the approach in two lines, then the code
□ STATE THE TIME AND SPACE COMPLEXITY at the bottom — graders look for it  ⭐
□ Handle null/empty explicitly, even in one line
□ Legible, blank lines between blocks, single-line strike-through for corrections
```

### CS fundamentals (some variants)
```
OS · DBMS · networks · OOP · C/C++ output prediction
→ ../../06_Online_Assessments/05_MCQ_Core_CS_Banks/
```

### AA round
```
- A harder DSA problem, or a design question
- Deep project questioning
- "Tell me about a time you disagreed with your manager/guide"
- "What would you change about how you worked on your M.Tech project?"
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
□ Codility: Correctness + Performance scored SEPARATELY → always submit something correct
□ Run the four custom tests: empty, single, all-equal, maximum size
□ mid = lo + (hi - lo)/2 ; long long for sums
□ Linked lists are over-represented — LRU cache and reverse-in-k-groups from memory
□ Simulation problems: numbered-comment the rules before coding
□ Annotate complexity in a comment; it costs nothing
```
