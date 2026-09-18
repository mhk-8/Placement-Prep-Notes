
# Expectation, Variance, Limit Theorems and Inequalities ⭐⭐⭐

> **Core idea in 3 lines**
> 1. Expectation is linear *always*; variance is only additive under independence — most exam
>    traps live in that gap.
> 2. The CLT is why we can put error bars on anything, and why the normal distribution is
>    everywhere.
> 3. Concentration inequalities (Markov, Chebyshev, Hoeffding) are the formal reason that "more
>    data ⇒ more reliable estimates", and they underpin bagging and generalisation bounds.

---

## 1. Expectation

```
discrete    E[X] = Σ x p(x)
continuous  E[X] = ∫ x f(x) dx
LOTUS       E[g(X)] = Σ g(x)p(x)   (no need to find the distribution of g(X))
```

**Linearity — the single most useful fact in probability:**

```
E[aX + bY + c] = aE[X] + bE[Y] + c        ⭐⭐⭐  holds even if X and Y are DEPENDENT
```

**Products:** `E[XY] = E[X]E[Y]` only when `X ⫫ Y` (or at least uncorrelated).

**Law of total expectation (tower rule):**

```
E[X] = E[ E[X | Y] ]
```

📐 *Worked use — expected number of coin flips to get the first head.* Let `N` be that number and
condition on the first flip:

```
E[N] = p·1 + (1−p)(1 + E[N])   ⇒  E[N] = 1/p
```

**Indicator trick ⭐⭐⭐.** To count things, write the count as a sum of indicators and use
linearity — dependence does not matter.

*Example:* expected number of fixed points in a random permutation of `n` items.
`X = Σ 1[item i stays put]`, `E[1ᵢ] = 1/n`, so `E[X] = n·(1/n) = 1`, for every `n`.

*Example (ML):* expected number of distinct samples in a bootstrap sample of size `n` drawn with
replacement. `P(sample i never chosen) = (1 − 1/n)ⁿ → e⁻¹ ≈ 0.368`. So about **63.2%** of the
data appears in each bootstrap sample, and the remaining ~36.8% is the **out-of-bag** set used for
free validation in random forests. ⭐⭐⭐ This is a favourite interview question.

---

## 2. Variance and covariance

```
Var(X) = E[(X − μ)²] = E[X²] − (E[X])²
Var(aX + b) = a² Var(X)                         ← b vanishes; a is SQUARED ⚠️
Cov(X,Y) = E[XY] − E[X]E[Y]
Var(X + Y) = Var(X) + Var(Y) + 2Cov(X,Y)
Corr(X,Y) = Cov(X,Y)/(σ_X σ_Y) ∈ [−1, 1]
```

⚠️ **Zero correlation does not imply independence.** `X ~ Uniform(−1,1)`, `Y = X²`:
`Cov = E[X³] − E[X]E[X²] = 0`, yet `Y` is a deterministic function of `X`. Correlation only
detects *linear* dependence. (The converse does hold: independence ⇒ zero correlation.) ⭐⭐

**Law of total variance:**

```
Var(X) = E[Var(X | Y)] + Var(E[X | Y])
          ─────────────   ─────────────
           unexplained      explained
```

📐 This is the population version of the bias–variance decomposition, and also the
`SS_total = SS_within + SS_between` identity behind ANOVA and behind `R²`.

**Variance of a sample mean** — the one that matters most:

```
X̄ = (1/n) Σ Xᵢ ,  i.i.d. ⇒  E[X̄] = μ ,  Var(X̄) = σ²/n ,  SE = σ/√n
```

⭐⭐⭐ **`√n`, not `n`.** To halve your error bar you need **four times** the data. This is the
reason A/B tests need large samples, and the reason averaging `B` independent models cuts variance
by `B` (bagging).

**Sample variance and Bessel's correction.** Using `X̄` instead of `μ` removes one degree of
freedom, so dividing by `n` underestimates:

```
E[ (1/n)Σ(Xᵢ − X̄)² ] = σ²(n−1)/n     ⇒  use  s² = (1/(n−1))Σ(Xᵢ − X̄)²
```

📐 Sketch: `Σ(Xᵢ − X̄)² = Σ(Xᵢ − μ)² − n(X̄ − μ)²`; take expectations to get
`nσ² − n(σ²/n) = (n−1)σ²`.

---

## 3. Limit theorems

**Law of Large Numbers.** `X̄_n → μ` as `n → ∞` (weak: in probability; strong: almost surely).
Justifies Monte-Carlo estimation and using a held-out set to estimate test error.

**Central Limit Theorem ⭐⭐⭐.** For i.i.d. `Xᵢ` with finite mean `μ` and variance `σ²`:

```
(X̄_n − μ) / (σ/√n)  →  N(0, 1)   in distribution
```

What to stress in an interview:

- The **original distribution does not matter** (as long as the variance is finite) — this is why
  the normal appears everywhere.
- It describes the distribution of the **mean**, not of the data. The data stays as skewed as it
  ever was. ⚠️ Very common confusion.
- Convergence is faster for symmetric distributions; `n ≈ 30` is a rule of thumb, but heavy-skew
  or heavy-tail data may need hundreds.
- **It fails** when the variance is infinite (Cauchy, some power-law/heavy-tail data), and when
  samples are strongly dependent.

**Consequence chain you should be able to recite:**

```
CLT  ⇒  X̄ ≈ N(μ, σ²/n)
     ⇒  95% CI:  X̄ ± 1.96 · s/√n
     ⇒  z-test / t-test for a difference of means
     ⇒  A/B-test sample-size formula
```

---

## 4. Concentration inequalities 📐

### Markov

For `X ≥ 0` and `a > 0`:

```
P(X ≥ a) ≤ E[X]/a
```

*Proof.* `E[X] = ∫₀^∞ x f(x)dx ≥ ∫_a^∞ x f(x)dx ≥ a∫_a^∞ f(x)dx = a P(X ≥ a)`. ∎

Weak but assumption-free: needs only non-negativity and a finite mean.

### Chebyshev

```
P(|X − μ| ≥ kσ) ≤ 1/k²
```

*Proof.* Apply Markov to `Y = (X − μ)² ≥ 0` with threshold `k²σ²`:
`P((X−μ)² ≥ k²σ²) ≤ E[(X−μ)²]/(k²σ²) = σ²/(k²σ²) = 1/k²`. ∎

So at least 75% of any distribution's mass lies within 2σ, and 89% within 3σ — distribution-free
(compare the normal's 95% / 99.7%, which assumes normality).

### Hoeffding ⭐

For independent `Xᵢ ∈ [a, b]`:

```
P(|X̄ − μ| ≥ t) ≤ 2 exp( −2nt² / (b−a)² )
```

The bound decays **exponentially in `n`** — far stronger than Chebyshev's `1/n`. Inverting it
gives the sample size needed for a given confidence, and it is the starting point for
generalisation bounds: with `|H|` hypotheses, a union bound gives

```
test error ≤ train error + √( log(2|H|/δ) / (2n) )
```

which is the formal statement of "more data closes the generalisation gap, more model capacity
widens it". ⭐⭐

### Jensen

For convex `φ`: `φ(E[X]) ≤ E[φ(X)]` (reversed for concave).

📐 Uses: `E[X²] ≥ (E[X])²` (hence `Var ≥ 0`); non-negativity of KL divergence
(`D(p‖q) = −E_p[log(q/p)] ≥ −log E_p[q/p] = 0` by concavity of `log`); the ELBO in variational
inference is Jensen applied to `log ∫ p(x,z)dz`. ⭐⭐

---

## 5. Moments, MGFs and tails

```
k-th moment            E[X^k]
k-th central moment    E[(X − μ)^k]
skewness               E[(X−μ)³]/σ³   (asymmetry; > 0 = right tail, e.g. income, latency)
kurtosis               E[(X−μ)⁴]/σ⁴   (3 for normal; > 3 = heavy tails, more outliers)
MGF                    M(t) = E[e^{tX}],  M^{(k)}(0) = E[X^k]
```

Practical relevance: **latency distributions are right-skewed**, so the mean is a poor summary and
you report p95/p99. Heavy-tailed features often need a log transform before a linear model.

---

## 6. Sampling and estimator quality

An estimator `θ̂` of `θ`:

```
Bias(θ̂)  = E[θ̂] − θ
Var(θ̂)   = E[(θ̂ − E[θ̂])²]
MSE(θ̂)   = E[(θ̂ − θ)²] = Bias² + Var        ← 📐 the decomposition
```

📐 *Proof.* Add and subtract `E[θ̂]`:
`E[(θ̂ − θ)²] = E[((θ̂ − E[θ̂]) + (E[θ̂] − θ))²]`. Expanding, the cross-term is
`2(E[θ̂] − θ)·E[θ̂ − E[θ̂]] = 0`, leaving `Var + Bias²`. ∎

This is the same algebra as the bias–variance decomposition of prediction error — worth saying
explicitly, since interviewers like the connection. A **biased estimator can have lower MSE**;
ridge regression is exactly that trade.

**Consistency:** `θ̂_n → θ` in probability. **Efficiency:** minimum variance among unbiased
estimators (Cramér–Rao lower bound `Var(θ̂) ≥ 1/I(θ)`, with `I` the Fisher information).

---

## 7. Bootstrap ⭐⭐

Resample the data `n` times **with replacement**, `B` times; compute the statistic on each
resample; use the spread of those `B` values as the sampling distribution.

```
 original sample (n)
        │
        ├─► resample 1 (n, with repl.) ─► θ̂₁ ┐
        ├─► resample 2 ────────────────► θ̂₂ ├─► empirical distribution of θ̂
        │                ...                 │    → SE, percentile CI
        └─► resample B ────────────────► θ̂_B ┘
```

Why it matters: it gives confidence intervals for statistics with no closed form (median, AUC,
F1), and it is exactly the resampling scheme inside bagging/random forests — with the ~36.8%
out-of-bag rows derived in §1 giving free validation.

---

## Recall questions

1. When does `Var(X+Y) = Var(X) + Var(Y)`, and when does `E[X+Y] = E[X]+E[Y]`?
2. Derive the fraction of unique rows in a bootstrap sample, and say what the rest is used for.
3. Prove Chebyshev from Markov.
4. What exactly does the CLT say converges — and what does it *not* say?
5. Give a variable pair with zero correlation and total dependence.
6. Why divide by `n−1` in the sample variance? Sketch the expectation argument.
7. State the law of total variance and connect it to bias–variance.
8. Prove `MSE = Bias² + Variance`.
9. Why is Hoeffding's bound qualitatively better than Chebyshev's?
10. How much extra data do you need to halve a confidence interval?
