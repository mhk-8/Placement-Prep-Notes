
# The Difficult Questions — Your Specific Ones

> Every candidate has three or four questions they hope will not come up. Yours are identifiable
> from your resume, which means they are identifiable to the interviewer too. **Prepare them
> properly and they stop being difficult.**
>
> The universal rule ⭐⭐⭐: **answer briefly, factually, without defensiveness, and then stop.**
> Length signals that a topic is unresolved for you. A calm 30-second answer closes it; a
> two-minute justification keeps it open.

---

## 1. "Why did you switch from Mechanical to CS?" ⭐⭐⭐

Full treatment in `01-The_Mechanical_to_CS_Story.md` — the most important file in this folder.
This question is **guaranteed**; prepare it first.

---

## 2. "Your M.Tech CGPA is 7.5, lower than your B.Tech 7.95. Why?" ⚠️⭐⭐⭐

**What they are checking:** whether there is a problem you are hiding, and whether you are
defensive about it.

**The answer (30 seconds):**
> "Two reasons, honestly. The first two semesters I was taking graduate CS courses while
> back-filling the undergraduate fundamentals underneath them — data structures, operating systems,
> theory — because I'd come from mechanical engineering and didn't have them. That cost me marks
> on the courses I was taking at the same time.
>
> The second is that I made a deliberate choice to put time into the M.Tech project and the TA work
> rather than into optimising grades. I'd make the same trade again — the fundamentals and the
> project depth have been worth more than the decimal would have been."

**Why this works:**
```
✅ Gives a real, verifiable reason (the field switch) without using it as an excuse
✅ Owns the second half as a CHOICE rather than a failure  ⭐
✅ Points at evidence that the choice paid off (the project, the TA role)
✅ Ends decisively. No trailing apology.
```

⚠️ **Do not:**
```
✗ Blame professors, grading, or "the IIT Madras curriculum is harder"
✗ Say "CGPA doesn't reflect ability" — true or not, it sounds like a complaint
✗ Over-explain. Two reasons, then stop.
✗ Bring it up unprompted in an interview where it was not asked  ⭐ (the exception is inside
  the Mechanical→CS answer, where pre-empting it works well)
```

**If they push: "But 7.5 is below our usual bar."**
> "I understand. I'd point at the project and the TA role as the evidence for what I can actually
> do, and I'm happy to go as deep as you like on either."

Calm, no argument, redirect to evidence.

---

## 3. "You have no industry internship. Why?" ⚠️⭐⭐⭐

**This is your sharpest gap**, particularly on the SDE resume, which has no experience section at
all. Prepare it carefully.

**The answer:**
> "That's fair — my only formal experience is the Infosys Springboard virtual internship, which was
> a self-directed project programme rather than a placement on a team.
>
> The reason is that my M.Tech intake was in 2025 and the summer between the two years went into
> the research project with Prof. Nandivada, which was the choice I made deliberately — a year on
> one hard systems problem, with a guide, rather than a shorter industry project. I think it's
> given me more depth than an internship would have, though I'd acknowledge it hasn't given me the
> experience of working in a team codebase.
>
> The closest thing I have to that is the TA work, where I'm responsible for other people's
> learning outcomes on a schedule, not just my own."

**The structure ⭐:**
```
1. CONCEDE the point honestly and immediately — do not fight it
2. Explain the ACTUAL REASON (a deliberate choice, not a failure to get one)
3. State what you got INSTEAD, with evidence
4. ACKNOWLEDGE the genuine gap — "working in a team codebase" — without pretending it isn't one
5. Offer the nearest equivalent you do have
```

⚠️ **Step 4 is the one candidates skip and the one that matters most.** Acknowledging the real gap
is what makes steps 2 and 3 believable. Pretending your project is equivalent to industry
experience is transparent and costs you more than the gap itself.

**What to add if you have done anything else:**
```
Open-source contributions, freelance work, a hackathon, a competition, a research paper,
a significant self-directed project — anything that involved a deadline or another
stakeholder. Add it if it is real. Do not invent it.
```

---

## 4. "Your M.Tech project is a compiler on GPUs. Why are you applying for an ML role?" ⭐⭐

Full answer in `../01_Technical_Round_Prep/04-ML_and_DL_Round_QA.md` §7. The core:

> "My coursework and most of my projects are ML. The M.Tech project is systems, and I chose it
> deliberately, because ML is increasingly a systems problem — training is bounded by memory
> bandwidth and communication, inference by the KV cache. Having written and profiled CUDA kernels
> means I understand *why* things like FlashAttention or quantisation work, not just that they do.
> For an applied or infrastructure role I think that's an advantage rather than a detour."

⭐ **Turn it into a specialisation**, not an apology. ML infrastructure is under-served and this
combination is exactly what it wants.

---

## 5. "Your M.Tech project builds on someone else's published work. What is actually yours?" ⚠️⭐⭐⭐

**A senior interviewer will ask this.** Answering it badly — by claiming the whole result — is the
fastest way to lose trust in an otherwise strong interview.

> "PInter is Prof. Nandivada's group's work — the parameterised constraint formulation and the
> interleaved generation-and-solving, which is what produces the 96% constraint reduction. That is
> the baseline I build on, not my contribution.
>
> What's mine is the GPU parallelisation: the data-structure mapping — points-to sets as
> device-resident bit-vectors, the dependence graph in CSR, one warp per node — the kernel design
> for the four constraint types, and the bottleneck work on coalescing, warp divergence and atomic
> contention. And the validation against Soot on DaCapo."

⭐ **Scrupulous attribution is a credibility multiplier.** The honest version is also more
impressive, because the GPU mapping is substantial work on its own and it now sounds precise rather
than inflated.

⚠️ Note that your resume bullet ("cutting constraint count by 96% and analysis time by 78%") is
ambiguous about attribution. Be ready to disambiguate it immediately and voluntarily.

---

## 6. "You graduate in 2027. Why are you interviewing now?" ⭐

Straightforward, but have the answer ready:
> "This is the standard placement season for my batch — final-year M.Tech students interview in
> the autumn and join after graduating in 2027."

Know your expected joining date precisely and state it without hedging.

---

## 7. "What if we offer you a role that isn't in your area of interest?" ⭐

They are testing flexibility and how attached you are to a specific title.

> "I'd want to understand what the role actually involves, because titles cover a lot of different
> work. If it's adjacent — anything where the problems are systems-shaped — I'd be genuinely
> interested. If it's a long way from anything I've built toward, I'd rather say so than take it
> and be a poor fit for you as well as for me."

⚠️ Do **not** say "I'll do anything." It reads as desperation and it invites a bad placement.
Do **not** be rigid either. The honest middle is the right answer.

---

## 8. "You seem to have done a lot of course projects. Have you built anything real?" ⚠️

A fair challenge, and a common one for M.Tech candidates.

> "Most of them are course projects, yes. What I'd say distinguishes them is the depth — the
> Transformer is built from scratch rather than assembled from `nn.Transformer`, the IR project
> benchmarks six systems with significance testing rather than reporting one number, and the GPU
> projects are profiled and validated against reference implementations. The M.Tech project isn't a
> course project — it's a year of research work that's ongoing.
>
> The honest gap is that none of them have users, and I'd count that as the main thing an industry
> role would give me."

⭐ Again: concede the real part, redirect to the evidence, name the genuine gap.

---

## 9. "Are you applying anywhere else?" / "Would you accept if we offered?" ⭐

```
"Are you applying elsewhere?"
   "Yes — it's the placement season, so I'm in a few processes. This one is one I'd be
    particularly glad to move forward with, because <specific reason>."
   ⚠️ Honest, brief, and redirects to interest in THEM.

"Would you accept if we made an offer?"
   Do not over-promise if you are not sure. But do not be lukewarm either:
   "I'd take it seriously — based on what I've heard today, this is the kind of work I want
    to be doing. I'd want to see the full offer, but yes, I'm genuinely interested."
```

---

## 10. Questions you should refuse to answer ⚠️

Some questions are inappropriate. You are allowed to decline, politely.

```
- Caste, religion, marital status, family income, political views
- "Are you planning to get married / have children?"
- Anything about a medical condition or disability not relevant to the role

RESPONSE:  "I'd rather keep the conversation to the role, if that's alright."
           Said calmly, then move on. You do not need to justify the boundary.
```
⚠️ If a company's process is consistently inappropriate, tell your placement cell. That is what it
is for, and it protects the next candidate.

---

## 11. The universal technique ⭐⭐⭐

For **any** difficult question:

```
1. ACKNOWLEDGE the point honestly. Do not fight a fair observation.
        "That's fair."  /  "Yes, that's true."
2. EXPLAIN the real reason, briefly, without excuses.
3. REDIRECT to evidence of what you did instead, or what you have done since.
4. NAME the genuine residual gap, if there is one.       ⭐ the step people skip
5. STOP.
```

Step 4 is counter-intuitive and it is what makes the whole answer credible. An interviewer who
hears you name your own weakness accurately will believe your account of your strengths.

---

## 12. Rehearsal ⭐⭐

```
□ Write your own version of §2 (CGPA) and §3 (no internship). These two are near-certain.
□ Say each aloud, timed. Target 30-40 seconds. Most run to 90 on the first attempt.
□ Record §3 and watch it back for defensiveness — tone matters more than words here
□ Have someone ask you §5 (attribution) cold, and check you concede the baseline work
  immediately rather than after a pause
□ Practise STOPPING. The silence after a short honest answer feels long and is not.
```

---

## Recall questions

1. What is the universal five-step technique, and which step do people skip?
2. Give the two reasons in your CGPA answer, and what makes the second one strong.
3. What is the structure of the no-internship answer, and what is the genuine gap you name?
4. What is yours versus PInter's in the M.Tech project?
5. How do you decline an inappropriate question?
6. Why does naming your own weakness make your strengths more credible?
