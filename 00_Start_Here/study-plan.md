# Study Plan — Sep 2026 to Placement Season

> Created 2026-09-17 · Owner: HK · M.Tech, IIT Madras
> **Verify the actual Phase-1 date with the placement cell and correct this file.** This plan assumes Phase 1 opens **Tue 1 Dec 2026**, with pre-placement talks, PPO interviews and off-campus OAs running from **mid-October**.

---

## 1. The calendar you are actually racing

| Milestone | Assumed date | Action deadline |
|---|---|---|
| Resume freeze / CDC portal upload | ~mid-Oct | Resume final by **10 Oct** |
| Off-campus + early OA window opens | ~20 Oct | OA-ready by **18 Oct** |
| Pre-placement talks begin | ~Nov | Company folders built by **1 Nov** |
| **Phase 1, Day 1** | **1 Dec** | Full readiness by **28 Nov** |
| Phase 1, Slots 1–3 | 1–3 Dec | Cheatsheet-only revision |
| Phase 2 | Jan 2027 | Buffer, not the plan |

**Working horizon: 10 full weeks (21 Sep – 29 Nov), plus a 4-day setup window now.**

That is roughly **550–650 focused hours** at 8–9 h/day on weekdays and 10 h on weekends, minus coursework and thesis load. Budget honestly: if thesis takes 4 h/day, the plan below compresses to its **Priority-1 rows only** and you drop CV, TOC and Verbal entirely.

---

## 2. Priority tiers — what to drop when time runs short

Not all topics are equal. When you fall behind, you cut from the bottom, never from the top.

**P1 — never cut (≈65% of time)**
DSA patterns 1–14 · OS · DBMS + SQL · OOP · one language to fluency · Project deep dives · Aptitude (if your targets test it) · Core-CS MCQ banks

**P2 — cut only in the last two weeks (≈25%)**
Computer Networks · LLD/OOD · Classical ML + DL + ML system design (P1 if you are targeting AI/ML roles — swap it with Aptitude) · Advanced DS · HR/behavioral scripts

**P3 — cut freely (≈10%)**
Computer architecture · Compilers/TOC · Computer vision · Verbal ability · HLD case studies (for fresher SDE) · Design patterns beyond the top six

**If you are targeting AI/ML roles**, re-tier: `05_AI_ML/01–05, 08, 09` move to P1; `04_System_Design/03_HLD` and half of `01_DSA/17_Advanced_DS` move to P3. DSA stays P1 regardless — AI/ML OAs still gate on coding.

---

## 3. Week 0 — setup (Thu 17 – Sun 20 Sep)

Do not start studying content this week. Build the machinery.

- [ ] Fill `target-companies.md` with your real shortlist and their formats
- [ ] Fill `syllabus-checklist.md` — mark what you already know at 4–5/5 confidence; that is your baseline
- [ ] Pick your **primary OA language** and commit (see `rules.md` §2). Write your template file with fast I/O
- [ ] Take one **diagnostic timed mock**: 90 min, 3 DSA problems (easy/medium/medium) + 20 core-CS MCQs. Log it in `Trackers/mock-scores.md`. The score is irrelevant; the failure modes are the point
- [ ] Set up your problem list — **one** platform, one curated list. Not five
- [ ] Resume v1 drafted into `07_Interviews/03_Resume_and_Portfolio/`

**Diagnostic output → this plan's calibration.** If you solved 0–1 of 3, follow the plan as written. If you solved 3 of 3 comfortably, compress Phase A to three weeks and move the freed time into system design and ML depth.

---

## 4. Phase A — Foundations (Weeks 1–4: 21 Sep – 18 Oct)

**Goal by 18 Oct: OA-capable.** You can clear a standard 2-problem, 90-minute OA with a medium difficulty ceiling.

| Week | Dates | DSA (pattern of the week) | Core CS | ML (if applicable) | Other |
|---|---|---|---|---|---|
| **W1** | 21–27 Sep | Complexity & math · Arrays/strings · Prefix sums · Hashing | OS: processes, threads, scheduling (+ numericals) | Math foundations: linear algebra, matrix calculus | Language fluency drills daily (30 min) |
| **W2** | 28 Sep – 4 Oct | Two pointers · Sliding window · Binary search (incl. on answer) | OS: concurrency, deadlock, memory/paging | Probability & statistics; MLE/MAP | **Resume final by 10 Oct** |
| **W3** | 5–11 Oct | Sorting · Recursion/backtracking · Stacks & monotonic stack | DBMS: ER, normalisation, transactions | Classical ML part 1: regression, regularisation, bias-variance | SQL query practice 3×/week |
| **W4** | 12–18 Oct | Linked list · Trees & BST · Heaps/priority queues | DBMS: indexes, concurrency; SQL joins + window functions | Classical ML part 2: trees, ensembles, SVM, clustering | OOP: four pillars + SOLID |

**Weekly load (Phase A):** 4 new DSA subtopics × ~6 problems = ~24 problems/week · 1 core-CS topic to full depth · 1 timed mock on Saturday from W2 onward.

**Gate to pass before Phase B:** 90-minute mock with 2 mediums solved unaided, and 15/20 on a core-CS MCQ set. If you fail the gate, repeat W4 — do not advance on schedule alone.

---

## 5. Phase B — Depth & Speed (Weeks 5–8: 19 Oct – 15 Nov)

**Goal by 15 Nov: interview-capable.** You can solve a medium in 25 minutes while narrating, and defend any line of your resume.

| Week | Dates | DSA | Core CS | ML (if applicable) | Interview work |
|---|---|---|---|---|---|
| **W5** | 19–25 Oct | Graphs I: BFS/DFS, components, grids, topological sort | CN: TCP/IP, TCP vs UDP, HTTP/HTTPS, DNS | Deep learning: backprop by hand, activations, optimisers | Project deep-dive file #1 written |
| **W6** | 26 Oct – 1 Nov | Graphs II: Dijkstra, MST, union-find, 0-1 BFS | CN: subnetting, routing; Architecture: cache, pipelining | CNNs / RNNs; regularisation & normalisation | Project deep-dive file #2; **company folders built** |
| **W7** | 2–8 Nov | DP I: 1-D, knapsack family, LIS | OOP deep dive + design patterns (top 6) | NLP & transformers; attention in full | LLD: parking lot + BookMyShow, under time |
| **W8** | 9–15 Nov | DP II: 2-D grid, string DP, interval, bitmask | Revision sweep: OS + DBMS numericals | LLMs: fine-tuning, RAG, evaluation; ML system design framework | Mock interview #1 with a peer |

**Weekly load (Phase B):** ~30 problems/week, of which at least 10 are **timed, cold, unhinted**. Two mocks per week from W7.

**Gate to pass before Phase C:** one DP-hard and one graph-medium solved unaided in under 35 min each, and a 45-minute mock interview where you narrated throughout without going silent for more than 30 seconds.

---

## 6. Phase C — Simulation & Company Targeting (Weeks 9–10: 16 – 29 Nov)

**Goal by 29 Nov: repeatable performance under pressure.** No new topics after 22 Nov.

| Week | Dates | Focus |
|---|---|---|
| **W9** | 16–22 Nov | Greedy · bit manipulation · intervals · tries · advanced DS (Fenwick/segment tree). Last new material. Company-specific OA patterns from `08_Company_Wise`. 3 timed mocks. 2 mock interviews. |
| **W10** | 23–29 Nov | **Zero new topics.** Daily: 1 timed mock OA (alternate SDE / ML) + 1 mock interview + 90 min from `10_Mistake_Log_and_Revision`. Build every file in `09_Cheatsheets`. HR answers rehearsed aloud, recorded once. |

**The W10 rule:** anything you cannot do by 23 Nov, you will not learn by 1 Dec. Convert that time into reliability on what you already know. A candidate who executes 70% of the syllabus flawlessly beats one who half-knows 100%.

---

## 7. Placement week (1 Dec onward)

- Night before: read **only** `09_Cheatsheets/night-before.md` and your mistake log's `recurring-patterns.md`. Stop by 22:30. Sleep is a performance input, not a luxury.
- Morning of: 20 minutes of easy problems to warm up the hands. Never attempt a hard problem on test day.
- After every round: write the debrief into `07_Interviews/06_Post_Interview_Debriefs/` **the same day**, and update that company's `05-debrief.md`.
- Between slots: do not study new material. Reread the relevant cheatsheet and the company folder.

---

## 8. Alternate timelines

**If you only have 8 weeks** (started late): drop Phase A weeks 1 and 3 to half-weeks by studying only P1 rows; drop CN, architecture, TOC, CV and verbal entirely; keep both gates.

**If you have 16 weeks** (starting for Phase 2 / next cycle): expand Phase B to 8 weeks, add all P3 topics, add 6 HLD case studies, and add a second language to working fluency. Add a monthly full-day simulation: OA in the morning, three back-to-back mock interviews in the afternoon.

**If a target company's OA lands mid-plan:** collapse to a 5-day company sprint — day 1–2 their past OA patterns, day 3–4 their most-repeated topics, day 5 a full simulation of their format. Then return to the plan where you left it. Do not restart.

---

## 9. Review cadence

- **Daily (10 min, end of day):** update `Trackers/problem-tracker.md`; log every miss in `10_Mistake_Log_and_Revision/mistake-log.md`.
- **Weekly (Sun, 60 min):** re-solve 3 problems you got wrong; update `Trackers/topic-revision-tracker.md`; write next week's 3 concrete targets at the bottom of this file.
- **Phase gate (end of W4, W8, W10):** run the gate test above. Record pass/fail here. A failed gate costs one week of the next phase — plan for that possibility rather than pretending it away.

---

## 10. Progress log

> Append one line per week. Honest, short, no narrative.

| Week | Planned | Actually done | Gate | Adjustment for next week |
|---|---|---|---|---|
| W0 | Setup + diagnostic | | — | |
| W1 | | | — | |
| W2 | | | — | |
| W3 | | | — | |
| W4 | | | Gate A: | |
| W5 | | | — | |
| W6 | | | — | |
| W7 | | | — | |
| W8 | | | Gate B: | |
| W9 | | | — | |
| W10 | | | Gate C: | |
