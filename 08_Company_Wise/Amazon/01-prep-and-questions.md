
# Amazon — Prep Plan, Question Bank and Debriefs

---

## 1. Topic priorities ⭐⭐

| Priority | Topic | Source | Status |
|---|---|---|---|
| **P1** | **Leadership Principle stories** — 8 STAR stories mapped to LPs ⚠️ your gap | `../../07_Interviews/04_HR_and_Behavioral/02-STAR_Story_Bank.md` | ☐ |
| **P1** | Binary search on the answer ⭐ (ship packages in D days) | `../../01_DSA/` | ☐ |
| **P1** | Heaps / top-K ⭐ (K closest points to origin) | `../../01_DSA/` | ☐ |
| **P1** | Hash map + counting; two pointers / sliding window | `../../01_DSA/` | ☐ |
| **P1** | Grid BFS/DFS — islands, rotting oranges, shortest path | `../../01_DSA/` | ☐ |
| **P2** | **DP** ⚠️ your gap — 1-D and simple 2-D | `../../01_DSA/` | ☐ |
| **P2** | Trees, tries, union-find, monotonic stack | `../../01_DSA/` | ☐ |
| **P2** | LLD — design a class hierarchy and code it | `../../04_System_Design/02_LLD_OOD/` | ☐ |
| **P3** | The debugging section — bug families | `../../06_Online_Assessments/01_OA_Patterns_by_Company/Amazon.md` §4 | ☐ |
| **P3** | HLD basics for the loop | `../../04_System_Design/03_HLD_Case_Studies/` | ☐ |

⚠️ **The counter-intuitive priority:** at Amazon, behavioural preparation is P1, not P3. Your STAR
bank has `[FILL IN]` gaps and a weak leadership/conflict story. Fix that before adding more DSA.

---

## 2. The two-week plan ⭐⭐⭐

| Date | Task | Done |
|---|---|---|
| D-14 | Research protocol; read the 16 Leadership Principles properly | ☐ |
| D-13 | **Fill in the STAR bank** — all six stories, with real specifics ⚠️ | ☐ |
| D-12 | Map each story to 2-3 LPs; write the LP table in `00-process-and-oa.md` §4 | ☐ |
| D-11 | Binary search on the answer: 5 problems until automatic ⭐ | ☐ |
| D-10 | Heaps / top-K: 5 problems. Write "K closest points" from memory | ☐ |
| D-9 | Grid BFS/DFS: 4 problems | ☐ |
| D-8 | Hash map + sliding window: 5 problems | ☐ |
| D-7 | DP ⚠️: coin change, house robber, LIS, grid paths | ☐ |
| D-6 | **Rehearse 4 LP stories aloud, timed at 2 minutes each** ⭐ | ☐ |
| D-5 | Debugging drills: introduce bugs into your own old code and fix them fast | ☐ |
| D-4 | **Mock: 2 problems in 70 minutes, brute force submitted first** ⭐ | ☐ |
| D-3 | LLD: design and code a parking lot / library / rate limiter | ☐ |
| D-2 | Amazon Builders' Library — one article; prepare 2 questions | ☐ |
| D-1 | Pre-interview routine only. Re-read the LP table. Nothing new ⚠️ | ☐ |

---

## 3. Seeded question bank

### The highest-frequency coding problems ⭐⭐⭐
```
MUST be writable from memory in under 6 minutes each:
  □ Minimum capacity to ship packages within D days  (binary search on the answer)  ⭐⭐
  □ K closest points to origin  (heap, or quickselect)                               ⭐⭐
  □ Number of islands / rotting oranges  (grid BFS/DFS)
  □ Two sum / subarray sum = k  (hash map, prefix sums)
  □ Longest substring without repeating characters  (sliding window)
  □ Merge intervals / meeting rooms
  □ Top K frequent elements
  □ LRU cache
  □ Search suggestions system  (trie)
  □ Word ladder  (BFS)
  □ Course schedule  (topological sort)
```

### Amazon-flavoured wrappers
```
The story changes; the pattern does not. Recognise these:
  "fulfilment centre / packages / trucks"   → greedy or binary search on the answer
  "server load / cluster / tasks"           → heap or prefix sums
  "product search / suggestions"            → trie, or sorted array + binary search
  "reviews / ratings"                       → top-K, running median
  "order / transaction log parsing"         → string parsing + hash map
  "prime subscribers / delivery routes"     → graph
```

### Debugging section (6-7 snippets, ~2 min each)
```
Bug families: off-by-one in loop bounds · = vs == · wrong variable in nested loops ·
max initialised to 0 when values can be negative · missing or misplaced return ·
integer division · inverted condition · string index out of range

⭐ Method: do not read it as prose. Check loop bounds, comparison operators and
   initialisation FIRST — that covers about 80% of planted bugs.
```

### Work Style Assessment
```
Forced-choice statements mapped to the LPs. It IS scored.
⭐ Answer honestly but lean toward Ownership, Bias for Action, Dive Deep, Customer Obsession
  where the choice is genuinely close.
⚠️ There are consistency checks — maximising every trait looks incoherent.
```

### Work Simulation
```
A simulated inbox: emails, a dashboard, a chat thread. Pick most/least effective responses.
Heuristics that score well:
  1. Look at the DATA before acting
  2. Communicate proactively with the affected stakeholder
  3. Fix the ROOT CAUSE, not the symptom
  4. Prefer the customer-impacting fix over the internally convenient one
  5. Don't throw a colleague under the bus; don't silently take over their work — talk to them
  6. Escalate only after gathering facts and attempting a fix
```

### Behavioural — the questions to have stories for ⭐⭐⭐
```
□ Tell me about a time you took ownership of something outside your scope
□ Tell me about the most complex problem you've solved          → Dive Deep
□ Tell me about a time you had to make a decision with incomplete data → Bias for Action
□ Tell me about a time you failed                               ⚠️ must have one
□ Tell me about a time you disagreed with someone               ⚠️ your weakest
□ Tell me about a time you had to learn something quickly       → the Mechanical→CS switch ⭐
□ Tell me about a time you insisted on a higher standard
□ Tell me about a time you delivered under a tight deadline
□ Tell me about a time you simplified something
□ Tell me about a time you went beyond what was asked
```

⚠️ **Prepare at least six distinct stories.** Amazon interviewers across the loop compare notes,
and reusing one story for three questions is noticed.

### LLD / design (loop round)
```
- Design a parking lot / library system / elevator / vending machine, then CODE it
- Design a rate limiter
- Design an in-memory key-value store with TTL
- Emphasis: class design, SOLID, extensibility — and working code, not just a diagram
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
□ PARTIAL SCORING: submit a brute force FIRST, then optimise, then submit again
□ Read both coding problems in the first 3 minutes before writing anything
□ Every round is partly behavioural — have an LP story ready even in a DSA round
□ My strongest LPs: Dive Deep (points-to), Learn and Be Curious (the switch),
  Insist on Highest Standards (exact validation vs Soot)
□ My weakest LP: Have Backbone — do not pick that story unless asked directly
□ Remove debug prints before final submit; the grader looks at code quality
□ Do not leave the Work Style section blank — it is scored
```
