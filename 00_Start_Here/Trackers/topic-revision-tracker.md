# Topic Revision Tracker

> Spaced repetition for *concepts* (the problem tracker handles individual problems).
> A topic enters this table the day you first study it, with four scheduled reviews: **D+1, D+3, D+7, D+21**.

## The method — active recall, not re-reading

A review is: **close the notes, write what you remember on paper, then check.** Re-reading a note feels like learning and is not; the effort of retrieval is the mechanism. Budget 5–10 minutes per topic.

Three outcomes:
- **Recalled well (4–5)** → tick and move to the next scheduled date.
- **Partial (3)** → tick, but re-insert at D+3 from today rather than continuing the schedule.
- **Blank (0–2)** → do not tick. Restudy the topic today and restart its schedule from D+1.

Confidence scale is the same as `syllabus-checklist.md`: 0 never seen → 5 can teach it.

## Daily use

Each morning, the **cold recall block** takes everything whose next due date is today or earlier. If the queue exceeds 8 topics, do the 8 oldest and push the rest — do not skip the block.

---

## Active queue

| Topic | Folder | First studied | D+1 | D+3 | D+7 | D+21 | Conf | Next due | Notes |
|---|---|---|---|---|---|---|---|---|---|
| *example:* Page replacement + Belady | `02_Core_CS/01_OS` | 2026-09-22 | ☑ 09-23 | ☑ 09-25 | ☐ 09-29 | ☐ 10-13 | 3 | 2026-09-29 | Kept confusing LRU vs Optimal on the same trace |
| | | | ☐ | ☐ | ☐ | ☐ | | | |
| | | | ☐ | ☐ | ☐ | ☐ | | | |
| | | | ☐ | ☐ | ☐ | ☐ | | | |
| | | | ☐ | ☐ | ☐ | ☐ | | | |
| | | | ☐ | ☐ | ☐ | ☐ | | | |
| | | | ☐ | ☐ | ☐ | ☐ | | | |
| | | | ☐ | ☐ | ☐ | ☐ | | | |
| | | | ☐ | ☐ | ☐ | ☐ | | | |
| | | | ☐ | ☐ | ☐ | ☐ | | | |

---

## Graduated topics

> A topic graduates when all four reviews are ticked **and** confidence is 4+. Graduated topics get one final pass in Week 10 and are otherwise left alone.

| Topic | Folder | Graduated on | Conf | W10 final pass |
|---|---|---|---|---|
| | | | | ☐ |
| | | | | ☐ |
| | | | | ☐ |

---

## Restarted topics

> Anything that went blank at D+7 or D+21. These are your real weak points — far more informative than the graduated list.

| Topic | Times restarted | Why it keeps slipping | Fix applied |
|---|---|---|---|
| | | | |
| | | | |

**If a topic restarts twice**, the problem is the note, not your memory. Rewrite it: shorter, with a worked example and a diagram, and add it to `09_Cheatsheets`.

---

## Coverage by section

> Count of topics graduated, updated at each phase gate.

| Section | Total topics | Graduated W4 | Graduated W8 | Graduated W10 |
|---|---|---|---|---|
| `01_DSA` | 57 | | | |
| `02_Core_CS` | ~45 | | | |
| `03_Languages` | 7 | | | |
| `04_System_Design` | 7 | | | |
| `05_AI_ML` | 29 | | | |
| `06` + `07` readiness | 14 | | | |
