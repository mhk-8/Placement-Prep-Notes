
# Tell Me About Yourself — and the Core HR Questions

> The opener, the closer, and the six questions in between that every HR round contains. Scripts
> are drafted from your actual resume; rewrite them in your own words before using them.

---

## 1. "Tell me about yourself" ⭐⭐⭐

Full treatment, including the SDE and ML variants, is in
`../03_Resume_and_Portfolio/01-Resume_Walkthrough_Script.md`. The structure:

```
[WHO]  where you are now                              ~10 s
[PATH] how you got here, incl. the Mechanical pivot   ~20 s
[WHAT] two or three things you work on, with a number ~40 s
[WHY]  why this role                                  ~20 s   ← then STOP
```

⚠️ **The HR version differs from the technical version** in one respect: HR cares more about
motivation and trajectory, less about the technical content. Trim the [WHAT] section and expand the
[WHY].

---

## 2. "Why this company?" ⭐⭐⭐

**The most commonly failed HR question**, because most answers are interchangeable.

```
❌ "It's a great company with a good culture and I'll learn a lot."
   → true of every company; tells them nothing; signals you did no preparation
```

**The formula that works:**
```
[SPECIFIC THING ABOUT THEM]  +  [SPECIFIC THING ABOUT YOU]  +  [THE CONNECTION]
```

**Worked examples for your profile:**

> **NVIDIA / AMD / an HPC team:**
> "The work I've spent the last year on is GPU parallelisation of an irregular workload — a
> compiler analysis, which is about the worst-case fit for a GPU. Everything I learned doing it
> came from your architecture documentation and Nsight. Working on the stack itself, rather than on
> top of it, is the obvious next step for me."

> **A database or systems team (Oracle, a storage company):**
> "My M.Tech project is a static analysis problem, and the thing I find most interesting about it
> is the same thing that makes query optimisation interesting — you're reasoning about a program's
> behaviour without running it, and the cost model decides everything. That's the kind of problem
> I want to keep working on."

> **An ML infrastructure team:**
> "I've done both halves of this: from-scratch deep learning — the Transformer, a network with
> hand-derived backprop — and CUDA kernel work with profiling. The place those two meet is exactly
> where I want to work, and this team is one of the few doing that rather than one or the other."

> **A product company (Amazon, Microsoft, Flipkart):**
> "Two things. The scale — the problems only exist at your volume, and the constraint-driven
> engineering that follows from that is what I enjoy. And the ownership model: I've spent a year on
> one deep problem with a guide rather than a manager, so working with a lot of autonomy is what
> I'm used to."

⭐ **Research before every interview:** read their engineering blog, find one recent thing, and name
it. Fifteen minutes of preparation makes this answer specific, and specific is the entire point.

---

## 3. "Why should we hire you?" / "What makes you different?" ⭐⭐

Answer with your actual differentiator, not with adjectives.

> "Two things, I think. The first is the combination — I've done compilers and GPU programming and
> deep learning, and my M.Tech project sits exactly where those meet. That intersection is not
> common, and it's deliberate rather than accidental.
>
> The second is less about skills. I came into CS from mechanical engineering two years ago and
> got to a point where I teach the advanced algorithms course here. Whatever this role needs that
> I don't currently know, I've already demonstrated I can pick up a field from nothing."

⚠️ Do not say "I'm a fast learner, hard worker and team player." Those are claims without evidence,
and everyone makes them.

---

## 4. "What are your strengths?" ⭐

Pick **two**, and attach a specific example to each. Claims without evidence are worthless.

> "Two I'd name. First, going deep rather than broad — I've spent a year on one problem and I know
> it at the level of memory-access patterns and profiler counters, not at the level of a summary.
> That's how I prefer to work.
>
> Second, explaining technical things. I TA the advanced algorithms course, so I do it weekly, and
> it has changed how I work on my own problems — if I can't explain the design decision simply, I
> usually don't understand it yet."

---

## 5. "What is your weakness?" ⭐⭐⭐

**The rules:**
```
✅ A REAL weakness, that a colleague would recognise
✅ With a CONCRETE remediation you are actually doing
✅ That is not fatal to the role you are applying for
❌ A disguised strength ("I'm a perfectionist", "I work too hard") — transparent and irritating
❌ Something disqualifying ("I struggle to meet deadlines", "I don't like working in teams")
```

**Options that are genuinely true for your profile:**

> **(a) Depth over breadth**
> "I tend to go very deep on one thing at the expense of breadth. I spent a lot of the last year on
> the GPU project and, looking at my own resume, there are standard areas I'm thinner on than I
> should be — dynamic programming is the obvious one, which is a bit embarrassing given I TA the
> algorithms course. I've been deliberately working through it over the last few weeks rather than
> adding more depth where I'm already comfortable."

⭐ This is the best option: it is true, it is verifiable from your resume, it shows self-awareness,
and the remediation is specific and current.

> **(b) Over-engineering**
> "I have a tendency to optimise before I've established the thing works. On the SSSP project I
> spent time on the adaptive Δ heuristic before I'd properly validated the base implementation, and
> then had to untangle which of the two was causing a bug. I've become much more disciplined about
> correct-first-then-optimise since."

> **(c) Asking for help late**
> "I tend to sit with a problem longer than I should before asking. It comes from wanting to have
> thought about it properly first, but in practice it costs time. I've started giving myself an
> explicit limit — if I haven't made progress in a set time, I write down where I'm stuck and go
> and ask."

⚠️ Pick **one** and say it once. Do not offer three.

---

## 6. "Where do you see yourself in five years?" ⭐

They are checking for commitment and a plausible trajectory — not a precise plan.

> "Technically deep rather than managerial, at least for the first several years. I'd want to be
> someone the team goes to for a specific area — right now that would be the systems-performance
> side. Beyond that, I'd rather be good at choosing which problems matter than have a title in
> mind."

⚠️ **Do not say:** "doing an MBA", "starting my own company", "doing a PhD" — unless it is true, in
which case be honest and accept the consequence. Do not say "in your position" either; it is a
cliché and it lands badly.

---

## 7. "Do you have any plans for higher studies?" ⚠️⭐⭐

**This question is really "are you a flight risk?"** — and it is asked of M.Tech candidates far more
often than of B.Tech ones, because you have already chosen more education once.

```
IF YOU DO NOT PLAN A PhD (the straightforward case):
  "No. I considered a PhD — my M.Tech project is research-adjacent, so it was a real question —
   but I'd rather work on problems that ship. I want to see the systems I build being used."

IF YOU MIGHT, LATER:
  "Not in the near term. If I went back it would be much later and it would be because a
   specific problem demanded it, not as a default next step. Right now I want to build things."
```

⚠️ **Do not lie about this.** If you are actively applying to PhD programmes, saying "no" and then
leaving in eight months damages you, your placement cell and the next candidate from your
institute. If it is genuinely uncertain, the second script is honest and acceptable.

---

## 8. "Are you comfortable relocating?" / bond / notice ⭐

```
RELOCATION : Give a clear answer. Hedging reads as a problem. If you have a genuine constraint,
             say it plainly and early rather than at the offer stage.
             "Yes, I'm open to relocating."

SERVICE BOND / AGREEMENT : Ask about the specifics factually — duration, amount, what triggers
             it — without sounding like you are planning to break it.
             "Could you tell me about the service agreement terms?" is a reasonable question
             at the OFFER stage, not in round one.

NOTICE / JOINING DATE : You graduate in 2027. Know your expected availability date and say it
             without ambiguity.
```

---

## 9. "What are your salary expectations?" ⚠️

In campus placement this is usually pre-determined by the package tier, so the question often does
not arise. If it does:

> "For a campus role I'd expect to be in line with the standard package for the position. I'm more
> focused on the work and the team at this stage."

⚠️ Never name a number first in a campus context. Never raise compensation in round one.

---

## 10. "Do you have any offers?" ⭐

```
IF YES : Be honest but not boastful, and do not name a company unless asked.
         "I have one other process at a similar stage."
         Do NOT use it as leverage in a campus context — it reads badly and placement cells
         have rules about it.

IF NO  : Simply, without apology. "Not yet — this is early in my season."
         ⚠️ Do not over-explain or sound anxious. It is September of your placement year.
```

---

## 11. "Is there anything else you'd like us to know?" ⭐⭐

The closing question. Most candidates say "no". That is a wasted opportunity.

> "One thing — I noticed the JD mentions <X>, and I didn't get a chance to mention that my
> <project/experience> is directly relevant to that because <one sentence>. I wanted to flag it in
> case it's useful."

Or, if everything relevant was covered:

> "Only that this is the kind of work I've been deliberately pointing myself at, so I'd be glad to
> go further in the process. Thank you for the time."

⭐ Have **one** thing prepared — usually the strongest item that the interview did not cover.

---

## 12. The universal rules for HR rounds ⚠️⭐⭐⭐

```
1. SPECIFIC BEATS IMPRESSIVE. A small, real, concrete story outperforms a grand vague one,
   every time.
2. NEVER CRITICISE anyone — a professor, a teammate, a previous institution, a company.
   It tells them how you will talk about THEM.
3. THIS ROUND RARELY WINS AN OFFER AND FREQUENTLY LOSES ONE. Your goal is to be a person
   they would happily sit next to, not to be dazzling.
4. ANSWER, THEN STOP. Rambling is the most common failure in HR rounds.
5. HONESTY IS THE STRATEGY, not just the ethic. Calibrated honesty about a weakness makes
   your strengths credible.
6. HAVE QUESTIONS READY. See ../05_Questions_To_Ask/.
```

---

## Recall questions

1. Give the formula for "why this company" and produce one for an HPC company.
2. What makes a weakness answer work, and what are the two failure modes?
3. What is the higher-studies question actually asking?
4. What do you say if you have no other offers?
5. What is the one rule that governs how you talk about a past professor or teammate?
6. What should you have prepared for "anything else we should know?"
