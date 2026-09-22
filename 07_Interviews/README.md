
# 07 — Interviews

> **This folder is built around your actual resumes** (`SDE Resume.pdf`, `ML Resume.pdf`,
> `OLD Master Resume.pdf`). The project files, the narrative scripts and the difficult-question
> answers are specific to you, not generic. Where a file says `[FILL IN]`, that is a gap only you
> can close — do it before you need it.

---

## 1. Your profile in one page ⭐⭐

```
WHO      M.Tech CSE, IIT Madras (CS25M028), CGPA 7.5, graduating 2027
         B.Tech Mechanical Engineering, IIITDM Kancheepuram, 7.95, 2024

TRACKS   SDE / Systems (GPU, parallel computing, compilers)  ← your strongest positioning
         ML / Deep Learning
         ⭐ ML INFRASTRUCTURE — the intersection, and the least crowded category for you

EVIDENCE M.Tech project : points-to analysis on GPU (Prof. Nandivada) — 78% time reduction
         CUDA           : Δ-stepping SSSP (2M vertices / 300M edges), image preprocessing pipeline
         Deep learning  : Transformer from scratch (34.8 BLEU), multi-task vision (0.91 F1),
                          feedforward net with hand-derived backprop
         NLP / IR       : six retrieval systems benchmarked with Wilcoxon + Cohen's d
         Teaching       : TA for CS5800 Advanced DS&A, and CS1111
         Experience     : Infosys Springboard virtual internship (Gesture Volume)
```

**Your differentiator, stated plainly:** compilers **and** GPU **and** deep learning, with a
flagship project sitting exactly where they meet. Very few campus candidates have all three. Lead
with it.

**Your three narrative risks**, each with a prepared answer in `04_HR_and_Behavioral/`:
```
1. The Mechanical → CS switch         — guaranteed to be asked, every interview
2. M.Tech CGPA 7.5, below B.Tech 7.95 — asked often
3. No industry internship             — sharpest on the SDE resume, which has no
                                        experience section at all
```

---

## 2. What is in this folder

```
07_Interviews/
│
├── README.md                          ← you are here
│
├── 01_Technical_Round_Prep/
│     ├── 00-Interview_Protocol.md          the 6-step protocol, thinking aloud, hints, stuck
│     ├── 01-GPU_and_Parallel_Computing_QA.md   ⭐⭐⭐ your differentiator — mandatory revision
│     ├── 02-DSA_Round_Playbook.md         patterns, templates, YOUR GAPS (DP is the big one)
│     ├── 03-CS_Fundamentals_Rapid_Fire.md  OS/DBMS/CN/C++/OOP + COMPILERS (your resume invites it)
│     ├── 04-ML_and_DL_Round_QA.md         for ML tracks, incl. the "why ML?" calibration answer
│     └── 05-Mock_Interview_Log.md         template + tracker
│
├── 02_Project_Deep_Dives/             ⭐ 40-60% of every interview for an M.Tech candidate
│     ├── 00-How_To_Present_A_Project.md   the 3 lengths, the question ladder, limitations first
│     ├── 01-Points_To_Analysis_on_GPU.md  ⭐⭐⭐ flagship
│     ├── 02-Parallel_SSSP_on_GPU.md
│     ├── 03-Image_Preprocessing_Pipeline_GPU.md
│     ├── 04-Transformer_Machine_Translation.md  ⭐⭐⭐ flagship ML
│     ├── 05-Multi_Task_Visual_Perception.md
│     ├── 06-Information_Retrieval_Engine.md
│     ├── 07-Feedforward_NN_From_Scratch.md
│     └── 08-Gesture_Volume_Infosys_Internship.md
│
├── 03_Resume_and_Portfolio/
│     ├── 00-Resume_Versions_Audit.md      ⭐ honest audit of your three versions + defect fixes
│     ├── 01-Resume_Walkthrough_Script.md  drafted scripts + what every resume line invites
│     ├── 02-Skill_Claims_Defence.md       3 questions per claimed skill; the Java problem
│     └── 03-GitHub_and_Portfolio_Checklist.md
│
├── 04_HR_and_Behavioral/
│     ├── 00-Tell_Me_About_Yourself.md     the core HR questions with drafted answers
│     ├── 01-The_Mechanical_to_CS_Story.md ⭐⭐⭐ the most important file here
│     ├── 02-STAR_Story_Bank.md            six stories built from your resume — has [FILL IN]s
│     └── 03-Difficult_Questions.md        CGPA, no internship, attribution, "why ML?"
│
├── 05_Questions_To_Ask/
│     └── Questions_To_Ask_Them.md         incl. questions only YOU could credibly ask
│
└── 06_Post_Interview_Debriefs/
      ├── Debrief_Template.md              copy per round; write within 2 hours
      └── 00_Interview_Tracker.md          ⭐ "where am I losing?" analysis + question bank
```

**Related folders:**
```
../01_DSA/                    the coding-round content
../02_Core_CS/                the depth behind the rapid-fire round
../04_System_Design/          design rounds
../05_AI_ML/                  ML theory and ML system design
../06_Online_Assessments/     the stage before this one
../10_Mistake_Log_and_Revision/   where everything you get wrong ends up
```

---

## 3. Do these five things first ⭐⭐⭐

Before anything else in this folder:

```
1. FIX THE RESUME DEFECTS.  Two real bugs, about 15 minutes of work:
     - the (cid:123) bullet-glyph encoding, which can break ATS parsing
     - GitHub/LinkedIn as visible URL text, not just icons
   → 03_Resume_and_Portfolio/00-Resume_Versions_Audit.md §3e, §3h

2. WRITE YOUR MECHANICAL → CS ANSWER.  Forty seconds, in your own words, out loud, timed.
   You will use it in every single interview.
   → 04_HR_and_Behavioral/01-The_Mechanical_to_CS_Story.md

3. FILL IN THE [FILL IN] BLANKS in the STAR story bank, especially Story E (leadership /
   teamwork), which is your least-documented and most-asked area.
   → 04_HR_and_Behavioral/02-STAR_Story_Bank.md

4. START DYNAMIC PROGRAMMING.  It is the clearest gap between your resume and a DSA round,
   and it is slightly awkward given you TA the algorithms course.
   → 01_Technical_Round_Prep/02-DSA_Round_Playbook.md §5

5. EXPLAIN THE POINTS-TO PROJECT TO A NON-COMPILER PERSON.  If they cannot follow it,
   your flagship project is a liability rather than an asset.
   → 02_Project_Deep_Dives/01-Points_To_Analysis_on_GPU.md §2
```

---

## 4. The preparation timeline ⭐⭐

| When | Focus |
|---|---|
| **8+ weeks out** | DSA volume, DP gap, CS fundamentals. One peer mock a week |
| **4-8 weeks** | Project files: write your own 60-second versions and rehearse them. Two mocks a week. Resume fixes done |
| **2-4 weeks** | Company-specific: read their OA pattern file, their engineering blog. Three mocks a week, including one project deep-dive mock ⭐ |
| **1 week** | Difficult questions rehearsed. GPU Q&A revised. GitHub tidied. No new material |
| **Day before** | Re-read your project files and your walkthrough script. One light mock at most. Sleep ⚠️ |

---

## 5. The pre-interview routine (60 minutes) ⭐⭐⭐

Run this before every interview.

```
 0-10 min  COMPANY
           Which archetype? Read their file in ../06_Online_Assessments/01_OA_Patterns_by_Company/
           Read one engineering blog post. Pick your two questions to ask.

10-25 min  PROJECTS
           Re-read the deep-dive files for the 2-3 projects relevant to THIS company.
           Say each 60-second version ALOUD.
           Draw one architecture diagram from memory.

25-40 min  TECHNICAL REVISION
           Systems/HPC → 01-GPU_and_Parallel_Computing_QA.md §8 (the 30-second refresh)
           ML          → 04-ML_and_DL_Round_QA.md §8
           Any         → 03-CS_Fundamentals_Rapid_Fire.md §9 (20-min drill)

40-50 min  NARRATIVE
           Your walkthrough script, aloud, timed.
           Your Mechanical→CS answer, aloud.
           Whichever difficult question is most likely for this company.

50-60 min  LOGISTICS AND SETTLE
           Internet, camera, microphone, link tested. Water. Notebook and pen.
           Resume open in a tab — know WHICH VERSION they have. ⭐
           Phone away. Then stop preparing and let your mind settle. ⚠️
```

⚠️ **Do not learn anything new in the last hour.** It will not stick and it displaces the things
that would have.

---

## 6. The five habits that matter most ⭐⭐⭐

```
1. THINK ALOUD. Silent problem-solving reads as being stuck. This is the most common
   technical-round failure and the easiest to fix — by recording yourself once a week.

2. STATE LIMITATIONS BEFORE BEING ASKED. Volunteering a weakness makes every other claim
   you make more credible. It is the single highest-leverage habit in project discussion.

3. ANSWER, THEN STOP. Rambling loses more HR rounds than any wrong answer. The silence
   after a short honest answer feels long and is not.

4. DEBRIEF WITHIN TWO HOURS. Ten debriefs make the eleventh interview substantially easier.
   The part you forget first is the part you got wrong.

5. BE SCRUPULOUS ABOUT ATTRIBUTION. Your flagship project builds on published work.
   Conceding the baseline immediately and precisely is a credibility multiplier —
   and the honest version is more impressive anyway.
```

---

## 7. What is being graded, across all rounds ⭐⭐

```
TECHNICAL ROUND : problem solving · coding · communication · verification
PROJECT ROUND   : depth (why × 3) · judgement (why THIS approach) · honesty (limitations)
                  · your specific contribution
HR ROUND        : motivation · commitment · how you talk about other people
                  ⚠️ rarely wins an offer; frequently loses one
THROUGHOUT      : can they picture working with you for two years?
```

---

## 8. Frequently asked (by you, of this folder)

**"Which file should I read if I have one hour?"**
`04_HR_and_Behavioral/01-The_Mechanical_to_CS_Story.md`, then
`02_Project_Deep_Dives/00-How_To_Present_A_Project.md`. Those two shape more of your outcome than
any technical file.

**"Which companies should I target?"**
Systems and HPC — NVIDIA, AMD, Qualcomm, Samsung R&D, Arcesium, database and kernel teams — plus
ML-infrastructure teams. Your combination is rare there and common nowhere else. See
`01_Technical_Round_Prep/02-DSA_Round_Playbook.md` §1.

**"Should I lead with the GPU work or the ML work?"**
Depends on the company; see `03_Resume_and_Portfolio/01-Resume_Walkthrough_Script.md` §7. For ML
*infrastructure*, lead with both and name the intersection explicitly — that is your best pitch and
your least crowded market.

**"What if I don't know the answer in the interview?"**
Say so, then reason aloud. `"I'm not sure — I know it's related to X, can I reason about it?"` An
honest reasoned attempt scores far above a confident wrong answer, because these rounds are partly
a calibration test.

**"Is the CGPA going to block me?"**
7.5 clears most cut-offs. It will be asked about. Prepare the 30-second answer in
`04_HR_and_Behavioral/03-Difficult_Questions.md` §2 and then stop worrying about it — your projects
are stronger than your transcript, and that is the argument to make.

---

Revision rule: after every session, move anything you got wrong into
`../10_Mistake_Log_and_Revision/`.
