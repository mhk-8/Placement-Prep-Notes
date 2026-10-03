
# Quant Firms — Prep Plan, Question Bank and Debriefs

> ⚠️ Per `00-process-and-oa.md`, the recommended posture is **attempt without reallocating
> preparation time**. This file is sized for that: a one-week minimum-viable plan, not a ten-week
> programme.

---

## 1. Topic priorities ⭐

| Priority | Topic | Source | Status |
|---|---|---|---|
| **P1** | **Mental arithmetic drill** — 20 min/day | `../../06_Online_Assessments/02_Aptitude_and_Quant/00-Core_Arithmetic_Toolkit.md` | ☐ |
| **P1** | Probability: Bayes (natural frequencies), expected value, linearity | `../../06_Online_Assessments/02_Aptitude_and_Quant/Probability_and_Combinatorics.md` | ☐ |
| **P1** | The puzzle canon + its **patterns** | `../../06_Online_Assessments/03_Logical_Reasoning_and_Puzzles/Common_Interview_Puzzles.md` | ☐ |
| **P2** | **Low-latency C++** ⭐ — if targeting the developer track | `../../07_Interviews/01_Technical_Round_Prep/03-CS_Fundamentals_Rapid_Fire.md` §6 | ☐ |
| **P2** | Estimation / Fermi method | `00-process-and-oa.md` §5 | ☐ |
| **P3** | Markov chains, random walks, gambler's ruin — only if committing | `../../05_AI_ML/02_Probability_and_Statistics/` | ☐ |

---

## 2. The one-week plan ⭐⭐

| Date | Task | Done |
|---|---|---|
| D-7 | **Check the CGPA bar** ⚠️ — if you do not clear it, stop here | ☐ |
| D-7 | Arithmetic drill (20 min); memorise the fraction↔percentage table | ☐ |
| D-6 | Arithmetic drill; Bayes with natural frequencies; 5 worked problems | ☐ |
| D-5 | Arithmetic drill; expected value + linearity of expectation; the HH-vs-HT derivation ⭐ | ☐ |
| D-4 | Arithmetic drill; 8 puzzles from the canon, reading the PATTERN notes | ☐ |
| D-3 | Arithmetic drill; 8 more puzzles; 5 estimation questions aloud | ☐ |
| D-2 | Arithmetic drill; low-latency C++ refresh (false sharing, cache locality, allocation) | ☐ |
| D-1 | Arithmetic drill only. Nothing new ⚠️ | ☐ |

⭐ **The arithmetic drill every single day is the only non-negotiable item.** It is a pure drill
skill that improves fast for about three weeks and then plateaus — so even one week helps
measurably, which is not true of the probability content.

---

## 3. Seeded question bank

### The arithmetic test ⚠️⭐⭐⭐
```
FORMAT  : commonly 80 questions in 8 minutes = 6 SECONDS each. You cannot compute; you must know.
STRATEGY: skip INSTANTLY if unsure. Accuracy on 60 attempted beats guessing on 80.

MEMORISE:
  □ Multiplication tables to 25×25
  □ Squares 1-40; cubes 1-20; powers of 2 to 2²⁰ = 1,048,576
  □ Fractions → decimals → percentages: all n/2 … n/16, plus the 1/7 family (142857 rotating)
  □ √2=1.414 √3=1.732 √5=2.236 √7=2.646

TECHNIQUES:
  ×11       : 43×11 → 4|7|3 = 473 (carry if the middle sum exceeds 9)
  ×5        : ×10 ÷2            ×25 : ×100 ÷4            ×125 : ×1000 ÷8
  ×9, ×99   : ×10 − itself, ×100 − itself
  d5²       : d(d+1)|25  →  75² = 7×8|25 = 5625
  a²−b²     : (a+b)(a−b)  →  53² − 47² = 100×6 = 600
  Near 100  : 98×102 = 100² − 2² = 9996
  37.5% = 3/8 · 16⅔% = 1/6 · 6.25% = 1/16 · 12.5% = 1/8
```

### Probability ⭐⭐
```
□ Conditional probability and Bayes — ALWAYS give the natural-frequency version ⭐
  "Out of 100,000 people: 100 have the disease, 99 test positive; 99,900 healthy, 4,995 test
   positive; so 99/5,094 ≈ 1.9%."
□ Expected value of a game; would you pay $X to play?
□ LINEARITY OF EXPECTATION ⭐⭐⭐ — write the count as a sum of indicators. Dependence does
  not matter. (Expected fixed points in a random permutation = 1, for every n.)
□ "At least one" → use the complement
□ Expected flips to first see HH = 6; to first see HT = 4  ⭐⭐ know the state-equation derivation:
     E_S = 1 + ½E_H + ½E_S ;  E_H = 1 + ½·0 + ½E_S  →  E_H = 4, E_S = 6
  The asymmetry: a failed HH attempt loses all progress; a failed HT attempt keeps you in state H
□ Two children / Monty Hall / boy-girl wording traps ⚠️ the wording determines the answer
□ Gambler's ruin; expected time to hit a boundary (if committing further)
```

### Puzzles — know the PATTERN, not just the answer ⭐⭐
```
□ 25 horses (7 races)              → elimination by transitivity
□ 12 balls, heavy or light (3)     → information bound: 3^k ≥ outcomes
□ 1000 bottles, 10 prisoners       → parallel binary encoding
□ Burning ropes (45 min)           → change the rate, not the measurement
□ Bridge crossing (17 min)         → pair the expensive items
□ 100 prisoners and hats (99)      → parity as a broadcast bit
□ Two eggs, 100 floors (14)        → equalise the worst case
□ Five pirates                     → backward induction
□ Water jugs                       → gcd invariant
□ 100 doors                        → reframe process as property (perfect squares)
```

### Low-latency C++ ⭐ (developer track)
```
□ Why does std::vector usually beat std::list? (cache locality — the pointer chase dominates)
□ What is false sharing and how do you fix it? (pad to a cache line)
□ Why avoid heap allocation in a hot path? (unpredictable latency, cache pollution,
  possible lock contention in the allocator)
□ Branch prediction — how do you write branch-friendly code? When is branchless better?
□ What is a cache line, and what is the cost of a DRAM miss? (~64 bytes; few hundred cycles)
□ Move semantics; what std::move actually does
□ RAII and why it matters for exception safety
□ Lock-free basics: std::atomic, compare-and-swap, the ABA problem, memory ordering
□ How would you measure a 100-nanosecond difference reliably?
⭐ YOUR ANSWER TO ALL OF THESE connects to real work: "I fixed uncoalesced access, warp
  divergence and atomic contention in a CUDA kernel, measured with Nsight." That is the same
  instinct at a different layer.
```

### Market-making games (trader track)
```
□ You quote a bid-ask on an unknown quantity; others trade against you
□ Principles: quote WIDE when uncertain, narrow as you learn; if someone lifts your offer
  instantly your price was too low — UPDATE UPWARD; think in expected value; manage position size
```

### Estimation / Fermi
```
"How many piano tuners in Chennai?" · "Daily revenue of a metro station?" ·
"How many tennis balls fit in a Boeing 747?"
METHOD: decompose into a product of factors each estimable within 2×; round aggressively
        (3×10⁶, not 2,847,391); state assumptions aloud; sanity-check the magnitude
```

---

## 5. Debriefs ⭐⭐⭐

Full template: `../../07_Interviews/06_Post_Interview_Debriefs/Debrief_Template.md`

### Round 1 — `<date>` — `<type>`
```
INTERVIEWER        :
STRUCTURE / TIMING :
QUESTIONS ASKED (verbatim):
  1.
  2.
  3.
CODING PROBLEM     :
  My approach     :
  Correct approach (write it out NOW):
WHAT I FUMBLED, AND THE RIGHT ANSWER:
  1.
  2.
TONE / STYLE FOR THE NEXT ROUND:
OUTCOME            :
THREE ACTIONS:
  1.
  2.
  3.
```

### Round 2 — `<date>` — `<type>`
```
(same structure)
```

### Round 3 — `<date>` — `<type>`
```
(same structure)
```

---

## 6. Questions I collected myself ⭐⭐⭐

| Date | Source (senior / my attempt) | Round | Question | Topic |
|---|---|---|---|---|
| | | | | |
| | | | | |
| | | | | |

---

## 7. Corrections to make to `00-process-and-oa.md`

```
□
□
```


---

## 8. The 30-second pre-test refresh ⭐

```
□ Arithmetic test: 6 seconds per question. SKIP INSTANTLY if unsure
□ Fractions: 3/8 = 37.5% · 1/6 = 16⅔% · 1/16 = 6.25% · 1/7 = .142857 rotating
□ d5² = d(d+1)|25 ; a²−b² = (a+b)(a−b) ; ×11 = outer digits with the sum between
□ Bayes → natural frequencies, always
□ Linearity of expectation works even for DEPENDENT events — write the sum of indicators
□ HH = 6 flips, HT = 4. Know why
□ Sanity check every probability: between 0 and 1? Expected value the right magnitude?
□ Developer track: my latency story is the CUDA bottleneck analysis with Nsight
```
