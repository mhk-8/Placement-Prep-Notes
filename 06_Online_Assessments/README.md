
# 06 — Online Assessments

> **The complete guide to using this folder.**
> Folder: `06_Online_Assessments`

---

## 1. Why this folder exists

The online assessment is a **filter, not a test of depth**. It rewards pattern recognition, speed,
time allocation and composure. Most candidates who fail to convert a placement season fail *here* —
not in the interview — and they fail for reasons that have nothing to do with how much they know.

The three ways people lose an OA, in order of frequency:

```
1. TIME. They spend nine minutes on one hard question and never reach six easy ones.
2. FORMAT SURPRISE. They did not know the sections lock, or that prompts must not be printed,
   or that the Advanced section decides the salary band.
3. ARITHMETIC. They know the method but cannot execute it in 45 seconds without a calculator.
```

Every file in this folder attacks one of those three.

---

## 2. What is in here

```
06_Online_Assessments/
│
├── README.md                          ← you are here
│
├── 01_OA_Patterns_by_Company/         What each company's test actually looks like
│     ├── Amazon.md                        debugging + 2 DSA + work style + work simulation
│     ├── Microsoft.md                     Codility, correctness + performance scoring, Group Fly
│     ├── Google.md                        2 hard problems, constraints-first method
│     ├── TCS_NQT.md                       iON platform, bands, sectional locking
│     ├── Infosys_Wipro_Accenture_Cognizant.md   the mass-recruiter playbook
│     ├── Adobe_Oracle_SAP_Salesforce.md   DSA + heavy CS fundamentals
│     ├── Goldman_Sachs_and_Quant_Firms.md mental maths, probability, market-making
│     └── Startups_Unicorns_and_Others.md  ⭐ HOW TO RESEARCH ANY COMPANY + 5 archetypes
│
├── 02_Aptitude_and_Quant/             12 files: theory + formulas + shortcuts + solved examples
│     ├── 00-Core_Arithmetic_Toolkit.md    ⭐ READ THIS FIRST — tables, fractions, shortcuts
│     ├── Number_System.md
│     ├── Percentages.md
│     ├── Profit_and_Loss.md
│     ├── Ratio_Proportion_and_Mixtures.md
│     ├── Averages_and_Ages.md
│     ├── Time_Speed_Distance.md
│     ├── Time_and_Work.md
│     ├── Simple_and_Compound_Interest.md
│     ├── Probability_and_Combinatorics.md
│     ├── Algebra_and_Progressions.md
│     ├── Geometry_and_Mensuration.md
│     └── Data_Interpretation.md
│
├── 03_Logical_Reasoning_and_Puzzles/
│     ├── Common_Interview_Puzzles.md      ⭐ 19 canonical puzzles + a PATTERN INDEX
│     └── Logical_Reasoning.md             seating, blood relations, syllogisms, series, DS
│
├── 04_Verbal_Ability/
│     ├── Grammar_and_Error_Spotting.md    12 rule sets + worked error-spotting
│     └── Reading_Comprehension.md         skim-and-map, elimination heuristics, worked passage
│
├── 05_MCQ_Core_CS_Banks/                  theory + 20 explained MCQs each
│     ├── DBMS.md
│     ├── Operating_Systems.md
│     ├── Computer_Networks.md
│     ├── OOPs.md
│     └── Data_Structures_and_Algorithms.md
│
└── 06_Timed_Mock_Logs/
      ├── Mock_Test_Template.md            ⭐ copy per mock; the MISTAKE BREAKDOWN is the point
      └── 00_Progress_Tracker.md           master table + error-cause trend
```

**Related folders in this vault:**
```
../01_DSA/                    the coding-round preparation this folder assumes
../02_Core_CS/                the depth behind 05_MCQ_Core_CS_Banks
../03_Languages/              language syntax, gotchas, SQL
../04_System_Design/          for the interview rounds after the OA
../05_AI_ML/                  for AI/ML and data-science roles
../00_Start_Here/             the overall study plan and trackers
../10_Mistake_Log_and_Revision/  where everything you get wrong ends up
```

---

## 3. The two-page strategy ⭐⭐⭐

### 3.1 Before any test — research the format (30 minutes, highest return in the season)

Open `01_OA_Patterns_by_Company/Startups_Unicorns_and_Others.md` **Part 1** and run the five-step
research protocol: placement cell → seniors → public archives → the platform's demo test → write it
down. Knowing that a section locks, or that the compiler is C++14, or that prompts must not be
printed, is worth more marks than an extra week of practice.

### 3.2 During the test — the universal rules

```
□ READ THE INSTRUCTION PAGE. Negative marking? Sectional locking? Can you revisit?
□ SCAN THE WHOLE SECTION FIRST (30 seconds). Rank questions by confidence.
□ DO THE EASY 60% OF EVERY SECTION FIRST, within that section's own timer.
□ THE 20-SECOND RULE ⭐: if you cannot classify a question in 20 seconds
  ("this is an alligation problem", "this is a sliding window"), mark it and move on.
□ NEVER let one hard question eat three easy ones. This is the ONLY real failure mode.
□ IN CODING: brute force → SUBMIT → optimise → submit. Partial credit is real.
□ Read input exactly as specified; print exactly what is asked; no prompt strings.
□ Attempt the Advanced/higher-band sections if offered — they decide your package.
□ Reserve the last 5 minutes to revisit flagged questions and re-submit your best code.
```

### 3.3 After the test — capture it within the hour

Write down the problems, your approach and what you missed, into
`01_OA_Patterns_by_Company/<Company>.md`. Problem families recur across seasons, and interviewers
sometimes revisit your OA solution.

---

## 4. The daily study routine ⭐⭐⭐

Three versions. Pick the one that matches the time you actually have, not the one you wish you had.

### 4.1 The full day (8 hours) — for the pre-placement months

```
 07:30 - 08:00   MENTAL ARITHMETIC DRILL (30 min)          ⭐ non-negotiable
                 Tables 12-25, squares to 40, the fraction-percentage table.
                 Then 40 timed two-digit multiplications and divisions.
                 Log your score. This is the single highest-leverage habit in the folder.

 08:00 - 10:00   DSA CODING (2 h)
                 3-4 problems from ../01_DSA/, one pattern family per day.
                 Timed. Write the brute force first, then optimise.

 10:00 - 10:15   Break — away from the screen

 10:15 - 11:45   APTITUDE (1 h 30)
                 One topic file from 02_Aptitude_and_Quant/:
                   30 min  read the theory and formula table
                   45 min  work through the solved examples ON PAPER, then the practice set
                   15 min  re-derive the shortcuts from memory

 11:45 - 12:30   REASONING (45 min)
                 One type from 03_Logical_Reasoning_and_Puzzles/Logical_Reasoning.md,
                 20 questions, timed at 60 seconds each.

 12:30 - 14:00   Lunch and rest

 14:00 - 15:30   CORE CS (1 h 30)
                 One file from 05_MCQ_Core_CS_Banks/ or ../02_Core_CS/.
                 Read the theory, then do the 20-question bank WITHOUT looking at answers.

 15:30 - 16:15   VERBAL (45 min)
                 Mon/Wed/Fri: grammar rules from Grammar_and_Error_Spotting.md + 20 questions
                 Tue/Thu:     two timed RC passages from Reading_Comprehension.md

 16:15 - 16:30   Break

 16:30 - 18:00   PROJECT / RESUME / SYSTEM DESIGN (1 h 30)
                 The OA is not the only round. Keep this slot.

 18:00 - 19:00   Exercise, meals, life. Protect this slot. ⚠️

 19:00 - 20:30   MOCK or WEAK-AREA WORK (1 h 30)
                 Tue & Fri: a full timed mock, logged in 06_Timed_Mock_Logs/
                 Other days: redo yesterday's wrong questions WITHOUT the solution

 20:30 - 21:00   REVISION (30 min)  ⭐ the slot people skip and should not
                 Flip through ../10_Mistake_Log_and_Revision/ and today's formula tables.
                 Spaced repetition is what makes yesterday's work stick.
```

### 4.2 The four-hour day — alongside coursework or an internship

```
 Morning   30 min  Mental arithmetic drill                      ⭐ never skip
 Slot 1    90 min  DSA coding (2-3 problems, timed)
 Slot 2    60 min  ONE of: aptitude topic / reasoning set / core CS bank  (rotate daily)
 Slot 3    45 min  Verbal (alternate grammar and RC)
 Evening   15 min  Revision of the day's formulas + the mistake log
```
**Weekly:** one full timed mock on Saturday, analysed on Sunday.

### 4.3 The 90-minute day — exam weeks, or when everything is on fire

```
 20 min  Mental arithmetic + formula-table recitation
 40 min  ONE DSA problem, timed, written properly
 20 min  One aptitude topic's solved examples OR one MCQ bank section
 10 min  Mistake log review
```
⭐ **Consistency beats intensity.** Ninety minutes every day for eight weeks beats eight hours
twice a week, because the arithmetic and pattern-recognition skills are built by repetition, not by
duration.

---

## 5. The eight-week plan ⭐⭐

Assumes you are starting from scratch with a placement season ahead.

| Week | Aptitude | Reasoning | Verbal | Technical | Coding | Mocks |
|---|---|---|---|---|---|---|
| **1** | Core toolkit, number system, percentages | Series, coding-decoding | Grammar rules 1-5 | DSA complexity tables | Arrays, strings | — |
| **2** | Profit-loss, ratio, averages | Blood relations, directions | Grammar rules 6-12 | DBMS | Hash maps, two pointers | 1 sectional |
| **3** | TSD, time-work | Seating arrangements | RC technique | Operating systems | Sorting, binary search | 1 sectional |
| **4** | SI/CI, P&C, probability | Syllogisms, data sufficiency | Para-jumbles, vocabulary | Computer networks | Trees, recursion | 1 full |
| **5** | Algebra, geometry, DI | Puzzle canon | Timed RC sets | OOP + output prediction | Graphs, DP basics | 2 full |
| **6** | **Revision + speed drills** | **Mixed timed sets** | **Full verbal sections** | **All banks re-tested** | **Timed 2-problem sets** | 2 full |
| **7** | Company-specific patterns from `01_OA_Patterns_by_Company/` | | | | | 3 full |
| **8** | **Taper.** Formula sheets, mistake log, one light mock. Sleep. ⚠️ | | | | | 0-1 |

⚠️ **Week 8 is a taper, not a sprint.** Do not take a hard mock the day before a real OA; a bad
score damages confidence more than the practice helps.

---

## 6. How to use each folder well

### `01_OA_Patterns_by_Company/`
Read the file for your target company **twice**: once when the company is announced, and once the
night before. Add your own notes after every attempt. Use the template in
`Startups_Unicorns_and_Others.md` Part 3 for companies without a dedicated file.

### `02_Aptitude_and_Quant/`
```
1. Read 00-Core_Arithmetic_Toolkit.md FIRST and memorise the fraction-percentage table. ⭐
2. For each topic file: read the concept once, then LIVE in the formula table and
   the solved examples.
3. Work every solved example ON PAPER before reading the solution.
4. After a week, cover the formula table and rewrite it from memory.
```

### `03_Logical_Reasoning_and_Puzzles/`
```
For Logical_Reasoning.md, the content is the NOTATION and the METHOD — seating frames,
blood-relation symbols, Venn diagrams, trace tables. Practise drawing them, not reading them.

For Common_Interview_Puzzles.md, read the PATTERN note at the end of each puzzle. The
patterns (information bounds, backward induction, invariants, parity) transfer to puzzles
you have never seen; the puzzles themselves do not. Use the pattern index at the end. ⭐
```

### `04_Verbal_Ability/`
```
Grammar is pure recall: run the 15-minute revision drill at the end of the grammar file
before every verbal section.
RC is pure method: do two timed passages a week and analyse the CAUSE of every wrong answer
(misread / inference too far / outside knowledge / ran out of time).
```

### `05_MCQ_Core_CS_Banks/`
```
1. Read the theory section.
2. Attempt all 20 questions WITHOUT looking at the answers.
3. Read every explanation, including for the questions you got right — the
   "why the other options are wrong" parts are where most of the learning is. ⭐
4. Two weeks later, redo the bank cold. Anything you get wrong twice goes into
   ../10_Mistake_Log_and_Revision/.
```

### `06_Timed_Mock_Logs/`
```
Copy Mock_Test_Template.md for every mock. Fill Section 5 (the mistake breakdown)
within 30 minutes of finishing. Add a row to 00_Progress_Tracker.md.

⭐ The score is the least useful number in the log. The ERROR-CAUSE TALLY is the point:
   if "T — time pressure" has been your top cause for five mocks, more studying will not
   help and a stricter skip rule will.
```

---

## 7. The five habits that matter most ⭐⭐⭐

```
1. DAILY MENTAL ARITHMETIC (20-30 min). Improves fast for ~3 weeks, then plateaus.
   Start in week one, not the week before the test.

2. ALWAYS PRACTISE TIMED. Untimed practice builds false confidence. An untimed correct
   answer is not evidence that you can produce it in 45 seconds.

3. LOG THE CAUSE OF EVERY ERROR, not just the error. Most people discover that a single
   cause accounts for well over half their lost marks.

4. LEARN TO ABANDON QUESTIONS. This is a skill and it must be practised deliberately.
   Attempting 18 with 17 correct beats attempting 25 with 15 correct.

5. RESEARCH THE FORMAT BEFORE EVERY TEST. Thirty minutes, and it regularly changes the
   outcome more than a week of practice would.
```

---

## 8. Test-day checklist ⭐⭐

**The night before**
```
□ Confirm the test link, login, time and duration
□ Test your internet, webcam and microphone
□ Charge everything; know where the power socket is
□ Re-read your target company's file in 01_OA_Patterns_by_Company/
□ Skim your own formula tables and mistake log. Learn NOTHING new. ⚠️
□ Sleep. Eight hours beats two more hours of revision, every time.
```

**One hour before**
```
□ Eat something. Hunger costs more marks than one more formula gains.
□ Quiet room, door closed, phone in another room, desk clear
□ Rough sheets and two pens ready
□ Close every application and browser tab (tab-switching is logged) ⚠️
□ Keep your ID ready for proctored verification
□ Do five minutes of easy arithmetic to warm up — do NOT attempt anything hard
```

**During**
```
□ Read the instruction page properly
□ Scan the section before starting
□ Apply the 20-second classification rule
□ Watch the clock at 25%, 50%, 75%
□ Submit something for every coding problem
□ Do not leave non-coding sections blank (Work Style, essays — they are scored)
```

**After**
```
□ Within one hour: write down the problems, your approach, and what you missed
□ File it in 01_OA_Patterns_by_Company/<Company>.md
□ Move new concepts into ../10_Mistake_Log_and_Revision/
□ Then stop thinking about it. The next test is what you can still influence.
```

---

## 9. Frequently asked questions

**"How many hours do I need?"**
Roughly 150-200 focused hours for someone starting from scratch, spread over 8-10 weeks. Spread is
more important than total: 2 hours daily beats 14 hours on Sunday.

**"Should I use a calculator while practising?"**
No — unless the real test allows one. Practising with a calculator builds a skill you cannot use.

**"How do I get faster at arithmetic?"**
Section 4's morning drill. There is no other route. It is a drill skill and it responds to drilling.

**"I know the concepts but run out of time. What do I do?"**
That is a **pacing** problem, not a knowledge problem. Two fixes, in order: (i) practise the
20-second classification rule until abandoning a question feels normal, and (ii) drill arithmetic
so the questions you *do* attempt take less time. More studying will not help.

**"Is it worth attempting the Advanced sections if I am unsure?"**
Almost always yes — they typically determine your salary band, not just selection. Check for
negative marking first.

**"Which single file should I read if I have one hour?"**
`02_Aptitude_and_Quant/00-Core_Arithmetic_Toolkit.md`, then Section 3 of this README.

---

Revision rule: after every session, move anything you got wrong into
`../10_Mistake_Log_and_Revision/`.
