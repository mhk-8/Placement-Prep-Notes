
# Goldman Sachs, Quant and HFT Firms — Online Assessments

> **Accuracy note.** OA formats change every recruiting season, and companies run different
> papers for different campuses, roles and dates. Treat everything below as the *shape* of the
> test — what it measures and how to prepare — and verify the exact duration, section count and
> cutoff against this year's campus notice and last year's seniors before you sit the test.


Covers: **Goldman Sachs, Morgan Stanley, JP Morgan, DE Shaw, Tower Research, Optiver, Jane Street,
Quadeye, Graviton, WorldQuant, Citadel/Citadel Securities**.

This is the hardest OA family on campus, and it is hard in a *different way*: less "implement
Dijkstra", more **probability, mental maths under extreme time pressure, and estimation**.

---

## 1. Two distinct sub-families ⭐⭐⭐

```
 A. INVESTMENT BANK / TECH-IN-FINANCE          B. QUANT TRADING / HFT
    Goldman Sachs, Morgan Stanley, JPMC           Optiver, Jane Street, Tower, DE Shaw,
                                                  Quadeye, Graviton, Citadel Securities
    ─────────────────────────────────            ──────────────────────────────────────
    CS fundamentals + DSA + aptitude              PROBABILITY + mental maths + estimation
    + some finance/market awareness               + market-making games + puzzles
    Coding: Easy-Medium                           Coding: Medium-Hard, or none at all
    Platform: HackerRank / HireVue                Platform: custom timed tests, often brutal
```

Prepare for the one you are actually applying to. The overlap is probability and mental arithmetic.

---

## 2. Goldman Sachs (representative of family A)

| Item | Typical |
|---|---|
| Platform | HackerRank; plus **HireVue** recorded video interview for some tracks |
| Sections | Aptitude/numerical, logical reasoning, **CS fundamentals MCQ**, **2 coding problems**, sometimes a subjective/essay and a video round |
| Duration | ~2-3 hours across sections |
| Coding band | Easy to Medium |

**CS fundamentals in the MCQ:** data structures and complexity, OS (processes, threads, deadlock,
scheduling), DBMS (normalisation, ACID, SQL), networks (TCP/UDP, HTTP), OOP, and basic security
(hashing vs encryption, symmetric vs asymmetric).

**Coding topics:** arrays, strings, hash maps, sorting, greedy, simple DP, occasionally a tree or
graph. Add **financial-flavoured wrappers**: stock buy/sell problems, portfolio balancing, order
matching, interest computations.

⭐ The classic family: **Best Time to Buy and Sell Stock** in all its variants (one transaction,
unlimited, at most k, with cooldown, with fee). Know all five.

**HireVue video round:** recorded answers to behavioural prompts with ~30 s prep and ~2-3 min to
answer. Practise: record yourself, watch it back, cut filler words, look at the camera not the
screen. Prepare STAR stories for teamwork, failure, leadership, conflict, and "why finance".

---

## 3. Quant trading firms (family B) ⭐⭐⭐

### 3.1 The mental-maths test
Several firms (Optiver most famously) run an **80-questions-in-8-minutes** arithmetic test: no
calculator, whole-number and decimal arithmetic, negative numbers, percentages and fractions.

That is **6 seconds per question**. You cannot compute; you must *know*.

**What to memorise cold:**

| Item | Range |
|---|---|
| Multiplication tables | up to **25 × 25** |
| Squares | 1-40 |
| Cubes | 1-20 |
| Powers of 2 | up to 2²⁰ = 1,048,576 |
| Fraction → decimal → percentage | all n/2 … n/16, plus 1/7 family |
| Square roots | √2 = 1.414, √3 = 1.732, √5 = 2.236, √7 = 2.646 |
| Common logs | log 2 = 0.301, log 3 = 0.477, log 7 = 0.845 |

**Techniques to drill:**
```
Multiply by 11        : 43 × 11 → 4 (4+3) 3 → 473
Multiply by 5         : ×10 ÷2
Multiply by 25        : ×100 ÷4
Squares near 50       : 47² = (50−3)² = 2500 − 300 + 9 = 2209
Squares ending in 5   : 65² = 6×7 | 25 = 4225
a² − b² = (a+b)(a−b)  : 53² − 47² = 100 × 6 = 600
Near-100 products     : 98 × 102 = 100² − 2² = 9996
Percentages           : 37.5% = 3/8;  16.67% = 1/6;  6.25% = 1/16
Division by 5, 25, 125: multiply by 2, 4, 8 and shift the decimal
```

> **Practice protocol:** 10 minutes a day of timed arithmetic (there are free Optiver-style
> trainers online, or generate your own with a script). Track your score daily. This is a pure
> drill skill — it improves fast and then plateaus, so start early.

### 3.2 Probability and expected value ⭐⭐⭐
The core intellectual content. Expect:

```
- Conditional probability and Bayes (the medical-test / two-children / Monty Hall family)
- Expected value of a game, and whether you would pay $X to play it
- Linearity of expectation problems (the single most useful tool) ⭐
- Random walks: expected time to hit a boundary, gambler's ruin
- Coin/dice sequence problems: expected flips until HH vs HT ⭐ (classic, and the answers differ!)
- Card problems: expected number of ..., probability of ...
- Continuous: uniform, exponential, normal; order statistics of uniforms
- Markov chains: stationary distribution, expected hitting time
- Variance and covariance computations
- Combinatorics: stars and bars, inclusion-exclusion, derangements
```

**Worked example — the classic that separates candidates.**

*Expected number of fair-coin flips to first see the pattern HH? And HT?*

Let `E_HH` be the expected flips to see HH from scratch.
Define states: `S` (nothing useful), `H` (one H so far).
```
E_S = 1 + ½·E_H + ½·E_S      (flip H → state H; flip T → back to S)
E_H = 1 + ½·0   + ½·E_S      (flip H → done; flip T → back to S)
```
From the first: `½E_S = 1 + ½E_H ⇒ E_S = 2 + E_H`.
Substitute into the second: `E_H = 1 + ½(2 + E_H) = 2 + ½E_H ⇒ E_H = 4`, so **`E_S = 6`**.

For HT:
```
E_S = 1 + ½E_H + ½E_S
E_H = 1 + ½E_H + ½·0        (flip H → stay in H; flip T → done)
⇒ E_H = 2,  E_S = 2 + E_H = 4
```
**HH takes 6 flips on average, HT takes 4.** The asymmetry surprises people: after a failed HH
attempt you lose all progress, but a failed HT attempt (another H) keeps you in state H.

> Be able to reproduce this. It or a variant appears constantly.

### 3.3 Market-making and game-theoretic rounds
Some firms run interactive games: you quote a bid-ask on an unknown quantity, others trade against
you, and you must update on the information their trades reveal.

Principles:
- **Quote a wider spread when uncertain**; narrow it as you learn.
- If someone lifts your offer immediately, your price was too low — **update upward**.
- Think in expected value, and in the information content of others' actions.
- Manage position size; do not accumulate a huge one-sided exposure.

### 3.4 Estimation / Fermi questions
"How many piano tuners in Chennai?", "How many tennis balls fit in a Boeing 747?", "What is the
daily revenue of a metro station?"

Method:
```
1. Decompose into a product of factors you can each estimate within a factor of 2
2. Round to convenient numbers (3 × 10⁶, not 2,847,391)
3. Compute in orders of magnitude
4. State your assumptions out loud, then sanity-check the final number
```

### 3.5 Puzzles
The standard canon, asked fast: 25 horses, weighing coins, 100 prisoners and hats, river crossing,
burning ropes, light bulbs, bridge crossing. See
`03_Logical_Reasoning_and_Puzzles/Common_Interview_Puzzles.md`.

### 3.6 Coding (when present)
Medium-Hard DSA, or a **low-latency C++** exercise for HFT roles: cache-friendly data layout,
avoiding allocation in hot paths, `std::vector` vs `std::list` reality, branch prediction,
lock-free basics. Some firms ask a **simulation/backtest** exercise instead.

---

## 4. Study plan (8-10 weeks, start early)

| Week | Focus |
|---|---|
| 1-2 | Mental arithmetic daily drill (10 min) + memorise the tables above. Probability basics |
| 3-4 | Conditional probability, Bayes, expected value, linearity of expectation |
| 5 | Random walks, Markov chains, gambler's ruin, hitting times |
| 6 | Combinatorics: stars and bars, inclusion-exclusion, derangements, expected-value counting |
| 7 | Puzzle canon + estimation questions (do 20 of each aloud) |
| 8 | DSA Medium-Hard; for HFT roles, C++ performance topics |
| 9-10 | Full timed mocks; HireVue practice if relevant; log everything |

---

## 5. Books and sources worth the time

- *A Practical Guide to Quantitative Finance Interviews* — Xinfeng Zhou (the standard text)
- *Heard on the Street* — Timothy Crack
- *Fifty Challenging Problems in Probability* — Frederick Mosteller
- Brainstellar / quant interview problem archives
- Your own daily arithmetic trainer

---

## 6. Test-day checklist

- [ ] Arithmetic test: **do not get stuck** — 6 seconds means skip instantly if unsure
- [ ] Probability: define the states/events explicitly on paper before computing
- [ ] Use linearity of expectation before trying anything cleverer
- [ ] Sanity-check every probability: is it between 0 and 1? Does the expected value have the right
      order of magnitude?
- [ ] Estimation: state assumptions, round aggressively, sanity-check
- [ ] In interactive rounds: think in expected value and update on others' actions
