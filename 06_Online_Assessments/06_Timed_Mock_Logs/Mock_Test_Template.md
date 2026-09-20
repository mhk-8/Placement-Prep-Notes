
# Mock Test Log — Template

> **How to use this file.** Copy everything between the two `=====` markers into a new file named
> `YYYY-MM-DD_<Company-or-Source>.md` in this folder, one file per mock. Fill it in **within
> thirty minutes of finishing the test**, while you still remember why you got things wrong.
>
> **The rule that makes this work:** the score is the least useful number in this document. The
> **mistake breakdown** in Section 5 is the whole point. A mock you do not analyse is a mock you
> wasted.

---

## Why log mocks at all ⭐⭐⭐

```
Practising without logging  →  you repeat the same three mistakes for six weeks.
Practising with logging     →  you discover that 60% of your lost marks come from ONE cause
                               (usually: time spent on questions you should have skipped),
                               and you can fix that cause directly.
```

Most candidates plateau not because they lack knowledge but because they never find out **which**
of the five error causes is costing them. Section 5 exists to find that out.

---

=====  COPY FROM HERE  =====

# Mock Test — `<Company / Source>` — `<YYYY-MM-DD>`

## 1. Test metadata

| Field | Value |
|---|---|
| Date | |
| Start time / End time | |
| Source | (PrepInsta / company past paper / AMCAT / self-made / placement cell) |
| Target company | |
| Platform simulated | (HackerRank / Codility / iON / Mettl / paper) |
| Total duration | |
| Sectional timing? | Yes / No |
| Negative marking? | Yes / No — if yes, what fraction |
| Calculator allowed? | Yes / No |
| Environment | (quiet room / hostel / noisy — be honest, it affects the result) |
| Interruptions | (number and total minutes lost) |

---

## 2. Section-wise scores

| # | Section | Qs | Attempted | Correct | Wrong | Skipped | Score | Accuracy % | Time used | Time allotted |
|---|---|---|---|---|---|---|---|---|---|---|
| 1 | Quantitative Aptitude | | | | | | | | | |
| 2 | Logical Reasoning | | | | | | | | | |
| 3 | Verbal Ability | | | | | | | | | |
| 4 | Technical MCQ | | | | | | | | | |
| 5 | Coding | | | | | | | | | |
| 6 | Other | | | | | | | | | |
| | **TOTAL** | | | | | | | | | |

```
Accuracy %      = Correct / Attempted × 100
Attempt rate %  = Attempted / Total questions × 100
Net score       = Correct − (negative marking × Wrong)
```

**The two numbers that matter most ⭐⭐:**

| Metric | This mock | Last mock | Target |
|---|---|---|---|
| **Accuracy %** (of what you attempted) | | | **> 85%** |
| **Attempt rate %** | | | **> 75%** |

> **Diagnosis rule:**
> - High attempt rate + low accuracy → you are **guessing**. Slow down and skip more.
> - Low attempt rate + high accuracy → you are **too slow**. Drill speed, not concepts.
> - Both low → the topic knowledge is missing. Go back to the notes.
> - Both high → move to a harder mock.

---

## 3. Time management analysis ⭐⭐⭐

### 3.1 Per-section pacing

| Section | Allotted | Used | Over/Under | Questions left unseen |
|---|---|---|---|---|
| Quant | | | | |
| Reasoning | | | | |
| Verbal | | | | |
| Technical | | | | |
| Coding | | | | |

⚠️ **"Questions left unseen"** is the most damning column. A question you never read is a question
you could not even guess on. If this is above zero in any section, your pacing — not your
knowledge — is the binding constraint.

### 3.2 The time-sink log

List every question where you spent **more than double** the per-question budget:

| Q# | Section | Topic | Time spent | Got it right? | Should I have skipped it? |
|---|---|---|---|---|---|
| | | | | | |
| | | | | | |
| | | | | | |

```
Total time lost to questions I should have skipped:  ______ minutes
Number of easy questions I could have done in that time:  ______
```

⭐ **This single calculation is usually the biggest finding in the whole log.** Most lost marks are
not "hard questions I could not do" — they are "easy questions I never reached because I spent nine
minutes on a hard one".

### 3.3 Pacing checkpoints
Did you check the clock at the planned points?

| Checkpoint | Planned position | Actual position | On track? |
|---|---|---|---|
| 25% time elapsed | | | |
| 50% time elapsed | | | |
| 75% time elapsed | | | |

---

## 4. Coding section detail (if applicable)

| Q# | Problem topic | Approach used | Test cases passed | Optimal? | Time | Notes |
|---|---|---|---|---|---|---|
| 1 | | | / | Yes / No | | |
| 2 | | | / | Yes / No | | |
| 3 | | | / | Yes / No | | |

```
□ Did I submit a brute force BEFORE optimising?              Yes / No
□ Did I test empty / single-element / maximum-size inputs?   Yes / No
□ Did I check for integer overflow?                          Yes / No
□ Did I read the input format exactly (no prompt strings)?   Yes / No
□ Did I remove debug prints before final submission?         Yes / No
□ Complexity I achieved vs intended:  ______ vs ______
```

**Problem I could not solve — what was the key insight I missed?**
```


```

---

## 5. Mistake breakdown ⭐⭐⭐ — the core of this log

For **every single wrong or skipped question**, classify the cause. Be brutally honest; the
category matters more than the question.

### 5.1 The error-cause table

| Q# | Section | Topic | My answer | Correct answer | **Cause code** | Fix (one line) |
|---|---|---|---|---|---|---|
| | | | | | | |
| | | | | | | |
| | | | | | | |
| | | | | | | |
| | | | | | | |
| | | | | | | |
| | | | | | | |
| | | | | | | |

### 5.2 Cause codes ⭐⭐⭐

| Code | Cause | What it actually means | The fix |
|---|---|---|---|
| **K** | **Knowledge gap** | I did not know the concept, formula or rule | Go back to the topic file and re-learn it |
| **A** | **Application error** | I knew the concept but could not apply it here | Do 10 more problems of this exact pattern |
| **C** | **Calculation error** | Right method, wrong arithmetic | Daily mental-arithmetic drill; write intermediate steps |
| **M** | **Misread the question** | I answered a different question | Underline the actual ask before solving |
| **T** | **Time pressure** | I knew it but rushed, or never reached it | Pacing discipline; skip earlier |
| **G** | **Guessed** | No basis at all | Should this have been a skip instead? (negative marking) |
| **S** | **Silly/careless** | Right answer, wrong bubble; sign error; units | Slow down on the final step |

### 5.3 The cause tally — fill this in every time

| Cause | Count this mock | Count last mock | Trend |
|---|---|---|---|
| K — Knowledge gap | | | |
| A — Application | | | |
| C — Calculation | | | |
| M — Misread | | | |
| T — Time | | | |
| G — Guess | | | |
| S — Silly | | | |
| **Total errors** | | | |

> **Read the tally, not the score.** ⭐⭐⭐
> - Mostly **K** → you are under-prepared on content. Study, do not take more mocks yet.
> - Mostly **A** → you know the theory but not the patterns. Do focused problem sets.
> - Mostly **C** or **S** → your arithmetic and care are the bottleneck. This is the *fastest*
>   thing to fix: 20 minutes of daily drill for two weeks.
> - Mostly **M** → build the habit of underlining the question's actual ask.
> - Mostly **T** → your pacing rule is wrong. Revise the skip threshold downward.
> - Mostly **G** → you are attempting things you should skip. With negative marking this is
>   actively costing you marks.

---

## 6. Topic-wise weakness map

Tick the topics where you lost two or more marks. Over several mocks a pattern appears.

**Quantitative**
```
□ Number system / HCF-LCM      □ Percentages              □ Profit & loss
□ Ratio / proportion / mixtures □ Averages & ages          □ Time, speed, distance
□ Time & work / pipes           □ SI & CI                  □ P&C / probability
□ Algebra / progressions        □ Geometry / mensuration   □ Data interpretation
```

**Reasoning**
```
□ Number / letter series        □ Coding-decoding          □ Blood relations
□ Direction sense               □ Seating (linear)         □ Seating (circular)
□ Floor / box puzzles           □ Syllogisms               □ Data sufficiency
□ Statement-conclusion          □ Figure / visual series   □ Puzzles
```

**Verbal**
```
□ Subject-verb agreement        □ Tenses                   □ Prepositions
□ Articles                      □ Pronouns / modifiers     □ Parallelism
□ Para-jumbles                  □ Sentence completion      □ Vocabulary
□ Reading comprehension: main idea / detail / inference / tone / vocab-in-context
```

**Technical**
```
□ DBMS / SQL                    □ Operating systems        □ Computer networks
□ OOP concepts                  □ DSA complexity           □ C/C++ output prediction
□ Pseudocode tracing            □ Coding implementation
```

---

## 7. Three actions before the next mock ⭐⭐

Not ten. **Three.** Specific, small, and completed before the next test.

| # | Action | Source file | Deadline | Done? |
|---|---|---|---|---|
| 1 | | | | ☐ |
| 2 | | | | ☐ |
| 3 | | | | ☐ |

*Good actions look like:* "Redo the 10 alligation problems in `Ratio_Proportion_and_Mixtures.md`
and re-time them" or "Memorise the port-number table and self-test on Friday."

*Bad actions look like:* "Improve at quant", "Practise more", "Be faster."

---

## 8. Reflection (three sentences, no more)

**What went well:**
```

```

**What cost me the most marks, in one sentence:**
```

```

**The one rule I will apply differently next time:**
```

```

=====  COPY TO HERE  =====

---

## Master progress tracker

Keep this table in a **separate file** called `00_Progress_Tracker.md` in this folder, and add one
row after every mock. This is what you review before a real OA.

| # | Date | Source | Total % | Quant % | Reas % | Verb % | Tech % | Code % | Accuracy % | Attempt % | Top error cause |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | | | | | | | | | | | |
| 2 | | | | | | | | | | | |
| 3 | | | | | | | | | | | |
| 4 | | | | | | | | | | | |
| 5 | | | | | | | | | | | |
| 6 | | | | | | | | | | | |
| 7 | | | | | | | | | | | |
| 8 | | | | | | | | | | | |

**What to look for in this table ⭐:**
```
- Is the TOP ERROR CAUSE column changing? If it has read "T" for five mocks, your pacing fix
  is not working and needs to be more aggressive.
- Is accuracy rising while attempt rate holds? That is real improvement.
- Is the total % flat while accuracy rises? You are being too conservative — attempt more.
- Is one section consistently 20 points below the others? That is where the next week goes.
```

---

## How to run a mock properly ⭐⭐⭐

A mock taken badly teaches you nothing. The point is to reproduce test-day conditions closely
enough that the results transfer.

```
BEFORE
  □ Same time of day as the real test, if known
  □ Phone in another room, on silent. Not face-down on the desk — another room.
  □ Single monitor, browser full-screen, everything else closed
  □ Rough sheets and pen ready; no calculator unless the real test allows one
  □ Water and a clear desk; tell people not to disturb you
  □ Set a visible timer for the whole test AND for each section

DURING
  □ No pausing. Ever. If the phone rings, note the interruption and keep going.
  □ No looking anything up. A wrong answer is data; a looked-up answer is noise.
  □ Note the clock position at 25%, 50% and 75%
  □ Mark every question you are unsure about, so you can classify it honestly afterwards

AFTER
  □ Do NOT check answers immediately. First, spend 10 minutes writing down your own
    assessment of what went wrong. Then check. ⭐ This makes the analysis honest.
  □ Fill in this log within 30 minutes
  □ Re-solve every wrong question WITHOUT looking at the solution, on paper
  □ Only then read the solutions
  □ Move genuinely new concepts to /10_Mistake_Log_and_Revision/
```

---

## Mock schedule ⭐

| Phase | Frequency | Type |
|---|---|---|
| **Foundation** (weeks 1-3) | 1 sectional mock / week | Single section, untimed then timed |
| **Build** (weeks 4-6) | 2 full mocks / week | Full-length, timed, section-locked |
| **Peak** (weeks 7-8) | 3 full mocks / week | Company-specific patterns |
| **Taper** (final 3 days) | 0-1 light mock | Revision only; do NOT take a hard mock the day before ⚠️ |

⚠️ **Do not take a difficult mock the day before a real test.** A bad score the night before
damages confidence far more than the practice helps. Spend that evening on your formula sheets and
your own mistake log, and sleep.

---

## Recall questions

1. What are the two metrics that matter more than the raw score, and what are the targets?
2. What does "high attempt rate, low accuracy" tell you to change?
3. List the seven error-cause codes and the fix for each.
4. Why is "questions left unseen" the most damning column in the pacing table?
5. Why should you write your own assessment *before* checking the answer key?
6. Why only three actions before the next mock?
7. What should you not do the day before a real OA?
