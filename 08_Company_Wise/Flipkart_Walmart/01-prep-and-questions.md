
# Flipkart / Walmart — Prep Plan, Question Bank and Debriefs

---

## 1. Topic priorities ⭐⭐

| Priority | Topic | Source | Status |
|---|---|---|---|
| **P1** | **Machine coding** ⚠️⚠️ — build 4 working systems from scratch, timed | `../../04_System_Design/02_LLD_OOD/machine-coding-guide.md` | ☐ |
| **P1** | OOP + SOLID in practice | `../../04_System_Design/02_LLD_OOD/solid-in-practice.md` | ☐ |
| **P1** | DSA Medium — arrays, strings, hashing, trees, graphs | `../../01_DSA/` | ☐ |
| **P2** | Design patterns — strategy, factory, observer, decorator, singleton | `../../04_System_Design/04_Design_Patterns/` | ☐ |
| **P2** | **DP** ⚠️ | `../../01_DSA/` | ☐ |
| **P2** | HLD basics for the HM round — caching, sharding, queues | `../../04_System_Design/03_HLD_Case_Studies/` | ☐ |
| **P3** | Core CS fundamentals | `../../06_Online_Assessments/05_MCQ_Core_CS_Banks/` | ☐ |

⚠️ **The machine-coding round is P1 and it is unpractised for you.** Everything else on this list
you have some foundation for; that one you do not. Allocate accordingly.

---

## 2. The two-week plan ⭐⭐⭐

| Date | Task | Done |
|---|---|---|
| D-14 | Research protocol; **confirm the machine-coding language and whether code must RUN** ⚠️ | ☐ |
| D-13 | Read the LLD framework + SOLID in practice; study one worked case study fully | ☐ |
| D-12 | **Machine coding #1: parking lot, 90 min, timed, working code** ⭐ | ☐ |
| D-11 | Review #1 against the case study; note what you over- or under-engineered | ☐ |
| D-10 | DSA: 3 Medium problems | ☐ |
| D-9 | **Machine coding #2: Splitwise-style expense sharing, 90 min** ⭐ | ☐ |
| D-8 | Design patterns: strategy, factory, observer, decorator — with small code examples | ☐ |
| D-7 | DSA: 3 problems. DP ⚠️ | ☐ |
| D-6 | **Machine coding #3: in-memory key-value store with TTL, 60 min** ⭐ | ☐ |
| D-5 | HLD: read two case studies; practise the estimation numbers | ☐ |
| D-4 | **Machine coding #4, with someone reviewing the code afterwards** ⭐⭐ | ☐ |
| D-3 | DSA: 3 problems; OOP rapid-fire | ☐ |
| D-2 | Project 60-second versions framed around engineering trade-offs; 2 questions | ☐ |
| D-1 | Pre-interview routine only. Nothing new ⚠️ | ☐ |

⭐ **Four timed machine-coding builds is the single highest-value block in this plan.** The skill is
pacing and scoping: deciding in the first ten minutes what you will *not* build.

---

## 3. Seeded question bank

### Machine coding — the method ⭐⭐⭐
```
MINUTE 0-10   CLARIFY AND SCOPE
              - List the required features out loud. Confirm what is OUT of scope.
              - Decide explicitly: no DB, no UI, no auth, in-memory only (unless asked)
              - Name the 4-6 core entities

MINUTE 10-20  DESIGN ON PAPER
              - Entities, their fields, and their responsibilities
              - The 2-3 interfaces where behaviour will vary (pricing strategy, allocation
                policy, notification channel) ⭐ this is where SOLID earns its marks
              - The service/manager class that orchestrates

MINUTE 20-75  CODE IT, BOTTOM UP
              - Entities first, then the interfaces, then the service, then main()
              - Compile/run EARLY and OFTEN ⚠️ do not write 500 lines then compile
              - Keep it in one file or a few files — build tooling is not graded

MINUTE 75-90  DEMONSTRATE AND TIDY
              - A main() that exercises every required flow
              - Basic validation and error handling
              - Be ready for "now add feature X" — your interfaces should make it a small change ⭐
```

⚠️ **The two failure modes:** (1) designing for 40 minutes and producing code that does not run;
(2) building a UI/DB/framework nobody asked for and running out of time on the core flows.

### Machine-coding problems to practise
```
□ Parking lot        — levels, spot types, pricing strategy, ticketing  ⭐ the canonical one
□ Splitwise          — users, groups, expenses, equal/exact/percentage splits, balance settlement
□ Library system     — books, members, borrowing, due dates, fines
□ Elevator           — multiple lifts, request scheduling, direction state
□ In-memory KV store — get/put/delete, TTL expiry, LRU eviction
□ Logging framework  — levels, pluggable sinks (console/file), formatters
□ Snake and ladder   — board, dice, players, turn order
□ Cab booking        — riders, drivers, matching strategy, trip lifecycle
```

### DSA (Medium)
```
Arrays and strings · hashing (subarray sum = k, group anagrams, top-K) ·
sliding window · trees (traversals, LCA, validate BST) · graphs (BFS/DFS, topological sort) ·
heaps (merge K, running median) · intervals · DP (coin change, LIS, edit distance) ·
LRU cache · monotonic stack
```

### OOP and patterns
```
□ Four pillars; overloading vs overriding; abstract class vs interface
□ SOLID — all five, with Liskov and the Rectangle/Square example ⭐
□ Composition over inheritance, and why
□ Strategy (pricing, allocation), Factory (object creation), Observer (notifications),
  Decorator (layered behaviour), Singleton (and why it is often a smell)
```

### HLD (hiring-manager round)
```
□ Design a URL shortener / rate limiter / notification service
□ Caching: where, what to evict, cache invalidation
□ Sharding and partitioning; consistent hashing
□ Message queues and asynchronous processing
□ The CAP trade-off, stated honestly rather than as a slogan
□ Back-of-envelope estimation ⭐ — ../../04_System_Design/estimation-numbers.md
```

### Behavioural
```
- Why Flipkart / Walmart?
- Tell me about an engineering trade-off you made and why  ⭐ your Near/Far worklist story
- Why no industry internship?  ⚠️
- A time you had to ship something imperfect because of a deadline
- Why Mechanical → CS?
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
MACHINE CODING
□ Scope OUT loud in the first 10 minutes: no DB, no UI, no auth, in-memory
□ Identify the 2-3 places behaviour VARIES → those become interfaces (strategy pattern)
□ Compile and run EARLY. Working code beats elegant non-running code
□ main() must exercise every required flow
□ Expect "now add feature X" — that is the extensibility test

DSA / HM
□ Frame projects around TRADE-OFFS, not compiler content
□ My trade-off story: per-bucket arrays → Near/Far worklist, for O(|V|) memory
  independent of graph diameter
```
