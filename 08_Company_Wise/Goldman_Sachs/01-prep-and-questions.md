
# Goldman Sachs — Prep Plan, Question Bank and Debriefs

---

## 1. Topic priorities ⭐⭐

| Priority | Topic | Source | Status |
|---|---|---|---|
| **P1** | **CS fundamentals breadth** — DS, OS, DBMS, networks, OOP, security basics | `../../06_Online_Assessments/05_MCQ_Core_CS_Banks/` | ☐ |
| **P1** | **HireVue practice** ⚠️ — record yourself, 5 behavioural answers | `../../07_Interviews/04_HR_and_Behavioral/` | ☐ |
| **P1** | **"Why finance?"** rehearsed | `00-process-and-oa.md` §4 | ☐ |
| **P1** | DSA Easy-Medium; the five stock-problem variants ⭐ | `../../01_DSA/` | ☐ |
| **P2** | Aptitude — percentages, ratios, TSD, probability | `../../06_Online_Assessments/02_Aptitude_and_Quant/` | ☐ |
| **P2** | Logical reasoning | `../../06_Online_Assessments/03_Logical_Reasoning_and_Puzzles/` | ☐ |
| **P3** | Basic market vocabulary | see §3 | ☐ |

---

## 2. The two-week plan ⭐⭐⭐

| Date | Task | Done |
|---|---|---|
| D-14 | Research protocol; **confirm whether HireVue is in the process** ⚠️ | ☐ |
| D-13 | CS fundamentals: DS and complexity MCQ bank | ☐ |
| D-12 | OS MCQ bank | ☐ |
| D-11 | DBMS MCQ bank | ☐ |
| D-10 | Networks MCQ bank + security basics (hashing vs encryption, symmetric vs asymmetric, TLS) | ☐ |
| D-9 | **The five stock-problem variants, all written from memory** ⭐ | ☐ |
| D-8 | DSA: 4 Easy-Medium problems | ☐ |
| D-7 | **HireVue practice: record 5 answers, watch them back** ⭐⭐ | ☐ |
| D-6 | Aptitude: percentages, ratios, TSD, probability — 30 timed questions | ☐ |
| D-5 | Logical reasoning: series, seating, syllogisms — 20 timed questions | ☐ |
| D-4 | **Rehearse "why finance?" and "why Goldman?" aloud** ⭐; re-record one HireVue answer | ☐ |
| D-3 | **Mock: CS rapid-fire + 1 coding problem** | ☐ |
| D-2 | Read about SecDB; GS Engineering blog; prepare 2 questions | ☐ |
| D-1 | Pre-interview routine only. Nothing new ⚠️ | ☐ |

---

## 3. Seeded question bank

### CS fundamentals (the OA's distinctive section) ⭐⭐
```
DATA STRUCTURES : complexity tables for array/list/hash/BST/heap; stable vs in-place sorts;
                  comparison-sort lower bound; when a hash table degrades to O(n)
OS              : process vs thread, deadlock (four conditions), scheduling, virtual memory,
                  paging, thrashing, page replacement and Belady's anomaly
DBMS            : normalisation, ACID, isolation levels, indexing, joins, transactions
NETWORKS        : OSI layers, TCP vs UDP, three-way handshake, HTTP status classes, DNS,
                  what happens when you type a URL
OOP             : four pillars, overloading vs overriding, abstract vs interface, SOLID
SECURITY ⭐      : hashing vs encryption (one-way vs reversible), symmetric vs asymmetric,
                  what TLS actually establishes, salting, SQL injection, why you never
                  store plaintext passwords
```

### Coding — the stock-problem family ⭐⭐⭐
```
Know all five variants cold; Goldman asks this family disproportionately:
  1. Best time to buy and sell — ONE transaction        (track the running min)
  2. Unlimited transactions                             (sum every positive delta)
  3. At most TWO transactions                           (DP with 4 states)
  4. At most K transactions                             (DP over k)
  5. With a cooldown / with a transaction fee           (state-machine DP)
```

### Other coding
```
- Arrays, strings, hash maps, sorting, greedy
- Intervals: merge, meeting rooms
- Simple DP: coin change, LIS, house robber
- Trees and graphs: traversals, BFS/DFS
- Financial wrappers: portfolio rebalancing, order matching, interest/compounding
- "Implement a simple matching engine" (occasionally, as a longer problem)
```

### HireVue video round ⚠️⭐⭐
```
FORMAT : a prompt appears, ~30 seconds to prepare, 2-3 minutes to answer, recorded, often
         one take only.

THE QUESTIONS ARE PREDICTABLE:
  □ Tell me about yourself
  □ Why Goldman Sachs? Why technology in finance?        ⚠️ prepare properly
  □ Tell me about a time you worked in a team
  □ Tell me about a challenge you overcame
  □ Tell me about a time you failed
  □ Describe a time you had to explain something technical to a non-technical person ⭐ TA story
  □ Where do you see yourself in five years?

HOW TO PRACTISE ⭐:
  □ Record yourself on your phone. Answer each in 2 minutes. WATCH IT BACK.
  □ Look at the CAMERA, not the screen — it reads as eye contact
  □ Cut filler: "basically", "actually", "kind of", "you know"
  □ Structure: STAR, but compressed. Context in two sentences, then the action
  □ Smile at the start. On video, neutral reads as flat
  □ Good lighting from the front; plain background; test your microphone
  □ Finish before the timer rather than being cut off mid-sentence
```

### Behavioural / fit
```
- Why finance? Why Goldman specifically?     ⚠️ prepared answer in 00-process-and-oa.md §4
- Why is your M.Tech CGPA 7.5?
- Why Mechanical → CS?
- Tell me about a time correctness mattered more than speed  ⭐ strong fit — the Soot
  validation story and the Wilcoxon testing story both work
- How do you explain technical work to someone without the background?  → the TA role ⭐
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
□ "Why finance?" → the CONSTRAINT, not the industry: a wrong number has an immediate cost,
  so the correctness standard is higher. Evidence: exact validation vs Soot; Wilcoxon testing
□ The five stock-problem variants
□ Security: hashing is one-way, encryption is reversible; symmetric = one shared key,
  asymmetric = keypair; TLS establishes a symmetric session key after asymmetric auth
□ HireVue: look at the CAMERA, smile at the start, finish before the timer
□ My communication evidence: I TA Advanced DS&A
```
