
# Target List and Fit Analysis

> **⭐⭐⭐ Read this before anything else in this folder.**
>
> Placement season is a constrained-resource problem: you have limited preparation time and limited
> interview slots. Treating every company as equally worth preparing for wastes both. This file
> ranks your realistic targets **against your actual profile** and says where to spend the effort.
>
> Profile basis: M.Tech CSE IIT Madras (CGPA 7.5, 2027), B.Tech Mechanical IIITDM (7.95, 2024),
> CUDA/compilers M.Tech project, three GPU projects, from-scratch deep learning, TA for Advanced
> DS&A. Full analysis in `../07_Interviews/README.md` §1.

---

## 1. Your edge, stated plainly ⭐⭐⭐

```
RARE        : CUDA + compilers + deep learning, with a flagship project at the intersection.
              Very few campus candidates have all three; almost none have the compiler half.
COMMON      : "I know DSA and I've done some ML projects." — true of most of your batch.
WEAK        : no industry internship; CGPA 7.5; thin DP; little team-codebase experience.
```

**The strategic conclusion:** compete where your rare thing is *valued*, not where it is merely
interesting. In a generic SDE funnel your CUDA work is a nice conversation; in an HPC, compiler,
or ML-infrastructure funnel it is the reason you get hired.

---

## 2. The tier table ⭐⭐⭐

**Fit** = how much your specific profile is worth there. **Odds** = realistic conversion given your
CGPA, no-internship gap and DSA level. **Effort** = marginal preparation needed beyond your baseline.

| Company / group | Tier | Fit | Odds | Effort | Why |
|---|---|---|---|---|---|
| **NVIDIA** | A | ⭐⭐⭐ | Medium | Low | CUDA, Nsight, kernel work — you have *exactly* the portfolio. Your best single fit |
| **Qualcomm** | A | ⭐⭐⭐ | Medium-High | Medium | Systems + embedded C/C++ + architecture. Large IIT-M intake |
| **Samsung R&D (SRIB)** | A | ⭐⭐⭐ | Medium-High | Medium | One hard algorithmic problem, C/C++ only, no STL in some variants — suits a C++ systems person |
| **Arcesium** | A | ⭐⭐ | Medium | Medium | DSA + fundamentals + some quant flavour; strong IIT recruiter |
| **AMD / Intel / Micron / TI** | A | ⭐⭐⭐ | Medium | Medium | Same argument as NVIDIA/Qualcomm. Copy `_TEMPLATE/` when they announce |
| **Amazon** | B | ⭐⭐ | Medium | Medium | Biggest recruiter; pure DSA + Leadership Principles. Your CUDA work is neutral here |
| **Microsoft** | B | ⭐⭐ | Medium | Medium | Codility; C++ is welcome; linked lists/strings emphasis |
| **Google** | B | ⭐⭐ | Low-Medium | **High** | Hardest algorithmic bar; your DP gap is the binding constraint ⚠️ |
| **Adobe** | B | ⭐⭐ | Medium | Low | DSA + **C/C++ output prediction** + CS fundamentals — plays to your strengths |
| **Oracle** | B | ⭐⭐ | Medium | **High** | Heavy DBMS/SQL **and Java** — your weakest claimed skill ⚠️ see §5 |
| **Flipkart / Walmart** | B | ⭐⭐ | Medium | Medium | LeetCode Medium; machine-coding round later |
| **Goldman Sachs** | C | ⭐⭐ | Medium | Medium | CS fundamentals + moderate DSA + HireVue video round |
| **Quant trading firms** | C | ⭐ | Low | **Very High** | Mental arithmetic + probability. You have no probability project; 8-10 weeks of dedicated prep ⚠️ |
| **Sprinklr / Media.net / Codenation** | D | ⭐⭐ | Low-Medium | **High** | Competitive-programming difficulty |
| **Databricks / Rubrik / Nutanix** | B/D | ⭐⭐⭐ | Low-Medium | High | Systems-heavy product companies — excellent fit, high bar. Worth a folder if they visit |
| **Service companies (TCS/Infosys/Wipro/etc.)** | — | ⭐ | High | Low | Safety net only. Your profile is substantially over-qualified |

---

## 3. Where to spend your preparation time ⭐⭐⭐

```
50%  DSA, WITH DP AS THE PRIORITY
     The single highest-leverage thing you can do. It is the binding constraint for Tier B
     and Tier D, and it is the one gap that shows up on every resume read.
     → ../07_Interviews/01_Technical_Round_Prep/02-DSA_Round_Playbook.md §5

20%  PROJECT NARRATIVE
     Your projects ARE your differentiator, and they are only worth what you can explain.
     The points-to project especially — if a non-compiler person cannot follow it, your
     flagship is a liability.
     → ../07_Interviews/02_Project_Deep_Dives/

15%  CS FUNDAMENTALS + C++ + COMPILERS
     Cheap marks for Tier A and Adobe. The compiler questions are free for you.
     → ../07_Interviews/01_Technical_Round_Prep/03-CS_Fundamentals_Rapid_Fire.md

10%  THE NARRATIVE RISKS
     Mechanical→CS, CGPA, no internship. Rehearsed until calm and brief.
     → ../07_Interviews/04_HR_and_Behavioral/

 5%  APTITUDE
     Only if service companies or Goldman-style aptitude sections are on your list.
     → ../06_Online_Assessments/02_Aptitude_and_Quant/
```

⚠️ **Note what is NOT on this list: quant probability.** Unless you decide to commit 8-10 weeks to
it, the quant firms are a lottery ticket rather than a target. That is a legitimate choice — just
make it deliberately rather than by drifting into their tests unprepared. See §5.

---

## 4. The positioning decision you need to make ⭐⭐

You have three plausible self-presentations. **Pick one per company, and do not hedge.**

| Positioning | Pitch in one line | Best for | Resume to send |
|---|---|---|---|
| **Systems / HPC engineer** | "I parallelise irregular workloads on GPUs and I profile what I write." | NVIDIA, Qualcomm, Samsung, AMD, TI | SDE |
| **Generalist SDE with systems depth** | "Strong C++ and algorithms, with real performance-engineering experience." | Amazon, Microsoft, Adobe, Flipkart, Arcesium | SDE |
| **ML engineer with systems depth** ⭐ | "I've built deep learning from scratch *and* written the CUDA underneath it." | ML-infrastructure teams, NVIDIA's ML stack, inference teams | ML (reordered — see audit) |

⭐ **The third is your least crowded market.** Almost every ML applicant can fine-tune a model;
almost none can explain why FlashAttention works at the memory-traffic level. If an ML-infra role
appears, that is your highest-probability strong offer.

⚠️ **Do not present as a pure ML researcher.** Your CGPA, the absence of a publication, and a
systems M.Tech project make that the weakest available framing.

---

## 5. Honest go/no-go calls ⭐⭐

Decisions worth making **now**, before the season compresses your judgement.

<details><summary>Quant trading firms — go or no-go?</summary>

**The cost:** 8-10 weeks. Daily mental-arithmetic drills (80 questions in 8 minutes is the standard
Optiver-style bar), probability and expected value to a level your coursework has not covered, the
puzzle canon, and estimation. You have no probability or statistics project on your resume.

**The return:** very high compensation, genuinely interesting work, extremely low conversion rates.

**The honest read:** your profile is a systems profile, not a quant profile. The marginal 8-10
weeks spent on DP, project narrative and Tier A preparation almost certainly yields more expected
value than the same time spent on probability.

**RECOMMENDED:** sit the tests if they come to campus (there is no cost to attempting), but do
**not** reallocate preparation time to them. Do the 20-minute arithmetic drill if you enjoy it;
skip the 10-week programme.
→ `Quant_Trading_Firms/` has the minimum-viable preparation if you change your mind.
</details>

<details><summary>Google — worth the high effort?</summary>

**The constraint is DP and observation-driven problems**, which is exactly your gap. Google's bar
is the highest in Tier B and your conversion odds are the lowest.

**But:** the preparation for Google is *the same preparation* that lifts Amazon, Microsoft,
Flipkart, Sprinklr and Arcesium. Unlike quant prep, it is not specialised — it is the general DSA
work you need anyway, done harder.

**RECOMMENDED: yes, prepare for it**, because the effort is not wasted if you fail. Just do not
treat it as your primary target.
</details>

<details><summary>Oracle — is the Java gap disqualifying?</summary>

Oracle weights DBMS/SQL heavily (which you can learn fast) **and Java** (your weakest claimed
skill — you analyse Java in your M.Tech project but may not write it).

Two options: spend two weeks on real Java fundamentals (collections, generics, concurrency, JVM
memory model, GC) so the claim is solid, or use the honest framing in
`../07_Interviews/03_Resume_and_Portfolio/02-Skill_Claims_Defence.md` §3 and accept that a
Java-specific round may go badly.

**RECOMMENDED:** the honest framing, plus the two-week Java sprint **only if** Oracle or a
Java-heavy enterprise company is actually high on your list. Otherwise de-prioritise Java and lean
on C++.
</details>

<details><summary>Service companies — apply or not?</summary>

Your profile is well above their bar. The argument for applying is purely insurance: a confirmed
offer early removes a great deal of anxiety from the rest of the season, and anxiety costs
performance.

**RECOMMENDED:** follow your placement cell's rules on this (many restrict what you can hold while
interviewing elsewhere). If the rules permit it and it costs you one afternoon, take the insurance.
</details>

---

## 6. The shortlist to actually work from ⭐⭐⭐

Ranked by expected value for *you*, not by prestige:

```
 1. NVIDIA                  — best fit, prepare specifically
 2. Qualcomm                — best fit × high intake
 3. Samsung R&D (SRIB)      — best fit × format suits you
 4. Amazon                  — volume; highest absolute number of offers
 5. Microsoft               — good fit, C++ welcome
 6. Adobe                   — C/C++ output prediction plays to you
 7. Arcesium                — DSA + fundamentals, strong IIT recruiter
 8. Flipkart / Walmart      — standard Medium bar
 9. AMD / TI / Intel / Micron — same argument as 1-3, when they announce
10. Google                  — high effort, but the effort transfers
11. Goldman Sachs           — moderate bar, prepare the video round
12. Oracle                  — only with the Java decision made
13. Sprinklr / Media.net    — attempt, do not optimise for
14. Quant firms             — attempt, do not prepare for (see §5)
```

⭐ **Update this list as the season progresses** and as you learn which companies are actually
visiting. A ranking you revise is useful; one you wrote once and ignored is not.

---

## 7. The reality check ⚠️

```
You will not get an offer from your top choice by preparing for fourteen companies equally.
You will get one by being clearly, specifically excellent at the thing three of them want.

For you that thing is: "I parallelise irregular workloads on GPUs, I profile what I write,
and I can explain the compiler analysis underneath it to someone who has never seen one."

Everything in this folder is downstream of making that sentence true and well-rehearsed.
```
