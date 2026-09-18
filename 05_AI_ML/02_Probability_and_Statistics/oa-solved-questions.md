
# Probability & Statistics — Solved OA Questions

30 questions in the style of MNC online assessments and first-round screens.
Attempt each on paper before opening the answer.

---

## Set A — Basic probability

**Q1.** Two fair dice. `P(sum = 8 | at least one die shows 3)`?

<details><summary>Answer</summary>

At least one 3: `11` outcomes (6 + 6 − 1). Of these, sum 8: `(3,5)` and `(5,3)` — both contain a 3.
So `2/11`.
⚠️ The trap is answering `5/36` (unconditional) or forgetting to subtract the double-counted `(3,3)`.
</details>

**Q2.** Three cards drawn without replacement from a standard deck. `P(all three are hearts)`?

<details><summary>Answer</summary>

`(13/52)(12/51)(11/50) = 1716/132600 ≈ 0.0129` (about 1.3%). Equivalently `C(13,3)/C(52,3) = 286/22100`.
</details>

**Q3.** A bag has 5 red and 3 blue balls. Two drawn without replacement. `P(second is red)`?

<details><summary>Answer</summary>

`5/8`. By symmetry (or total probability:
`(5/8)(4/7) + (3/8)(5/7) = 20/56 + 15/56 = 35/56 = 5/8`).
⭐ The elegant argument: by exchangeability, any specific position is equally likely to hold any
ball, so the marginal is the same as for the first draw.
</details>

**Q4.** `P(A) = 0.6`, `P(B) = 0.5`, `P(A ∪ B) = 0.8`. Are `A` and `B` independent?

<details><summary>Answer</summary>

`P(A∩B) = 0.6 + 0.5 − 0.8 = 0.3`. `P(A)P(B) = 0.30`. Equal ⇒ **independent**.
</details>

**Q5.** Monty Hall: three doors, you pick one, the host (who knows) opens a different door with a
goat and offers a switch. Should you switch?

<details><summary>Answer</summary>

**Yes** — switching wins with probability `2/3`.
Your initial pick is right with `1/3`; the host's action gives no new information about your door
but concentrates the remaining `2/3` on the single unopened door.
⚠️ The answer depends on the host *always* opening a goat door and *always* offering a switch. If
the host opens a door at random and it happens to be a goat, it's 50/50.
</details>

**Q6.** How many people are needed so that `P(two share a birthday) > 0.5`?

<details><summary>Answer</summary>

**23.** `P(no match) = Π_{i=0}^{22}(1 − i/365) ≈ 0.4927`.
Generalisation: collisions become likely around `√N` draws — the birthday bound behind hash
collisions.
</details>

**Q7.** `P(spam) = 0.3`. A word appears in 80% of spam and 10% of ham. Given the word,
`P(spam)`?

<details><summary>Answer</summary>

`(0.8)(0.3) / [(0.8)(0.3) + (0.1)(0.7)] = 0.24/0.31 ≈ 0.774`.
</details>

**Q8.** A coin is fair with probability 0.5, or two-headed with probability 0.5. You flip 3 heads
in a row. `P(two-headed)`?

<details><summary>Answer</summary>

`(1)(0.5) / [(1)(0.5) + (1/8)(0.5)] = 0.5/0.5625 = 8/9 ≈ 0.889`.
</details>

---

## Set B — Expectation and variance

**Q9.** Expected number of rolls of a fair die to see a 6.

<details><summary>Answer</summary>

Geometric with `p = 1/6` ⇒ `E = 6`. Via conditioning: `E = 1 + (5/6)E`.
</details>

**Q10.** Expected number of rolls to see **all six** faces.

<details><summary>Answer</summary>

Coupon collector: `6(1 + 1/2 + … + 1/6) = 6 × 2.45 = 14.7`.
</details>

**Q11.** `X ~ Uniform(0,1)`. `E[X²]` and `Var(X)`?

<details><summary>Answer</summary>

`E[X²] = ∫₀¹x²dx = 1/3`. `E[X] = 1/2`, so `Var = 1/3 − 1/4 = 1/12`.
</details>

**Q12.** `Var(2X − 3Y)` with `Var(X)=4`, `Var(Y)=9`, `Cov(X,Y)=2`.

<details><summary>Answer</summary>

`4(4) + 9(9) + 2(2)(−3)(2) = 16 + 81 − 24 = 73`.
⚠️ Coefficients are squared for the variance terms and multiplied (with sign) for the covariance term.
</details>

**Q13.** In a bootstrap sample of size `n` drawn with replacement from `n` rows, what fraction of
the original rows appear (as `n → ∞`)?

<details><summary>Answer</summary>

`P(row i excluded) = (1 − 1/n)ⁿ → e⁻¹ ≈ 0.368`, so **≈ 63.2%** appear and ~36.8% are out-of-bag.
Random forests use those OOB rows as a free validation set. ⭐⭐⭐
</details>

**Q14.** You average `B` independent models each with variance `σ²`. Variance of the average? What
if they are correlated with correlation `ρ`?

<details><summary>Answer</summary>

Independent: `σ²/B`. Correlated: `ρσ² + (1−ρ)σ²/B`.
⭐ As `B → ∞` the variance floors at `ρσ²` — which is exactly why random forests *decorrelate*
trees by sampling features at each split, not just rows.
</details>

**Q15.** Expected number of fixed points in a random permutation of `n` elements.

<details><summary>Answer</summary>

`1`, for every `n`. Sum of indicators, each with probability `1/n`, and linearity ignores the
heavy dependence between them.
</details>

**Q16.** `X ~ N(100, 15²)`. `P(X > 130)` approximately?

<details><summary>Answer</summary>

`z = 2` ⇒ tail ≈ `(1 − 0.9545)/2 ≈ 2.3%`.
</details>

---

## Set C — Inequalities and limit theorems

**Q17.** Average income is ₹50 000. Bound the fraction earning over ₹200 000, with no other
assumptions.

<details><summary>Answer</summary>

Markov: `P(X ≥ 200000) ≤ 50000/200000 = 0.25`. Weak but assumption-free (income ≥ 0 is all we need).
</details>

**Q18.** Bound the probability that a variable is more than 3 standard deviations from its mean,
for **any** distribution.

<details><summary>Answer</summary>

Chebyshev: `≤ 1/9 ≈ 11.1%`. (For a normal it is 0.3% — but that needs normality.)
</details>

**Q19.** True or false: the CLT says the data becomes normally distributed as `n` grows.

<details><summary>Answer</summary>

**False.** It says the distribution of the **sample mean** approaches normality. The data's own
distribution is unchanged. ⚠️ A very common trap.
</details>

**Q20.** Samples from a distribution with `σ = 20`. How large must `n` be for the standard error
to be ≤ 1?

<details><summary>Answer</summary>

`20/√n ≤ 1 ⇒ n ≥ 400`.
</details>

**Q21.** When does the CLT fail?

<details><summary>Answer</summary>

Infinite variance (Cauchy, some power-law data), strong dependence between samples, or `n` too
small for a heavily skewed distribution.
</details>

---

## Set D — Estimation

**Q22.** MLE of `θ` for `Uniform(0, θ)` given samples `{2, 5, 3, 9}`.

<details><summary>Answer</summary>

Likelihood `= θ^{−n}` for `θ ≥ max xᵢ`, zero otherwise — decreasing in `θ`, so the MLE is the
smallest feasible value: `θ̂ = max xᵢ = 9`.
⚠️ Setting the derivative to zero fails here; the maximum is at a boundary. It is biased
(`E[θ̂] = nθ/(n+1)`); the unbiased version is `((n+1)/n)·max`.
</details>

**Q23.** Why is the MLE of a Gaussian variance biased?

<details><summary>Answer</summary>

It uses `x̄` rather than the unknown `μ`, which consumes one degree of freedom:
`E[σ̂²_MLE] = σ²(n−1)/n`. Divide by `n−1` to correct.
</details>

**Q24.** Which regulariser corresponds to a Laplace prior, and why does it zero out weights?

<details><summary>Answer</summary>

L1 / lasso. The Laplace density has a non-differentiable peak at 0 (equivalently the L1 ball has
corners on the axes), so the MAP solution sits exactly at zero for weak features.
</details>

**Q25.** In ridge regression as MAP, what does `λ` equal?

<details><summary>Answer</summary>

`λ = σ²/τ²` — noise variance over prior variance. Noisier data or a tighter prior ⇒ more
shrinkage. ⭐
</details>

**Q26.** Beta(2,2) prior, 7 successes in 10 trials. Posterior and its mean?

<details><summary>Answer</summary>

`Beta(2+7, 2+3) = Beta(9, 5)`; mean `9/14 ≈ 0.643`. The MLE alone would be `0.7`; the prior pulls
it toward `0.5`.
</details>

---

## Set E — Testing and A/B

**Q27.** `p = 0.03` at `α = 0.05`. What can you conclude?

<details><summary>Answer</summary>

Reject `H₀` at the 5% level. It does **not** mean there is a 97% chance the effect is real, and it
says nothing about the size of the effect — report the confidence interval too.
</details>

**Q28.** You test 20 metrics at `α = 0.05` and one is significant. Should you believe it?

<details><summary>Answer</summary>

Not on its own: `P(at least one false positive) = 1 − 0.95²⁰ ≈ 64%`. Apply Bonferroni
(`α = 0.0025`) or BH-FDR, or pre-register a single primary metric.
</details>

**Q29.** Baseline conversion 10%, want to detect a 1pp absolute lift with 80% power at α = 0.05.
Roughly how many users per arm?

<details><summary>Answer</summary>

`n ≈ 16·p(1−p)/δ² = 16(0.1)(0.9)/0.0001 = 14 400` per arm (≈ 28 800 total).
</details>

**Q30.** Your A/B test shows a 3% lift after 2 days. Ship it?

<details><summary>Answer</summary>

No — flag the issues: (i) peeking before the pre-registered sample size inflates the false-positive
rate; (ii) two days does not cover weekly seasonality; (iii) early lift may be a novelty effect;
(iv) check for sample ratio mismatch and guardrail metrics. Run to the planned horizon, then judge
on the confidence interval, not the point estimate. ⭐⭐⭐
</details>

---

## Scoring

| Correct | Read as |
|---|---|
| 27–30 | Strong — probability will not be your bottleneck |
| 21–26 | Reread the two or three sections you missed |
| 14–20 | Redo `01`–`04` of this folder with pen and paper |
| < 14 | Two focused days here; MLE/MAP and bias–variance depend on it |
