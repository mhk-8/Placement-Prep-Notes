# Daily Routine

> The plan tells you *what* to study. This file tells you *how a day runs*, so the decision is already made when you sit down. Decision fatigue is the quiet killer of a 10-week plan.

---

## 1. The principle

Three kinds of work, every single day, in this order:

1. **Revision first** (cold recall of old material) — because it is the part you will skip if you leave it for the evening, and it is the part that actually compounds.
2. **New learning** (one concept, properly) — while your attention is at its best.
3. **Practice under time** (problems, cold) — the only activity that transfers to the OA.

If a day collapses to 90 minutes, you do 30 minutes of each. Never zero out revision.

---

## 2. Standard weekday — 8 hours

| Time | Block | What exactly |
|---|---|---|
| 07:00–07:30 | **Cold recall** | Open `Trackers/topic-revision-tracker.md`, take what is due today. Close the notes, write the answers from memory on paper, then check. No re-reading first. |
| 07:30–09:30 | **New DSA pattern** | Read the pattern, write the template from scratch, solve 2 problems with the notes open. Goal: understanding, not speed. |
| 09:30–10:00 | Break — move, eat, no screen | |
| 10:00–12:00 | **Timed problem block** | 3 problems, notes closed, timer on. 25 min easy / 35 min medium hard cap. When the timer ends, stop and read the editorial. |
| 12:00–13:30 | Lunch + genuine break | |
| 13:30–15:30 | **Core CS or ML depth** | One topic to interview depth. Write the note in `02_Core_CS/…` or `05_AI_ML/…` in your own words *as you learn* — not afterwards. |
| 15:30–16:00 | Break | |
| 16:00–17:00 | **Thesis / coursework buffer** | Non-negotiable if you have deadlines. Protect it, or it eats the evening. |
| 17:00–18:00 | **Aptitude or SQL or MCQ drill** | 30 questions, timed, negative marking on. Rotate: Mon/Thu aptitude, Tue/Fri SQL, Wed core-CS MCQ. |
| 18:00–19:30 | Dinner, exercise, disconnect | |
| 19:30–20:45 | **Second problem block** | 2 problems from *revision* topics, not today's new one. This is where interleaving happens. |
| 20:45–21:00 | **Daily close-out** (below) | |
| after 21:00 | No technical study | |

**Deep-work blocks are phone-in-another-room blocks.** Two hours of genuinely undistracted work beats five hours of interrupted work, and you already know this.

---

## 3. Compressed weekday — 4 hours (heavy thesis / coursework day)

| Time | Block |
|---|---|
| 30 min | Cold recall (due items only) |
| 90 min | One new concept + template + 1 problem |
| 90 min | Timed problem block — 2 problems |
| 20 min | MCQ or aptitude drill |
| 10 min | Daily close-out |

Cut the second problem block and the depth block. Never cut recall or close-out.

---

## 4. Survival day — 90 minutes (exam, illness, travel)

- 20 min cold recall of what is due
- 45 min: one timed medium problem + read its editorial properly
- 15 min: 15 MCQs
- 10 min: close-out

A 90-minute day logged honestly keeps the streak and the tracker alive. A skipped day breaks the spaced-repetition schedule for everything downstream.

---

## 5. Saturday — simulation day

| Time | Block |
|---|---|
| 09:00–10:30 | **Full timed mock OA** in real conditions: no notes, no IDE autocomplete if the platform lacks it, phone off, one sitting |
| 10:30–11:30 | Break — do not review immediately, you need the distance |
| 11:30–13:00 | **Mock review** — the most valuable 90 minutes of the week. For each problem: what was the intended pattern, what did I miss, was it concept / bug / time / misread. Every miss into `10_Mistake_Log_and_Revision/mistake-log.md` |
| 14:30–16:00 | **Mock interview** with a peer (from W7 onward), or a recorded solo solve if no peer is available |
| 16:00–17:00 | Project deep-dive writing, or LLD problem under time |
| Evening | Off. Genuinely off. |

---

## 6. Sunday — consolidation day

| Time | Block |
|---|---|
| Morning (2 h) | **Weekly revision ritual** — see `10_Mistake_Log_and_Revision/README.md`: reread the week's mistakes, re-solve 3 of them cold, promote anything appearing 3+ times |
| Midday (1 h) | Update all four trackers; re-plan next week; write next week's 3 targets into `study-plan.md` §10 |
| Afternoon (1.5 h) | One system-design or ML-system-design case, written up properly |
| Rest of day | **Off.** Not "light study". Off. |

The Sunday afternoon off is not a reward, it is what makes weeks 7 through 10 possible. People who study 70 days straight are noticeably worse in the last fortnight, which is exactly when it counts.

---

## 7. The daily close-out — 15 minutes, never skipped

This is the highest-leverage quarter-hour of the day.

1. **Log problems** — every problem attempted goes into `Trackers/problem-tracker.md` with time taken and whether it was unaided.
2. **Log misses** — every wrong answer, failed problem and half-remembered concept into `10_Mistake_Log_and_Revision/mistake-log.md`, *with a root-cause tag*: concept gap / careless bug / time pressure / misread.
3. **Schedule revision** — anything learned today gets D+1, D+3, D+7, D+21 entries in `Trackers/topic-revision-tracker.md`.
4. **Write tomorrow's first block** on a sticky note. Tomorrow morning you execute, you do not decide.

If the day was bad, log it as a bad day and move on. The log is a diagnostic instrument, not a report card — the moment you start curating it to look good, it stops being useful.

---

## 8. Rules of engagement while studying

- **Timer on, always.** An untimed problem teaches you the algorithm but not the skill being tested.
- **25 min easy / 35 min medium / 45 min hard**, then stop and read the editorial. Grinding for 90 minutes on one problem feels virtuous and teaches almost nothing.
- **Notes written during learning, not after.** Post-hoc note-writing is transcription; live note-writing is processing.
- **Never read a solution without first writing down your best attempt.** The gap between them is the lesson.
- **Interleave.** Yesterday's topic in today's second block. Blocked practice (20 DP problems in a row) produces confidence that evaporates under a mixed OA.
- **Say the approach aloud** for one problem a day. Interviews are spoken, so practise the spoken form.

---

## 9. Energy management

- Hardest cognitive work in your first 3 waking hours. Protect that window for new DP or graph material, not for email or aptitude drills.
- **Sleep 7–8 h.** Below 6 h your problem-solving degrades measurably; you will feel productive and perform worse. This is a real cost, not a moral point.
- Exercise 30 min daily, even a walk. It is the cheapest intervention for the sustained attention this plan demands.
- Take one full evening off per week, and keep it even in November. Burnout in week 9 costs more than any topic you would have covered.
- Eat before the deep block, not during it.

---

## 10. Test-day routine (OA or interview)

- **Night before:** `09_Cheatsheets/night-before.md` only. Nothing new. Laptop, charger, ID, internet backup checked. Asleep by 23:00.
- **Morning:** normal breakfast. 20 minutes of *easy* problems to warm the hands — never a hard problem, a failure that morning costs you confidence you need.
- **15 min before:** close every tab, phone away, water on the desk, bathroom done. Reread your time-allocation rule from `06_Online_Assessments/README.md`.
- **First 5 min of the OA:** read *all* problems before writing any code. Rank them. Solve in your chosen order, not the given order.
- **After:** debrief within 2 hours, into `07_Interviews/06_Post_Interview_Debriefs/` or the company's `05-debrief.md`. Then stop thinking about it.
