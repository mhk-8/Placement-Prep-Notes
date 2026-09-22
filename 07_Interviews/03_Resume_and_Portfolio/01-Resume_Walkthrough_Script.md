
# The Resume Walkthrough — Your Script

> **"Walk me through your resume"** or **"tell me about yourself"** opens nearly every interview.
> It is the one question you know is coming, which makes it the one there is no excuse for
> fumbling. It also sets the agenda: whatever you emphasise is what they will ask about next.
>
> ⭐ **Use that.** A good walkthrough steers the interview onto ground you have prepared.

---

## 1. The structure ⭐⭐⭐

```
90 seconds. Four parts. Rehearsed, not memorised.

[WHO]      Where you are now, in one line                            ~10 s
[PATH]     How you got here — including the Mechanical → CS pivot,
           stated confidently and briefly                             ~20 s
[WHAT]     The two or three things you actually work on, with a number ~40 s
[WHY]      Why this role / this company, in one line                  ~20 s
           ← then STOP
```

⚠️ **Do not narrate the resume top to bottom.** "I did my tenth in Visakhapatnam, then my
intermediate..." is the most common opening and it wastes the most valuable minute of the
interview. Start from where you are now and go backwards only as far as is useful.

---

## 2. The SDE / Systems version ⭐⭐⭐

> "I'm a second-year M.Tech CSE student at IIT Madras. My background is a little unusual — my
> B.Tech was in Mechanical Engineering at IIITDM Kancheepuram, and I moved into CS because the
> problems I kept being drawn to were computational ones. I sat the GATE, got into IIT Madras, and
> the switch has worked out well: I now TA the Advanced Data Structures and Algorithms course here.
>
> What I actually work on is GPU and parallel systems. My M.Tech project under Prof. Nandivada
> parallelises a points-to analysis — a static analysis compilers use to figure out what each
> pointer can refer to — on NVIDIA GPUs. It's a hard workload for a GPU because the traversal is
> irregular and the graph rewrites itself while you traverse it, so most of the work has been
> restructuring the data: points-to sets as bit-vectors, the graph in CSR, one warp per node. We're
> at 78% lower analysis time over the traditional scheme.
>
> Alongside that I've done two other CUDA projects — a Δ-stepping shortest-path implementation
> handling 2 million vertices and 300 million edges, and a GPU image preprocessing pipeline — and
> some deep learning work, including a Transformer built from scratch.
>
> I'm interested in this role because it's the systems end of the stack, which is where I've
> deliberately pointed myself."

**Word count ≈ 215. Spoken at a natural pace, that is about 90 seconds.** Time yourself.

---

## 3. The ML version ⭐⭐⭐

> "I'm a second-year M.Tech CSE student at IIT Madras. My B.Tech was Mechanical Engineering, and I
> moved into CS through GATE — the short version is that the parts of mechanical engineering I
> enjoyed most were the computational ones, so I followed that.
>
> My work is mostly deep learning. I built the Transformer architecture from scratch in PyTorch for
> German-English translation — multi-head attention, positional encoding, the masking, the training
> pipeline with label smoothing and a Noam schedule — and reached 34.8 BLEU on Multi30k. I've also
> built a multi-task vision model where one VGG encoder feeds classification, localisation and
> segmentation heads simultaneously, which turned out to be mostly a question of how you balance
> three losses that operate at different scales. And an information retrieval engine where I
> benchmarked six ranking systems and validated the improvement with a Wilcoxon signed-rank test,
> because a better number isn't the same as a real improvement.
>
> My M.Tech project is on the systems side — parallelising a compiler analysis on GPUs — and I
> chose that deliberately, because ML is increasingly a systems problem and I wanted to understand
> the layer underneath the framework.
>
> I'm interested in this role because <specific reason>."

⭐ Note how the last paragraph **pre-empts** the obvious question ("why is your thesis a compiler
project?") rather than waiting to be asked. Pre-empting converts a weakness into a deliberate
choice.

---

## 4. The 30-second version ⭐

Some interviewers say "briefly". Have this ready:

> "Second-year M.Tech CSE at IIT Madras, after a Mechanical B.Tech — I switched through GATE. I
> work on GPU and parallel systems; my M.Tech project parallelises a compiler points-to analysis on
> CUDA. I've also done CUDA graph algorithms and from-scratch deep learning work, and I TA the
> Advanced DS&A course here."

---

## 5. Line-by-line: what each resume item invites ⭐⭐⭐

Every line on your resume is a question waiting to be asked. Know which.

| Resume line | The question it invites | Where the answer lives |
|---|---|---|
| **M.Tech CSE, IIT Madras, 7.5** | "Why is your M.Tech CGPA lower than your B.Tech?" | `../04_HR_and_Behavioral/04-Difficult_Questions.md` §2 |
| **B.Tech Mechanical, IIITDM** | "Why did you switch?" — **guaranteed** | `../04_HR_and_Behavioral/01-The_Mechanical_to_CS_Story.md` ⭐⭐⭐ |
| **Infosys Springboard, Virtual Intern** | "What did you actually do there?" / "Why no industry internship?" | `../02_Project_Deep_Dives/08-Gesture_Volume_Infosys_Internship.md` |
| **Points-to Analysis on GPU** | "Explain points-to analysis." / "Why a GPU?" / "What's yours vs the paper's?" | `../02_Project_Deep_Dives/01-...` |
| **PInter (CC'26)** | "Tell me about the paper." ⚠️ | Know its contribution in one sentence |
| **96% / 78%** | "Which part is your contribution?" | Project file §11 — be scrupulous |
| **Δ-stepping SSSP** | "Why doesn't Dijkstra parallelise?" | `../02_Project_Deep_Dives/02-...` |
| **atomicMin** | "Why is that safe without a lock?" | Commutative + associative ⇒ order-independent |
| **CSR** | "Why CSR? What's its weakness?" | `../01_Technical_Round_Prep/01-GPU...` §5 |
| **Nsight Compute** | "What did the profiler tell you?" ⚠️ | Have a specific metric, not a vague answer |
| **Image preprocessing, align-corners** | "What is align-corners?" | `../02_Project_Deep_Dives/03-...` §4 |
| **Transformer from scratch** | "Derive attention." / "Why √d_k?" / "Explain the masks." | `../02_Project_Deep_Dives/04-...` |
| **34.8 BLEU** | "Is that good? On what?" | Multi30k — small, clean, single-domain. Say so |
| **Noam scheduler** | "Why does warmup matter?" | Adam's v̂ unreliable early |
| **Multi-task, VGG11 from scratch** | "How did you balance the three losses?" | `../02_Project_Deep_Dives/05-...` §4 |
| **50% loc. @ IoU ≥ 0.75** | "That's low — why?" | Project file §6 — three reasons, two fixes |
| **Wilcoxon, Cohen's d** | "Why non-parametric? What does d mean?" | `../02_Project_Deep_Dives/06-...` §4 ⭐ |
| **MAP 0.2691 → 0.3304** | "Why did BM25 win?" | Saturation (k₁) + length normalisation (b) |
| **Languages: C++, CUDA C++, C, Java, Python** | Three questions on each ⚠️ | `02-Skill_Claims_Defence.md` |
| **TA, CS5800 Advanced DS&A** | "What do students struggle with?" ⭐ | A gift of a question — see §6 |
| **Subsystem Lead, Aero Design Club** | "Tell me about leading that team." | `../04_HR_and_Behavioral/02-STAR_Story_Bank.md` |

---

## 6. The TA line is an under-used asset ⭐⭐

You TA **CS5800 Advanced Data Structures and Algorithms**. In a DSA interview, that is directly
relevant and almost nobody in your position mentions it.

**Use it when:**
```
- Introducing yourself: "...and I TA the Advanced DS&A course here."
- Asked about teaching/communication: you have a real answer, not a hypothetical one
- Asked "how do you approach explaining something complex?" — you do it weekly
- After solving a problem: "This is actually one students find hard for a specific reason —
  they try to memorise the recurrence instead of deriving the state." ⭐
```

**The question it invites, and your answer:**
> *"What do students most commonly get stuck on?"*
> "Dynamic programming, almost universally — and the pattern is always the same. They try to
> recall a recurrence instead of asking what state is sufficient to describe a subproblem. Once
> they start by writing down what the state *means* rather than what the formula *is*, most of them
> unblock. Teaching it has made me much more disciplined about doing that myself."

⭐ That answer demonstrates DSA competence, teaching ability and self-awareness in four sentences.

---

## 7. Steering the interview ⭐⭐

The walkthrough sets the agenda. Choose your emphasis deliberately:

```
For a SYSTEMS/HPC company : spend most of the [WHAT] on the GPU work. Mention ML briefly.
For an ML company         : lead with the Transformer and multi-task work. Frame the GPU
                            project as systems depth underneath ML.
For an ML-INFRA company   : ⭐ lead with BOTH and name the intersection explicitly.
                            This is your best-fit category and the least crowded.
For a generalist company  : lead with breadth and the TA role; emphasise you can go deep on
                            either side.
```

⚠️ **Whatever you name last is what they usually ask about first.** End the [WHAT] section on the
project you most want to discuss.

---

## 8. Rehearsal protocol ⭐⭐⭐

```
□ Write your own version — do not read mine verbatim; it must sound like you
□ Say it ALOUD, timed. Target 90 seconds. Most people run to 3 minutes on the first try
□ Record it once and watch it back. Cut every "basically", "actually", "kind of"
□ Practise the 30-second version separately — it is NOT the 90-second one compressed live
□ Practise being INTERRUPTED at 40 seconds ("sorry, tell me more about the GPU part") —
  you must be able to switch without losing your place ⭐
□ Rehearse until it is fluent, then STOP rehearsing. Over-rehearsed sounds recited,
  and recited sounds insincere.
```

**The test:** can you deliver it while slightly nervous, at a natural pace, without the word
"basically"? If yes, it is ready.

---

## Recall questions

1. Give the four-part structure and the time budget for each.
2. What is the most common opening mistake?
3. Which version do you use for an ML-infrastructure company, and why is that category
   under-served?
4. Name five resume lines and the exact question each invites.
5. How do you use the TA line, and what question does it set up?
6. What should you do after the walkthrough is fluent?
