
# Infosys, Wipro, Accenture, Cognizant — Mass-Recruiter Online Assessments

> **Accuracy note.** OA formats change every recruiting season, and companies run different
> papers for different campuses, roles and dates. Treat everything below as the *shape* of the
> test — what it measures and how to prepare — and verify the exact duration, section count and
> cutoff against this year's campus notice and last year's seniors before you sit the test.


These four run high-volume campus drives with broadly similar tests: **aptitude-heavy, coding-light,
strictly timed, sectionally locked**. Preparing for one prepares you for all four; this file gives
the shared strategy and then the per-company differences.

---

## 1. The shared shape

```
Aptitude (quant + logical)  →  Verbal / English  →  Technical MCQ  →  Coding (1-3 easy problems)
                                                         │
                                                         ▼
                                         (some) Essay / communication assessment
```

**What they are actually filtering for:** speed, accuracy under time pressure, basic English, and
proof that you can write a working program. They are *not* filtering for algorithmic depth.

**The universal mechanics to internalise:**
1. **Sectional locking** — you cannot return to a previous section. ⚠️
2. **Per-question timers** in some tests (Infosys historically) — you cannot even return to the
   previous *question*.
3. **Negative marking** in some sittings — read the instruction page.
4. **No calculator** — mental arithmetic is scored in effect.

---

## 2. Infosys

| Item | Typical |
|---|---|
| Platform | HackerRank / Infosys in-house (SHL for some roles) |
| Structure | Reasoning Ability, Mathematical Ability, Verbal Ability, **Pseudocode**, **Puzzle Solving**, Coding |
| Duration | ~3 hours total across sections |
| Distinctive | ⭐ **Pseudocode section** and ⭐ **Puzzle section** are Infosys signatures |

### Pseudocode section ⭐
10-15 questions. You are given a language-agnostic pseudocode block and must predict the output or
identify what it computes.

What to drill:
- Loop tracing with a hand-drawn variable table (write `i`, `j`, and each variable in columns)
- Recursion tracing — draw the call stack, do not try to hold it in your head
- Array index manipulation and swapping
- Nested-loop counting (how many times does the inner statement execute?)
- Integer vs float division
- Pass-by-value vs pass-by-reference notation

> **Method: always make a trace table.** Six rows of a table beats thirty seconds of staring.
> ```
> step | i | j | arr        | output
>   1  | 0 | 2 | [3,1,2]    |
>   2  | 1 | 1 | [1,3,2]    |
> ```

### Puzzle section ⭐
Classic logic puzzles under time: weighing, crossing the river, age/relationship, lateral thinking.
See `03_Logical_Reasoning_and_Puzzles/Common_Interview_Puzzles.md` — Infosys draws from exactly
that canon.

### Coding
1-3 problems, easy to easy-medium. Arrays, strings, basic maths, occasionally a simple greedy or
hash map. Higher packages (Power Programmer / Specialist Programmer) get genuinely harder problems
— roughly LeetCode Medium with DSA depth (trees, graphs, DP).

---

## 3. Wipro

| Item | Typical |
|---|---|
| Platform | Wipro's own / AMCAT-style |
| Structure | Aptitude (quant, logical, verbal), **Written Communication Test (essay)** ⭐, Coding |
| Duration | ~2.5-3 hours |
| Distinctive | ⭐ The **essay** is scored by an automated engine |

### The Written Communication Test ⭐⭐
You are given a topic and ~20 minutes to write ~200-400 words. It is **machine-scored** on grammar,
vocabulary range, sentence structure, coherence and length.

How to score well:
```
1. LENGTH MATTERS. Hit the word count. A short essay scores badly regardless of quality.
2. STRUCTURE EXPLICITLY:
     Para 1  Introduction: restate the topic, state your position   (3-4 sentences)
     Para 2  Argument 1 with an example                             (4-5 sentences)
     Para 3  Argument 2 with an example                             (4-5 sentences)
     Para 4  Counter-point acknowledged, then rebutted              (3-4 sentences)
     Para 5  Conclusion: restate and close                          (3 sentences)
3. USE LINKING WORDS: however, moreover, consequently, in contrast, for instance,
   therefore, nevertheless. The engine rewards discourse markers. ⭐
4. VARY SENTENCE LENGTH. Mix short declaratives with longer complex sentences.
5. DO NOT repeat the same word — use synonyms.
6. LEAVE 2 MINUTES to proofread for spelling, articles and subject-verb agreement.
```

Common topics: technology's impact on society, work-from-home, social media, AI and jobs,
education reform, environment, urbanisation, the role of youth.

> Write and time three practice essays before the test. That alone puts you above most candidates.

### Coding
2-3 problems; same easy band as the others. Some sittings restrict languages — check whether Python
is allowed before relying on it.

---

## 4. Accenture

| Item | Typical |
|---|---|
| Platform | Accenture's assessment portal |
| Structure | Cognitive (verbal, analytical, quant), Technical MCQ, **Coding (2 problems)**, **Communication Assessment**, sometimes a Game-based/behavioural module |
| Duration | ~2-2.5 hours total |
| Distinctive | ⭐ **Communication Assessment** (spoken/listening, headset-based) for some tracks |

Notes:
- The **cognitive** section is tightly timed and section-locked; pace is everything.
- The **technical MCQ** covers networking basics, OS, DBMS/SQL, cloud/agile terminology, pseudocode,
  and MS Office/IT fundamentals — broader and shallower than a product company's.
- **Coding**: 2 problems, easy; language choice usually includes C, C++, Java, Python.
- The **communication assessment** may require a headset: repeat sentences, listen and answer,
  read aloud, and speak on a topic. Test your microphone beforehand. ⚠️

---

## 5. Cognizant (CTS)

| Item | Typical |
|---|---|
| Platform | Cognizant's portal / HackerRank |
| Structure | Aptitude + Logical + Verbal, **Automata Fix (debugging)** ⭐, Coding, sometimes an essay |
| Distinctive | ⭐ **Automata Fix**: given broken code, make it compile and pass tests |
| Tracks | GenC (base), GenC Next / GenC Pro / Elevate (higher bands, harder coding) |

### Automata Fix ⭐
Short programs with compile errors or logical errors. You fix them in place. The bug families are
the same as Amazon's debugging section (see `Amazon.md` §4): off-by-one, wrong operator, wrong
variable, missing return, bad initialisation, missing semicolon or type mismatch.

> **Do not rewrite the program.** You are scored on making the given code work with minimal
> changes, and the clock does not allow a rewrite.

Higher bands (GenC Pro / Elevate) add real DSA problems and a deeper technical interview.

---

## 6. What to prepare — the shared 80%

Ranked by return on time for all four companies:

| Priority | Area | Detail |
|---|---|---|
| **P1** | Arithmetic speed | Percentages, profit & loss, ratio, TSD, time-work, averages, SI/CI. **Mental calculation drills daily** |
| **P1** | Logical reasoning | Series, coding-decoding, blood relations, directions, seating, syllogism, data sufficiency |
| **P1** | Basic coding fluency | Arrays, strings, loops, recursion, patterns — able to write and debug in 10 minutes |
| **P2** | Pseudocode tracing | Trace tables; loops; recursion |
| **P2** | Verbal | Grammar rules, RC skimming, para-jumbles, vocabulary |
| **P2** | Technical MCQ | DBMS/SQL, OS, networks, OOP basics — `05_MCQ_Core_CS_Banks/` |
| **P3** | Essay writing | Three timed practice essays (Wipro, some Cognizant) |
| **P3** | Puzzles | The standard canon (Infosys) |

---

## 7. The mass-recruiter mindset ⭐⭐⭐

These tests are won by **pace and composure**, not by cleverness.

```
□ Do the easy questions in every section FIRST — within the section's own timer.
□ A question you cannot classify in 20 seconds is a question you skip.
□ Never let one hard question eat three easy ones. That is the only real failure mode.
□ Keep a rough sheet organised: one question per block, so you can re-check quickly.
□ In coding: read input exactly as specified, print exactly what is asked, no prompt text.
□ Attempt the higher-band sections if offered; they decide your package, not just your selection.
```

---

## 8. Six-week plan

| Week | Focus |
|---|---|
| 1-2 | Quant fundamentals + daily 20-minute mental-arithmetic drill |
| 3 | Logical reasoning + pseudocode tracing |
| 4 | Verbal + technical MCQ + essay practice (3 timed essays) |
| 5 | Coding fluency: 40 easy programs, each written in under 12 minutes |
| 6 | **Four full timed mocks**, logged in `06_Timed_Mock_Logs/`, with mistake analysis after each |
