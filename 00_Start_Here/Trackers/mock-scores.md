# Mock Scores

> Every timed simulation, logged. The score matters far less than the **failure mode**, which is the only thing you can act on.
> Cadence: 1/week from W2, 2/week from W7, daily in W10.

## What counts as a mock

Real conditions or it does not go in this file: full duration in one sitting, no notes, no phone, no pausing the timer, no looking anything up. A "mock" with three interruptions measures nothing.

---

## Log

| # | Date | Source / company format | Duration | Sections | Score | Est. cutoff | Cleared? | Time left | Primary failure mode |
|---|---|---|---|---|---|---|---|---|---|
| 1 | 2026-09-19 | *example:* self-set diagnostic | 90 min | 3 DSA + 20 MCQ | 1/3 + 13/20 | — | — | 0 min | Spent 45 min on Q2 before reading Q3, which was easier |
| 2 | | | | | | | | | |
| 3 | | | | | | | | | |
| 4 | | | | | | | | | |
| 5 | | | | | | | | | |
| 6 | | | | | | | | | |
| 7 | | | | | | | | | |
| 8 | | | | | | | | | |

---

## Per-mock review sheet

> Copy this block for every mock. Fill it during the Saturday review slot, **after** a break — not immediately, you need the distance.

```
### Mock #__ — <date> — <format>

Score: __/__   Time used: __/__ min   Cleared estimated cutoff? Y/N

Per question:
| Q | Topic/pattern | Attempted? | Result | Time | What went wrong |
|---|---|---|---|---|---|
| 1 |  |  |  |  |  |
| 2 |  |  |  |  |  |
| 3 |  |  |  |  |  |

Time allocation: planned ___ / ___ / ___   actual ___ / ___ / ___
Did I read all questions before starting?      Y / N
Did I submit a brute force before optimising?  Y / N
Did I leave a solvable question untouched?     Y / N — which: ___

Root cause of each miss (tag one):
- Q_: concept gap / careless bug / time mismanagement / misread the question / panic

The single biggest lever for next time (one sentence):
→ 

Entries created in 10_Mistake_Log_and_Revision: ___
```

---

## Failure-mode tally

> The point of this file. Tally after each mock; act on whatever leads.

| Failure mode | W1–4 | W5–8 | W9–10 | What it means | The fix |
|---|---|---|---|---|---|
| **Concept gap** — didn't know the pattern | | | | Coverage problem | More study, targeted at that pattern |
| **Careless bug** — right idea, wrong code | | | | Discipline problem | Pre-submit checklist; dry-run on an example every time |
| **Time mismanagement** — ran out | | | | Strategy problem | Enforce caps; read all questions first; rank before coding |
| **Misread the question** | | | | Attention problem | Restate the problem in your own words before coding |
| **Panic / froze** | | | | Exposure problem | More mocks, not more topics |
| **Environment** — IDE, setup, submission | | | | Preparation problem | Fix the setup once, permanently |

These need **different fixes**, and treating all of them as "study more" is the most common way a prep plan stalls. A candidate whose misses are 80% careless bugs does not need another month of DP.

---

## Trend

| Week | Mocks done | Avg score % | Avg time used % | Dominant failure mode | Cleared cutoff? |
|---|---|---|---|---|---|
| W1 | | | | | |
| W2 | | | | | |
| W3 | | | | | |
| W4 | | | | | |
| W5 | | | | | |
| W6 | | | | | |
| W7 | | | | | |
| W8 | | | | | |
| W9 | | | | | |
| W10 | | | | | |

**Read the trend, not the last score.** One bad mock is noise. Three mocks with the same dominant failure mode is a signal, and it tells you exactly what next week's plan should be.

---

## Time-allocation rule

> Written once, rehearsed in every mock, executed on test day without thinking.

**For a 90-minute, 3-problem OA:**
1. **0–5 min:** read *all* three problems. Rank by (my confidence × marks) ÷ estimated effort.
2. **5–30 min:** easiest first. Submit a brute force the moment it passes samples, then optimise.
3. **30–60 min:** second problem. Same rule.
4. **60–80 min:** hardest. Even a partial/brute-force submission scores on most platforms.
5. **80–90 min:** return to anything with failing edge cases. Never start something new.

**Hard rule:** if a problem has consumed 30% of total time with no working submission, leave it and come back only if time remains. The most common way strong candidates fail an OA is sinking 50 minutes into one problem and never reading the third.

Adapt the numbers to each company's format in `08_Company_Wise/<Company>/01-oa-pattern.md`.
