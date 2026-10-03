
# Quant and HFT Firms — Process and OA Pattern

> Covers: **DE Shaw, Tower Research, Graviton, Quadeye, Optiver, WorldQuant, Jane Street,
> Citadel Securities, IMC, APT Portfolio, Da Vinci**.

```
INFORMATION QUALITY : Low-Medium  — compiled from general knowledge, not from a 2026 campus notice
LAST VERIFIED       : not yet verified for this season
⚠️ RUN ../00-Research_Protocol.md BEFORE RELYING ON SECTION 1. The volatile table is a
   starting point to overwrite. What is durable here is sections 3-5.
```

> ## ⚠️⭐⭐⭐ READ THIS FIRST — the strategic call
>
> **Recommendation: attempt these tests, but do not reallocate preparation time to them.**
>
> The honest analysis (full version in `../00-Target_List_and_Fit.md` §5): these firms test
> **mental arithmetic at extreme speed, probability, and estimation** — none of which appear
> anywhere on your resume. Getting competitive requires 8-10 weeks of dedicated work, and
> conversion rates are very low even for well-prepared candidates.
>
> The same 8-10 weeks spent on **DP, project narrative and Tier A preparation** almost certainly
> produces more expected value for your profile, which is a systems profile rather than a quant one.
>
> **So:** sit the tests when they come to campus (there is no cost to attempting), do the
> 20-minute arithmetic drill if you enjoy it, and skip the ten-week programme. This file contains
> the **minimum-viable preparation** in case you change your mind or get through to an interview.

---

## 1. The volatile table ⚠️

| Field | Value (starting point) | Verified |
|---|---|---|
| Role titles | Quant Trader, Quant Researcher, Quant Developer, Software Engineer (HFT) ⭐ | ☐ |
| Eligibility | Usually a high CGPA bar ⚠️ many are 8.0+ — **check, you may not clear it** | ☐ |
| Rounds | Mental-maths test → probability/aptitude → technical/coding → trading games → final | ☐ |
| Signature test | **80 questions in 8 minutes** arithmetic (Optiver-style) = **6 seconds each** ⚠️ | ☐ |
| Coding | Varies: none, Medium-Hard DSA, or low-latency C++ ⭐ | ☐ |
| Locations | Mumbai, Gurugram, Bengaluru, Hyderabad | ☐ |

⚠️ **Check the CGPA bar first.** Several of these firms screen at 8.0+ and your 7.5 may not clear
the resume filter, which would make any preparation moot.

---

## 2. The two sub-families ⭐⭐

```
A. QUANT TRADER / RESEARCHER        B. QUANT DEVELOPER / HFT ENGINEER  ⭐ your path, if any
   ───────────────────────────         ──────────────────────────────────
   Mental arithmetic at speed          Medium-Hard DSA
   Probability and expected value      LOW-LATENCY C++ ⭐⭐ — cache-friendly layout, avoiding
   Market-making games                 allocation in hot paths, branch prediction, lock-free
   Estimation / Fermi questions        Systems knowledge: memory hierarchy, false sharing
   Puzzles                             Some probability
   Little or no coding                 Less emphasis on mental arithmetic
```

⭐⭐ **If you pursue these firms at all, target family B.** The **Quant Developer / HFT Engineer**
track is a *systems* role: cache-aware C++, latency measurement, lock-free data structures, memory
layout. That is genuinely your profile, and your CUDA performance-engineering work is directly
relevant. The trader track is not.

---

## 3. What they optimise for ⭐⭐

```
TRADER TRACK     : speed and calibration under pressure. Can you compute fast, estimate
                   sensibly, and update on new information without freezing?
DEVELOPER TRACK  : can you make C++ fast and prove it? Do you think in cache lines and
                   nanoseconds?  ⭐ this is the same instinct as your GPU work
```

**Topic emphasis — developer track (your path):**
```
1. LOW-LATENCY C++ ⭐⭐⭐ — cache locality, avoiding heap allocation in hot paths, false sharing,
   branch prediction, `std::vector` vs `std::list` reality, move semantics, lock-free basics
2. Memory hierarchy and architecture — exactly the content in your GPU preparation
3. DSA Medium-Hard
4. Probability and expected value (lighter than the trader track, but present)
5. Latency measurement — how do you actually time something at the nanosecond scale?
```

---

## 4. Your fit ⭐ (trader) / ⭐⭐ (developer)

| | Assessment |
|---|---|
| Fit | **⭐ for trader roles; ⭐⭐ for quant-developer / HFT-engineer roles** |
| Why | No probability or statistics project; no arithmetic-speed training. But your C++ performance engineering is real and directly relevant to the developer track |
| Biggest advantage | ⭐ For the developer track: *"I've profiled CUDA kernels with Nsight and fixed coalescing, divergence and atomic contention"* is a latency-engineering story that most applicants cannot tell |
| Biggest risk | ⚠️ The CGPA bar may filter you out before anything else; and the arithmetic test is a pure drill skill you have not drilled |
| Positioning | Systems engineer — explicitly target the developer track |
| Resume | SDE version |

**Which projects to lead with (developer track):**
```
1. Points-to Analysis on GPU   — the bottleneck analysis: three named bottlenecks, each fixed
                                 with a measured result. That IS latency engineering.  ⭐
2. Parallel SSSP on GPU        — memory-bound reasoning, O(|V|) memory decision
3. Image Preprocessing on GPU  — memory layout and coalescing
```

---

## 5. Minimum-viable preparation ⭐⭐

**If you are sitting a test next week with no prior preparation, do only this:**

```
MENTAL ARITHMETIC (20 min/day for a week — the highest-return item)
  □ Multiplication tables to 25×25
  □ Squares to 40; cubes to 20; powers of 2 to 2²⁰
  □ The fraction ↔ percentage table ⭐⭐⭐ (1/2 through 1/16, and the 1/7 family)
  □ ×11, ×5, ×25, ×9 shortcuts; squares ending in 5; a² − b² = (a+b)(a−b)
  → ../../06_Online_Assessments/02_Aptitude_and_Quant/00-Core_Arithmetic_Toolkit.md

PROBABILITY (3 hours total)
  □ Conditional probability and Bayes — the natural-frequency method ⭐
  □ Expected value, and LINEARITY OF EXPECTATION ⭐⭐⭐ (the single most useful tool)
  □ "At least one" via the complement
  □ Expected flips to see HH vs HT — know the answer is 6 and 4, and know WHY ⭐⭐
  → ../../06_Online_Assessments/02_Aptitude_and_Quant/Probability_and_Combinatorics.md
  → ../../05_AI_ML/02_Probability_and_Statistics/

PUZZLES (2 hours)
  □ 25 horses · 12 balls · burning ropes · bridge crossing · 100 prisoners and hats ·
    two eggs 100 floors · 1000 bottles · five pirates
  → ../../06_Online_Assessments/03_Logical_Reasoning_and_Puzzles/Common_Interview_Puzzles.md
    ⭐ Read the PATTERN notes, not just the answers — information bounds, backward induction,
      parity, equalising the worst case

ESTIMATION (30 min)
  □ The method: decompose into a product of factors, round aggressively, state assumptions,
    sanity-check the order of magnitude
```

**The full programme**, if you decide to commit:
`../../06_Online_Assessments/01_OA_Patterns_by_Company/Goldman_Sachs_and_Quant_Firms.md` §4 has the
8-10 week plan.

---

## 6. Tech stack and what to read

```
C++ (modern, performance-critical), Python (research), kdb+/q, Linux kernel tuning,
FPGA for the fastest paths, market-data protocols

READ (developer track):
  □ One article on low-latency C++ — "mechanical sympathy", cache-friendly design
  □ Refresh: false sharing, branch prediction, why std::vector beats std::list in practice ⭐
  □ How to measure latency properly (rdtsc, steady_clock, the pitfalls of micro-benchmarking)

BOOKS (if committing to the trader track):
  □ A Practical Guide to Quantitative Finance Interviews — Xinfeng Zhou (the standard text)
  □ Heard on the Street — Timothy Crack
  □ Fifty Challenging Problems in Probability — Mosteller
```

---

## 7. Cross-references

```
Full OA detail ⭐     : ../../06_Online_Assessments/01_OA_Patterns_by_Company/Goldman_Sachs_and_Quant_Firms.md
Arithmetic toolkit   : ../../06_Online_Assessments/02_Aptitude_and_Quant/00-Core_Arithmetic_Toolkit.md
Probability          : ../../06_Online_Assessments/02_Aptitude_and_Quant/Probability_and_Combinatorics.md
                       ../../05_AI_ML/02_Probability_and_Statistics/
Puzzles              : ../../06_Online_Assessments/03_Logical_Reasoning_and_Puzzles/Common_Interview_Puzzles.md
C++ / architecture   : ../../07_Interviews/01_Technical_Round_Prep/03-CS_Fundamentals_Rapid_Fire.md §6, §8
                       ../../07_Interviews/01_Technical_Round_Prep/01-GPU_and_Parallel_Computing_QA.md §6
Strategic call       : ../00-Target_List_and_Fit.md §5
```

---

## 8. Reservations and positives ⭐

```
POSITIVES:


RESERVATIONS:

```
