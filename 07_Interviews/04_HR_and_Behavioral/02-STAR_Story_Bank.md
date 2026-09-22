
# STAR Story Bank — Built From Your Actual Experience

> **The problem this file solves.** Behavioural questions are infinite; the stories you have are
> finite. The trick is not to prepare an answer per question — it is to prepare **five or six real
> stories** and learn to re-cut each one to fit whatever is asked.
>
> Every story below is drawn from your actual resume. **You must fill in the specifics** — I have
> the shape from your CV, not the details. Write them in your own words, with real numbers and real
> names.

---

## 1. The STAR structure ⭐⭐⭐

```
SITUATION  : the context, in two sentences. Where, when, who.          ~15 s
TASK       : what YOU specifically had to do. Not the team — you.      ~10 s
ACTION     : what you actually did, step by step. THE LONGEST PART.    ~60 s
RESULT     : the outcome, with a number or a concrete consequence.     ~15 s
             + what you learned, in one sentence.
```

⚠️ **The three failure modes:**
```
1. ALL SITUATION, NO ACTION. Two minutes of context and fifteen seconds of what you did.
   The ACTION is what is being assessed. Cut the setup ruthlessly.
2. "WE" INSTEAD OF "I". ⭐ Interviewers are trying to isolate YOUR contribution. Say "the team
   decided X, and I specifically did Y." Using "we" throughout makes your role invisible.
3. NO RESULT. A story that ends "...and then the semester finished" is not a story.
```

---

## 2. Your six core stories ⭐⭐⭐

These are the raw material. Fill in the details, then learn which questions each one answers.

---

### Story A — **Learning a field from scratch** (the Mechanical → CS switch)
```
SITUATION : Mechanical B.Tech at IIITDM; decided during third year to move into CS.
TASK      : Clear GATE while finishing a mechanical degree, then survive graduate CS courses
            without the undergraduate foundation everyone else had.
ACTION    : [FILL IN — be specific]
            - how you prepared for GATE alongside final-year coursework
            - the specific strategy in semester 1: which fundamentals you back-filled,
              in what order, and how you did it alongside the graduate courses
            - what you did when a course assumed knowledge you did not have
RESULT    : Completed the transition; now TA CS5800 Advanced Data Structures & Algorithms.
            [FILL IN any other concrete marker]
LEARNED   : How to systematically close a knowledge gap under time pressure, and that the
            fastest route is usually depth-first on the fundamentals everything else rests on.
```
**Answers:** learning something quickly · overcoming a challenge · biggest achievement ·
self-motivation · taking a risk · adapting to change.
⭐ **Your single most versatile story.** Practise it first.

---

### Story B — **The hardest technical problem** (points-to analysis on GPU)
```
SITUATION : M.Tech project under Prof. Nandivada — parallelising an Andersen-style points-to
            analysis on NVIDIA GPUs. The workload is irregular, data-dependent, and the graph
            rewrites itself during traversal: close to the worst case for a GPU.
TASK      : Make it actually fast, not just correct.
ACTION    : [FILL IN with the real sequence]
            - profiled first rather than guessing; identified three distinct bottlenecks
              (uncoalesced access, warp divergence from load imbalance, atomic contention)
            - the specific fix for each: CSR + warp-per-node, frontier compaction with
              warp-level primitives, per-block privatisation before global merge
            - how you VALIDATED correctness against Soot on DaCapo, because a soundness-critical
              analysis cannot be "mostly right"
RESULT    : 78% reduction in analysis time over the traditional scheme.
LEARNED   : To profile before optimising — two of the three bottlenecks were not what I
            expected going in. [ADJUST to what was actually true]
```
**Answers:** hardest technical problem · a time you had to figure something out ·
attention to detail · how you approach an unfamiliar problem · persistence.

---

### Story C — **A failure or a mistake** ⭐⭐⭐
```
⚠️ YOU MUST HAVE ONE. "I can't think of a failure" is the worst possible answer — it reads as
   either dishonest or as someone who has never attempted anything hard.

CANDIDATES from your resume — pick the one that actually happened:
  - The 50% localisation accuracy in the multi-task project: you shipped a result you were not
    happy with. What did you try? What would you do now?  ⭐ strong, because it is on your resume
  - A bug in the SSSP project that took a long time to find (the duplicate-enqueue problem,
    the adaptive-Δ boundary splitting equal distances)
  - Something in the Transformer that trained but produced garbage translations — a mask or a
    target-shift bug
  - Optimising before validating, then not knowing which change caused a bug

SHAPE:
SITUATION : [the specific project and what went wrong]
TASK      : [what you were trying to achieve]
ACTION    : what you did when you realised — how you diagnosed it, what you changed
RESULT    : the honest outcome, INCLUDING if it stayed imperfect
LEARNED   : the specific, behavioural change you made afterwards  ⭐ this is the whole point
```
**Answers:** a failure · a mistake · something you would do differently · handling setbacks ·
receiving criticism.

⭐ **The rule:** the story must end with a *changed behaviour*, not a moral. "I learned to test
more" is weak; "I now write the edge-case tests before I optimise, because on that project I
couldn't tell which of two changes broke it" is strong.

---

### Story D — **Explaining something difficult** (TA experience) ⭐⭐
```
SITUATION : TA for CS5800 Advanced Data Structures & Algorithms, and previously CS1111
            Problem Solving using Computers — very different audiences.
TASK      : Get students past a specific conceptual block. [FILL IN the real one]
ACTION    : [FILL IN — a concrete instance]
            - the specific misconception you kept encountering (e.g. students memorising DP
              recurrences instead of defining the state)
            - how you changed your explanation — the analogy, the diagram, the worked example
            - how you checked it had landed
RESULT    : [concrete — students unblocked, a doubt session that worked, feedback]
LEARNED   : That if I cannot explain a design decision simply, I usually do not understand it
            yet — which has changed how I work on my own problems.
```
**Answers:** explaining to a non-technical person · teaching/mentoring · communication ·
patience · going beyond your responsibilities.
⭐ Very few candidates have a genuine teaching story. Use it.

---

### Story E — **Leadership / working with a team** (Aero Design Club)
```
SITUATION : Subsystem Lead, Talpade Aero Design Club, IIITDM (June 2023 – April 2024).
            Also Co-ordinator, Industrial Design Club (2022-23).
TASK      : [FILL IN — what was your subsystem, what was the deadline, how many people]
ACTION    : [FILL IN]
            - how you divided work, and what you did when someone did not deliver
            - a specific disagreement and how it resolved
            - a decision you made under time pressure
RESULT    : [FILL IN — competition result, prototype completed, whatever actually happened]
LEARNED   : [FILL IN]
```
**Answers:** leadership · conflict with a teammate · a tight deadline · motivating others ·
a decision you made · working with people who disagreed with you.

⚠️ **This is your weakest-documented area and your most likely gap.** Most of your recent work is
individual. Spend real time filling this in — behavioural rounds lean heavily on teamwork, and
"I mostly work alone" is not an acceptable answer.

---

### Story F — **Shipping something for a user** (Gesture Volume)
```
SITUATION : Infosys Springboard virtual internship — a gesture-controlled volume system.
TASK      : Make it feel responsive, not just accurate.
ACTION    : [FILL IN]
            - discovered that pixel distance between landmarks changes with camera distance,
              so the same gesture gave different results
            - solved it with 3-D normalisation against a reference hand dimension
            - [any smoothing/latency work you did]
RESULT    : A working, low-latency system with two interaction modes.
LEARNED   : That for anything interactive, perceived latency matters more than model accuracy —
            a detector 2% more accurate but 100 ms slower makes the product worse.
```
**Answers:** building something end-to-end · a time you had to consider the user ·
working independently · a project you are proud of (small-scale version).

---

## 3. The question → story mapping ⭐⭐⭐

| Question | Primary story | Backup |
|---|---|---|
| Tell me about a challenge you overcame | A (switch) | B (GPU) |
| Tell me about a failure | **C** ⚠️ must have | — |
| Tell me about a time you learned something quickly | A | B |
| Describe your hardest technical problem | B | — |
| Tell me about a conflict with a teammate | E | ⚠️ weakest area |
| Tell me about leading a team | E | D (TA) |
| A time you worked under a tight deadline | E | A |
| Explaining something to a non-technical person | **D** ⭐ | F |
| A time you disagreed with a decision | E | B (a design disagreement with your guide?) |
| Going beyond what was asked | D or B | F |
| Handling criticism or feedback | C | D |
| A time you had to make a decision with incomplete information | B | E |
| Your biggest achievement | A | B |
| A time you helped someone | **D** ⭐ | E |
| Adapting to a big change | A | — |
| A time you had to say no / push back | E | ⚠️ may need a new story |

⚠️ **Two gaps visible in this table:** conflict/disagreement and pushing back. Both map to Story E,
which is your least-documented. Make filling in Story E a priority.

---

## 4. Re-cutting one story for different questions ⭐⭐

The same raw material answers different questions by changing **which part you expand**.

**Story B (the GPU project), re-cut three ways:**
```
"Hardest technical problem"      → expand the ACTION: the three bottlenecks and the fixes
"A time you had to learn fast"   → expand the SITUATION: arriving with no compilers background
                                   and having to absorb points-to analysis before you could start
"Attention to detail"            → expand the RESULT: validating exactly against Soot on DaCapo,
                                   because a soundness-critical analysis cannot be approximately
                                   right
```

⭐ **Practise this explicitly.** Take one story and tell it three ways, aloud. It is what turns six
stories into coverage of twenty questions.

---

## 5. The preparation checklist ⭐⭐

```
□ Fill in the [FILL IN] blanks in all six stories with REAL specifics
□ Every story has a NUMBER or a concrete consequence in the RESULT
□ Every story uses "I" for your actions, "we" only for genuine team decisions
□ Story C (failure) ends with a CHANGED BEHAVIOUR, not a platitude
□ Story E is properly filled in — it is your weakest and most-asked area ⚠️
□ Each story said aloud in under 2 minutes, timed
□ Each story re-cut for at least two different questions
□ Recorded once and watched back
```

---

## 6. What interviewers are really listening for ⭐⭐

```
OWNERSHIP     : do you say "I" and take responsibility, including for what went wrong?
SPECIFICITY   : real names, real numbers, real decisions — or vague generalities?
SELF-AWARENESS: can you describe your own mistake without defensiveness?
IMPACT        : did anything actually change as a result of what you did?
RECOVERY      : when it went wrong, what did you do NEXT? (this matters more than the failure)
GENEROSITY    : how do you talk about other people, especially ones who let you down? ⚠️
```

⚠️ **That last one is checked silently in every story.** How you describe a teammate who did not
deliver tells the interviewer how you will describe *them*. Be fair, be brief, and never be bitter.

---

## Recall questions

1. Give the STAR structure with time budgets, and name the longest section.
2. What are the three failure modes, and which is most common?
3. Why must a failure story end with a changed behaviour?
4. Which two question types are your weakest-covered, and which story must you fill in?
5. Demonstrate re-cutting one story for three different questions.
6. What is being assessed silently in every story you tell?
