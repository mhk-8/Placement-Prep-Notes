
# Hypothesis Testing and A/B Experiments ⭐⭐

> **Core idea in 3 lines**
> 1. A test asks: "if nothing were happening, how surprising is this data?" — the p-value is that
>    surprise, not the probability that you are right.
> 2. Power, effect size, sample size and significance are four knobs tied by one equation; fix
>    three and the fourth follows.
> 3. In ML interviews this topic appears as "how would you know your new model is actually
>    better?" — the answer is an experiment design, not a metric.

---

## 1. The framework

```
H₀  null hypothesis        "no effect" — what we try to reject
H₁  alternative            "there is an effect"
α   significance level     P(reject H₀ | H₀ true)   — Type I error rate, usually 0.05
β   P(fail to reject H₀ | H₁ true) — Type II error
1−β power                  P(detect a real effect), target 0.80
```

|  | H₀ true | H₀ false |
|---|---|---|
| **Reject H₀** | Type I (α) — false positive | correct (power) |
| **Fail to reject** | correct | Type II (β) — false negative |

Mnemonic: **Type I = convicting an innocent person; Type II = letting a guilty one go.**
In ML terms, Type I ≈ false positive ≈ 1 − precision concern; Type II ≈ false negative ≈ recall
concern.

---

## 2. p-values — say this correctly ⚠️⭐⭐⭐

```
p = P( data at least this extreme | H₀ is TRUE )
```

It is **not**:
- the probability that `H₀` is true,
- the probability that your result is a fluke,
- a measure of effect size (a huge `n` makes trivial effects "significant"),
- `p = 0.06` meaning "no effect" — it means "insufficient evidence at this α".

✅ A correct sentence to use: *"If there were truly no difference, we would see a result this
extreme only 3% of the time; that is unlikely enough that I'll act as though there is a
difference — but I also want to see the confidence interval and whether the effect is large enough
to matter."*

---

## 3. Choosing the test

```
                  What are you comparing?
                            │
        ┌───────────────────┼────────────────────┐
        ▼                   ▼                    ▼
     MEANS              PROPORTIONS         DISTRIBUTIONS /
        │                   │                ASSOCIATIONS
        │                   ▼                    │
        │            two-proportion z-test       ▼
        │            (CTR, conversion)     chi-square test
        │                                  (independence,
        ▼                                   goodness of fit)
 σ known & n large → z-test                KS test (continuous,
 σ unknown         → t-test                 also used for drift
 paired samples    → paired t-test          detection ⭐)
 >2 groups         → ANOVA (+ post-hoc)
 non-normal/ordinal→ Mann–Whitney U
                     Wilcoxon signed-rank
```

**Key statistics**

```
one-sample t:   t = (x̄ − μ₀)/(s/√n),        df = n−1
two-sample t:   t = (x̄₁ − x̄₂)/√(s₁²/n₁ + s₂²/n₂)   (Welch — do not assume equal variance)
two-proportion z: z = (p̂₁ − p̂₂)/√( p̂(1−p̂)(1/n₁ + 1/n₂) ),  p̂ = pooled rate
chi-square:     χ² = Σ (O − E)²/E,          df = (r−1)(c−1)
```

⚠️ z vs t: use t when `σ` is estimated from the sample. For `n > 30` they nearly coincide;
interviewers still expect you to name the reason (extra uncertainty from estimating `s`, heavier
tails).

---

## 4. Confidence intervals — usually better than the p-value

```
95% CI for a mean:        x̄ ± 1.96 · s/√n
95% CI for a proportion:  p̂ ± 1.96 · √( p̂(1−p̂)/n )
```

A CI tells you the effect **size** and the **precision**; a p-value only says "significant or
not". If the CI for the lift is `[0.1%, 0.4%]` the result is significant but may be commercially
irrelevant — an answer that shows product judgement. ⭐

Correct reading: "95% of intervals constructed this way would contain the true parameter" — not
"there is a 95% chance the parameter is in this interval" (frequentist parameters are fixed).

---

## 5. Sample size and power ⭐⭐

For comparing two proportions with `α = 0.05`, power `0.80`:

```
n per arm ≈ 16 · p(1 − p) / δ²          (δ = absolute lift you want to detect)
```

General form: `n ∝ σ²/δ²` — **halving the detectable effect quadruples the sample size.**

*Worked example.* Baseline conversion `p = 5%`, want to detect a relative lift of 10%
(`δ = 0.005`):

```
n ≈ 16 · 0.05 · 0.95 / 0.005² = 16 · 0.0475 / 0.000025 ≈ 30 400 per arm
```

⭐ Being able to produce an order-of-magnitude number like this on the spot is the single most
impressive thing in an A/B-testing interview. At 3 000 visitors/day/arm that is a **10-day**
experiment — so you also answer "how long should we run it?"

**Levers to reduce `n`:** accept a larger detectable effect, use a less noisy metric, use CUPED
(variance reduction with pre-experiment covariates), use paired/stratified designs, or raise α (at
the cost of more false positives).

---

## 6. A/B test design in practice ⭐⭐⭐

```
 1. Hypothesis:  "New ranker raises 7-day retention by ≥ 1pp"
 2. Metrics:     primary (one!)  + guardrails (latency, revenue, crash rate)
 3. Randomise:   by USER id, not by session/request  ⚠️  (hash(user_id) % 100)
 4. Power calc:  n per arm and expected duration, fixed BEFORE launch
 5. A/A test:    run the pipeline with no change; if it shows "significance", the
                 assignment or logging is broken
 6. Run to the pre-registered n — do not peek and stop early ⚠️
 7. Analyse:     effect size + CI + p-value; check segments; check guardrails
 8. Decide:      ship / iterate / kill, and write it down
```

### Common failure modes ⚠️ (these are the interview follow-ups)

| Problem | What happens | Fix |
|---|---|---|
| **Peeking / early stopping** | repeatedly testing inflates α far above 5% | fixed horizon, or sequential tests / always-valid p-values |
| **Multiple comparisons** | 20 metrics at α=0.05 ⇒ ~1 false positive by chance | Bonferroni `α/m`, or Benjamini–Hochberg FDR; declare one primary metric |
| **Simpson's paradox** | aggregate reverses within every segment | check segment-level results; randomise properly |
| **Network / interference effects** | treatment leaks between users (social, marketplace, ride-sharing) | cluster randomisation, switchback tests |
| **Novelty / primacy effect** | early lift decays as the novelty wears off | run ≥ 1–2 full weeks, check the trend over time |
| **Sample ratio mismatch** | arms are not 50/50 ⇒ assignment bug | chi-square on the split before trusting any result |
| **Day-of-week seasonality** | weekend behaviour differs | always run in whole weeks |
| **Survivorship / selection bias** | analysing only users who completed | intention-to-treat analysis |

---

## 7. Multiple testing

```
FWER (Bonferroni):  use α/m    — conservative, controls ANY false positive
FDR (Benjamini–Hochberg): sort p-values, find the largest k with p_(k) ≤ (k/m)α
                     — controls the expected PROPORTION of false discoveries
```

Use Bonferroni when a single false positive is costly (a medical claim); use BH when you are
screening many candidates and can tolerate a known false-discovery rate (feature screening, gene
studies, offline metric sweeps). ⭐

---

## 8. Where this shows up in ML work ⭐⭐

- **Model comparison offline:** a single held-out score is a point estimate. Use bootstrap CIs on
  the test set, or a paired test across CV folds (paired, because the folds are shared). Report
  "AUC 0.812 [0.804, 0.820]" rather than "AUC 0.812".
- **Online model rollout:** shadow → 1% → 5% → 50% with guardrail metrics; the statistics above
  decide when the lift is real.
- **Drift detection:** KS test or population-stability index between the training feature
  distribution and live traffic; chi-square for categorical features.
- **Bandits vs A/B:** a multi-armed bandit (Thompson sampling / UCB) shifts traffic towards the
  winner *during* the experiment, reducing regret. Prefer a bandit for short-lived choices
  (headline selection); prefer a fixed A/B test when you need a clean, unbiased effect estimate
  for a long-term decision. ⭐
- **Offline/online mismatch:** an offline metric improves but the online one does not — usually
  distribution shift, a feedback loop, position bias in logged data, or a proxy metric that does
  not track the business metric.

---

## 9. Bayesian A/B testing (the modern alternative)

Model each arm's rate with a Beta posterior, then report

```
P(rate_B > rate_A | data)      and     expected loss of choosing wrongly
```

Advantages: the statement is what stakeholders actually want, peeking is not invalid in the same
way, and the decision can be framed as expected loss. Disadvantages: a prior must be chosen, and
"stop when P > 0.95" still has frequentist error properties you should check by simulation.

---

## Recall questions

1. Define a p-value precisely, then list three wrong definitions.
2. Type I vs Type II error, in both legal and ML-metric terms.
3. When t-test, when z-test, when chi-square, when Mann–Whitney?
4. Compute the per-arm sample size for a 5% baseline and a 0.5pp absolute lift.
5. Why is peeking a problem, and what are two legitimate fixes?
6. Randomise by user or by session — and why does it matter?
7. What is a sample ratio mismatch and what should you do about it?
8. Bonferroni vs Benjamini–Hochberg: what does each control, and when do you use which?
9. How would you report whether model B beats model A on a fixed test set?
10. When would you choose a bandit over a fixed-horizon A/B test?
