
# 08 — Company-Wise

> One folder per target company, created the day you decide to apply and updated the day after
> every round. This folder is the one that compounds: by December it should contain information
> no public resource has, because it comes from your own attempts and your own seniors.

---

## 1. Why this folder beats generic preparation ⭐⭐⭐

```
GENERIC PREP : "I'll get good at DSA and then interview well."
               → true, slow, and ignores that every company tests a different thing.

COMPANY PREP : "Samsung gives one hard problem in 3 hours with C/C++ and no STL.
                Oracle weights SQL and DBMS as heavily as coding.
                Amazon's Work Style section is scored and I nearly left it blank."
               → each of those facts is worth more than a week of general practice.
```

**The asymmetry:** thirty minutes of research into a company's format regularly changes the outcome
more than another week of problems would. Run it every single time.

---

## 2. What is in here

```
08_Company_Wise/
│
├── README.md                          ← you are here
├── 00-Target_List_and_Fit.md          ⭐⭐⭐ START HERE — your shortlist, tiered against
│                                          your actual profile, with a fit rating each
├── 00-Master_Tracker.md               cross-company status, deadlines, question bank
├── 00-Research_Protocol.md            how to build a folder for a company not listed here
│
├── _TEMPLATE/                         copy this folder for any new company
│     ├── 00-process-and-oa.md
│     └── 01-prep-and-questions.md
│
│   ── TIER A — best profile fit (systems, GPU, compilers) ──
├── NVIDIA/
├── Qualcomm/
├── Samsung_RnD/
├── Arcesium/
│
│   ── TIER B — major product recruiters ──
├── Amazon/
├── Microsoft/
├── Google/
├── Adobe/
├── Oracle/
├── Flipkart_Walmart/
│
│   ── TIER C — quant and finance ──
├── Goldman_Sachs/
├── Quant_Trading_Firms/               DE Shaw, Tower, Graviton, Quadeye, Optiver, WorldQuant
│
│   ── TIER D — hard algorithmic ──
└── Sprinklr_MediaNet_Codenation/
```

Each company folder has **two files**:
```
00-process-and-oa.md   the process, the OA pattern, what they optimise for, your fit,
                       their tech stack, and WHICH OF YOUR PROJECTS TO LEAD WITH  ⭐
01-prep-and-questions.md  a dated two-week plan, topic priorities, a seeded question bank,
                       a shell for questions YOU collect, and per-round debrief slots
```

> **Why two files and not the six the old template suggested.** Six skeleton files per company is
> friction, and friction means they stay empty. Two files that you will actually fill beat six that
> you will not. If a company turns out to warrant more, split it then.

---

## 3. Accuracy caveat ⚠️⭐⭐⭐

**Everything in these files about process, duration and section counts is the *shape* of the test,
not a guarantee.** Company formats change every recruiting season, differ by campus, differ by role
and differ between the on-campus and off-campus funnel.

```
TREAT AS DURABLE   : what the company optimises for, the topic emphasis, the preparation strategy,
                     which of your projects to lead with
TREAT AS VOLATILE  : exact duration, number of sections, number of rounds, negative marking,
                     the platform, CTC, cut-offs
VERIFY BEFORE EVERY TEST : the campus notice + two seniors. Then correct the file.  ⭐
```

The files are written so the volatile parts are in clearly marked tables you can overwrite in
thirty seconds.

---

## 4. The four-step workflow ⭐⭐

```
STEP 1 — THE DAY THE COMPANY IS ANNOUNCED  (30 minutes)
  □ Read this folder's file for that company (or copy _TEMPLATE/ if it doesn't exist)
  □ Run the research protocol in 00-Research_Protocol.md — placement cell, two seniors,
    public archives, the platform's demo test
  □ CORRECT the volatile table in 00-process-and-oa.md with this year's facts
  □ Add the dates to 00-Master_Tracker.md

STEP 2 — THE TWO WEEKS BEFORE  (follow the dated plan)
  □ Work 01-prep-and-questions.md §2 as a checklist, not as intentions
  □ Read their engineering blog; find one thing to reference
  □ Decide which 2-3 projects you will lead with, and rehearse those 60-second versions

STEP 3 — THE DAY BEFORE  (60 minutes)
  □ The pre-interview routine in ../07_Interviews/README.md §5
  □ Re-read the "what they optimise for" and "your fit" sections
  □ Nothing new ⚠️

STEP 4 — WITHIN TWO HOURS AFTER  (the step that compounds)
  □ Fill the debrief slot in 01-prep-and-questions.md §5 — questions verbatim
  □ Correct anything in 00-process-and-oa.md that was wrong
  □ Copy coding problems into ../10_Mistake_Log_and_Revision/
  □ Add project questions to ../07_Interviews/02_Project_Deep_Dives/<project>.md
```

⭐ **Step 4 is the whole point of the folder.** A company you have attempted once and debriefed is a
company you are substantially better prepared for next time — and the notes help your juniors,
which is how the archive you are benefiting from got built.

---

## 5. What makes a good company folder ⭐⭐

| Signal of a good folder | Signal of a dead folder |
|---|---|
| Questions from **your own seniors**, with names and dates | Only generic internet content |
| A corrected volatile table with "verified 2026-10-xx" | Last year's numbers, unmarked |
| A dated prep checklist with items actually ticked | A list of intentions |
| A note on which of **your** projects to lead with | A generic "revise DSA" line |
| A debrief written the same day | An empty debrief section |
| A record of what made you *less* interested ⭐ | Only positives |

⚠️ That last one matters if you end up choosing between offers. Record the reservations while they
are fresh; you will not remember them in December.

---

## 6. How these files relate to the rest of the vault

```
../06_Online_Assessments/01_OA_Patterns_by_Company/   the OA FORMAT in depth
         ↕  (this folder cross-references rather than duplicating)
08_Company_Wise/<Company>/                            the COMPANY — process, fit, prep, debrief
         ↓
../07_Interviews/                                     HOW to behave in the round
../01_DSA/ ../02_Core_CS/ ../04_System_Design/ ../05_AI_ML/    the CONTENT
../10_Mistake_Log_and_Revision/                       what you got wrong
```

⚠️ **Deliberate non-duplication:** OA mechanics (platform, scoring model, section timing) live in
`06_Online_Assessments/01_OA_Patterns_by_Company/`. This folder links to them and adds what that
folder cannot: the process beyond the OA, your personal fit, and your own accumulated record.

---

## 7. Companies not covered here

Thirteen folders exist because those are the ones where your profile has a real edge or where the
company is a near-certain campus visitor. Everything else: copy `_TEMPLATE/` and run
`00-Research_Protocol.md`. The protocol takes thirty minutes and produces a better file than any
pre-written one, because it uses this year's information.

---

Revision rule: after every session, move anything you got wrong into
`../10_Mistake_Log_and_Revision/`.
