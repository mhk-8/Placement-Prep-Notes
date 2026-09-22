
# How to Present a Project

> **Why this folder matters more for you than for most candidates.** You are an M.Tech candidate
> with no full-time industry internship. That means **projects are your evidence**. Expect them to
> occupy 40-60% of every technical interview, and expect the depth of questioning to go well beyond
> what a B.Tech candidate faces. This is good news: it is the part of the interview where you can
> be the most knowledgeable person in the room.

---

## 1. The three lengths every project needs ⭐⭐⭐

| Length | When it is used | Content |
|---|---|---|
| **20 seconds** | Resume walkthrough — you are listing six projects | One sentence: what it does + the headline number |
| **60 seconds** | "Tell me about this project" | Problem → approach → result → your specific contribution |
| **5 minutes** | "Walk me through it in detail" | The full architecture, the key decision, the hardest bug, the limitation |

⚠️ **The most common failure is giving the 5-minute version when asked the 60-second question.**
Interviewers have a plan for the hour; a candidate who cannot compress is a candidate who will
over-run a design meeting. Give the 60-second version, then **stop and let them choose the
follow-up**. Their follow-up tells you what they actually care about.

---

## 2. The 60-second template ⭐⭐

```
[CONTEXT]   "This was my <course / M.Tech> project under Prof. <name>."         ~5 s
[PROBLEM]   "The problem was <X>, which matters because <Y>."                  ~10 s
[APPROACH]  "I <built / implemented / mapped> <the core technical idea>."      ~20 s
[RESULT]    "It achieved <specific number>, compared to <baseline>."           ~10 s
[HOOK]      "The hardest part was <one interesting thing>."                    ~10 s
            ← then STOP. The hook invites the question you WANT to be asked.  ⭐⭐⭐
```

⭐ **The hook is the most under-used technique in interviewing.** You control which follow-up you
get by choosing what to leave dangling. End on the part of the project you know best.

---

## 3. The question ladder you must survive ⭐⭐⭐

Every technical claim on your resume must survive **"why" asked three times**. Test each project
against this ladder yourself, out loud, before any interview:

```
L1  WHAT did you build?                        → the 60-second version
L2  WHY that approach?                         → what else did you consider, and why reject it?
L3  HOW does the key mechanism actually work?  → explain it at the level of data structures
L4  WHAT was the hardest bug / bottleneck?     → a specific, concrete story with a number
L5  WHAT are its limitations?                  → state them BEFORE being asked  ⭐
L6  HOW would you extend / scale / productionise it?
L7  WHAT would you do differently now?
```

⚠️ **Candidates fail at L2 and L5 far more often than at L3.** Knowing how a thing works is common;
being able to justify *choosing* it over the alternative, and being honest about where it breaks,
is rare and is exactly what senior interviewers screen for.

---

## 4. Stating limitations first — why it wins ⭐⭐

```
WEAK : "It worked well and achieved 34.8 BLEU."
       → the interviewer now hunts for the weakness, and finding it feels like catching you out.

STRONG: "It reached 34.8 BLEU on Multi30k, which is a small, clean, single-domain corpus.
         I'd expect it to degrade substantially on a noisier or more distant language pair,
         and I never tested beam search, so 34.8 is a greedy-decoding number that a beam
         of 4-5 would likely improve by a point or two."
       → the interviewer now trusts every OTHER number you quote.
```

Volunteering the limitation converts a vulnerability into a credibility signal. It costs nothing
and it is the single highest-leverage habit in project discussion.

---

## 5. The whiteboard rule ⭐

For any systems or ML project, **draw it**. A five-box diagram of your pipeline beats three minutes
of talking, and it gives the interviewer something to point at when asking follow-ups — which
keeps the conversation on ground you have prepared.

```
Practise drawing each project's architecture in under 60 seconds, from memory, on paper.
If you cannot, you do not know it well enough to discuss it under pressure.
```

---

## 6. Handling a project you have half-forgotten ⚠️

Some of your projects are from earlier semesters. If a detail is gone:

```
✅ "I don't remember the exact figure — it was around 0.9 macro-F1. What I do remember clearly
    is that the classification head converged much faster than the segmentation head, which is
    why I had to weight the losses."
❌ "I think it was maybe 0.95? Or 0.85, something like that."
```

Admit the gap, then immediately demonstrate you own the *reasoning*. Interviewers forgive a
forgotten number; they do not forgive a fabricated one, and a wrong confident number is the fastest
way to lose the room.

> **Before any interview, re-read the project files in this folder and re-open the repositories.**
> Thirty minutes of refresh prevents the worst ten minutes of the interview.

---

## 7. Your project portfolio at a glance

| Project | Track | Strength as an interview asset | Risk |
|---|---|---|---|
| **Points-to Analysis on GPU** (M.Tech) | SDE / Systems | ⭐⭐⭐ Flagship. Compilers **and** GPU — a rare pairing. Ongoing, so you own it deeply | Deep and niche; you must be able to explain it to a non-compiler person |
| **Parallel SSSP on GPU** | SDE / Systems | ⭐⭐⭐ Classic algorithm, real engineering, concrete scale numbers | Interviewer may know Δ-stepping better than you — know the paper |
| **Image Preprocessing on GPU** | SDE / Systems | ⭐⭐ Clean, explainable, good for CUDA fundamentals questions | Smallest project; do not over-claim |
| **Transformer for MT** | ML | ⭐⭐⭐ From scratch is the differentiator, not the BLEU | "From scratch" invites gradient-level questions — be ready |
| **Multi-Task Visual Perception** | ML | ⭐⭐⭐ Multi-task loss balancing is a genuinely interesting discussion | The 50% localisation accuracy is weak — own it proactively |
| **Information Retrieval Engine** | ML / NLP | ⭐⭐ Six systems benchmarked with **statistical testing** — rare rigour | Classical IR; connect it to RAG to make it current ⭐ |
| **Feedforward NN from Scratch** | ML | ⭐⭐ Proves you can hand-derive backprop | Only on the master resume; smallest ML project |
| **Gesture Volume** (Infosys) | Either | ⭐ Your only "experience" line on the ML resume | Virtual internship; do not oversell it as industry work |

---

## 8. Pre-interview project drill (30 minutes) ⭐⭐

```
□ Re-read the relevant project file in this folder
□ Say the 60-second version ALOUD, timed. Twice.
□ Draw the architecture from memory in under 60 seconds
□ Answer the three "anticipated follow-ups" aloud without reading
□ Re-open the repo; check you can still explain the entry point and the core function
□ Re-read your stated limitation so it comes out fluently, not defensively
```

---

## Recall questions

1. What are the three lengths, and which one do you give unprompted?
2. What is a "hook" and why does it matter?
3. Recite the seven-rung question ladder.
4. Why does volunteering a limitation *increase* credibility?
5. What do you say when you have forgotten a number?
