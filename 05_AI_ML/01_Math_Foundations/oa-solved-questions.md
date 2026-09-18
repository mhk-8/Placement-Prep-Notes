
# Math Foundations — Solved OA Questions

Format: attempt on paper first, then open the answer. Timings assume an OA where ~90 s per
question is typical.

---

## Set A — Linear algebra

**Q1.** `A` is `4×6` with rank 3. What is the dimension of the null space of `A`?

<details><summary>Answer</summary>

Rank–nullity: `rank + nullity = number of columns = 6`. So nullity `= 6 − 3 = 3`.
⚠️ The trap is using the number of rows. Nullity of `A` lives in `R^{n_cols}`.
</details>

**Q2.** `A` is symmetric with eigenvalues `{4, 1, −2}`. Which is true?
(a) `A` is positive definite (b) `A` is invertible (c) `A` is PSD (d) `A` is singular

<details><summary>Answer</summary>

**(b).** `det(A) = 4·1·(−2) = −8 ≠ 0`, so invertible. Not PD/PSD because of the negative
eigenvalue. Not singular.
</details>

**Q3.** For `A ∈ R^{m×n}`, what is `rank(AᵀA)` in terms of `rank(A)`?

<details><summary>Answer</summary>

`rank(AᵀA) = rank(A)`. Proof: `Ax = 0 ⇒ AᵀAx = 0`; conversely `AᵀAx = 0 ⇒ xᵀAᵀAx = ‖Ax‖² = 0 ⇒ Ax = 0`.
Same null space, so same nullity, so same rank.
This is why the normal equation `AᵀAw = Aᵀy` has a unique solution iff `A` has full column rank.
</details>

**Q4.** The singular values of `A` are `{5, 3, 0}`. What are `‖A‖₂`, `‖A‖_F`, and `rank(A)`?

<details><summary>Answer</summary>

`‖A‖₂ = σ_max = 5`. `‖A‖_F = √(25+9+0) = √34 ≈ 5.83`. `rank = ` number of non-zero singular
values `= 2`.
</details>

**Q5.** `A` is `3×3` with `det(A) = 5`. What is `det(2A)` and `det(A⁻¹)`?

<details><summary>Answer</summary>

`det(cA) = cⁿ det(A) = 2³·5 = 40`. `det(A⁻¹) = 1/5`.
⚠️ The exponent is the matrix dimension, not 2.
</details>

**Q6.** Best rank-1 approximation of `A` in Frobenius norm, where `A = UΣVᵀ` — what is it, and
what is the approximation error?

<details><summary>Answer</summary>

Eckart–Young: `A₁ = σ₁u₁v₁ᵀ`, and `‖A − A₁‖_F = √(σ₂² + σ₃² + …)`.
In spectral norm the error is `σ₂`.
</details>

**Q7.** Two unit vectors have cosine similarity 0. Their Euclidean distance is:

<details><summary>Answer</summary>

`‖u − v‖² = ‖u‖² + ‖v‖² − 2uᵀv = 1 + 1 − 0 = 2`, so distance `= √2`.
General relation for unit vectors: `‖u−v‖² = 2(1 − cos θ)`. Useful because it means
**on normalised vectors, ranking by Euclidean distance and by cosine similarity give the same
order** — the fact behind vector-database indexes. ⭐
</details>

**Q8.** Why is a covariance matrix always PSD?

<details><summary>Answer</summary>

For any `v`: `vᵀΣv = vᵀE[(x−μ)(x−μ)ᵀ]v = E[(vᵀ(x−μ))²] ≥ 0` — it is the variance of a scalar
random variable, hence non-negative. It is PD iff no direction has zero variance, i.e. no exact
linear dependence among features.
</details>

---

## Set B — Matrix calculus

**Q9.** `f(x) = xᵀAx` with `A` **not** symmetric. `∇f = ?`

<details><summary>Answer</summary>

`(A + Aᵀ)x`. Only when `A` is symmetric does this simplify to `2Ax`. OAs love this distinction.
</details>

**Q10.** `f(w) = ‖Xw − y‖²`. Give `∇_w f` and the minimiser.

<details><summary>Answer</summary>

`∇ = 2Xᵀ(Xw − y)`. Setting to zero: `XᵀXw = Xᵀy ⇒ w* = (XᵀX)⁻¹Xᵀy` (assuming full column rank).
With ridge penalty `λ‖w‖²`: `w* = (XᵀX + λI)⁻¹Xᵀy`, always invertible for `λ > 0`.
</details>

**Q11.** For binary cross-entropy with sigmoid, `∂L/∂z` (z = logit) equals:
(a) `σ(z)(1−σ(z))` (b) `p − y` (c) `y − p` (d) `−y/p`

<details><summary>Answer</summary>

**(b) `p − y`.** The sigmoid derivative cancels against the `1/p(1−p)` from the log — which is
exactly why cross-entropy, not MSE, is used with sigmoid outputs: MSE leaves a `σ'(z)` factor
that vanishes when the unit is saturated, killing the gradient. ⭐⭐⭐
</details>

**Q12.** Softmax over `K` classes with cross-entropy. `∂L/∂z_j = ?`

<details><summary>Answer</summary>

`p_j − y_j` (with one-hot `y`). Same clean form as the binary case.
</details>

**Q13.** A function `f: R^n → R^m`. The Jacobian has shape:

<details><summary>Answer</summary>

`m × n` — rows index outputs, columns index inputs.
</details>

**Q14.** In a layer `y = Wx + b` with upstream gradient `δ = ∂L/∂y`, give `∂L/∂W`, `∂L/∂x`, `∂L/∂b`.

<details><summary>Answer</summary>

`∂L/∂W = δxᵀ`, `∂L/∂x = Wᵀδ`, `∂L/∂b = δ`.
Memory aid: the shapes must match `W`, `x`, `b` respectively — there is only one way to arrange
the product for each. ⭐⭐⭐
</details>

**Q15.** Adding `λ‖w‖²` to the loss changes the SGD update how?

<details><summary>Answer</summary>

`w ← w − η(∇L + 2λw) = (1 − 2ηλ)w − η∇L` — the weights are multiplicatively shrunk every step,
which is why L2 is called *weight decay*.
⚠️ With Adam this equivalence breaks: the penalty gradient gets divided by `√v̂`, so the decay is
no longer uniform. **AdamW** decouples it and applies `w ← w − ηλw` directly.
</details>

---

## Set C — Optimisation

**Q16.** Which of these losses is convex in the parameters?
(a) logistic regression (b) linear regression with MSE (c) a 2-layer MLP (d) SVM hinge loss

<details><summary>Answer</summary>

(a), (b), (d) are convex. (c) is not — any network with a hidden layer has a non-convex loss
surface (permutation symmetry alone creates many equivalent minima).
</details>

**Q17.** Prove that any local minimum of a convex function is global.

<details><summary>Answer</summary>

Suppose `x*` is a local min and some `y` has `f(y) < f(x*)`. For `t ∈ (0,1)` convexity gives
`f(x* + t(y − x*)) ≤ (1−t)f(x*) + t f(y) < f(x*)`. For small enough `t` this point is inside any
neighbourhood of `x*`, contradicting local minimality. ∎ 📐
</details>

**Q18.** Momentum with `β = 0.9`. On a constant gradient `g`, what is the steady-state velocity?

<details><summary>Answer</summary>

`v = g + βg + β²g + … = g/(1−β) = 10g` — a 10× effective step. That is why you usually reduce the
learning rate when you increase momentum.
</details>

**Q19.** Why does Adam apply bias correction?

<details><summary>Answer</summary>

`m₀ = 0`, so `E[m_t] = (1 − β₁ᵗ)E[g]` — the estimate is biased towards zero early on, most
severely for `v` where `β₂ = 0.999` (after 10 steps, `v` retains only ~1% of its target scale).
Dividing by `(1 − β₁ᵗ)` and `(1 − β₂ᵗ)` makes the estimates unbiased, preventing tiny (or wildly
unstable) first steps.
</details>

**Q20.** In an SVM, complementary slackness `αᵢ(yᵢ(wᵀxᵢ + b) − 1) = 0` implies:

<details><summary>Answer</summary>

Either `αᵢ = 0` (the point does not affect `w`) or the constraint is active, i.e. the point lies
exactly on the margin. **Only margin points have `αᵢ > 0`** — these are the support vectors, and
`w = Σ αᵢyᵢxᵢ` depends on them alone. ⭐⭐
</details>

**Q21.** Training loss oscillates wildly and then becomes NaN. Most likely cause?

<details><summary>Answer</summary>

Learning rate too high (divergence). Fixes in order: lower the LR by 10×, add gradient clipping,
add warmup, check for an un-normalised input feature or a `log(0)`/division by zero in the loss.
</details>

**Q22.** Why do transformers need learning-rate warmup?

<details><summary>Answer</summary>

Adam's second-moment estimate `v̂` is unreliable in the first steps (few samples, strong bias), so
the effective step size has huge variance; combined with the large gradients from
randomly-initialised attention, an immediate full LR destabilises LayerNorm statistics. Warmup
ramps the LR up over a few thousand steps while the moment estimates settle. (Post-LN
architectures need it much more than Pre-LN.) ⭐⭐
</details>

**Q23.** Gradient descent on a quadratic with condition number `κ = 1000`. Roughly how does
convergence scale, and what helps?

<details><summary>Answer</summary>

The error contracts by a factor `(κ−1)/(κ+1) ≈ 0.998` per step — painfully slow, with zig-zag in
the narrow direction. Helpers: feature standardisation (reduces `κ`), momentum (improves the rate
to depend on `√κ`), adaptive methods, batch norm, or a second-order/preconditioned method.
</details>

**Q24.** `f(x, y) = x² − y²` at the origin: minimum, maximum or saddle? What does the Hessian say?

<details><summary>Answer</summary>

Saddle. `H = diag(2, −2)` — indefinite (one positive, one negative eigenvalue). In high
dimensions, a random critical point is a saddle with overwhelming probability, which is why
saddles, not local minima, are the practical obstacle in deep learning.
</details>

---

## Set D — PCA

**Q25.** Eigenvalues of the covariance matrix are `{10, 6, 3, 1}`. How many components are needed
for ≥ 90% explained variance?

<details><summary>Answer</summary>

Total `= 20`. `10/20 = 50%`; `16/20 = 80%`; `19/20 = 95% ≥ 90%`. **3 components.**
</details>

**Q26.** You forget to centre before PCA. What happens?

<details><summary>Answer</summary>

You eigendecompose the second-moment matrix `XᵀX/n = Σ + x̄x̄ᵀ`. The rank-1 term `x̄x̄ᵀ` drags the
top component towards the mean direction, so PC1 largely encodes "where the data is" rather than
"how it varies".
</details>

**Q27.** Data has features in metres and in milligrams. Which PCA variant?

<details><summary>Answer</summary>

Correlation-matrix PCA — standardise each feature to zero mean and unit variance first, else the
large-variance unit dominates.
</details>

**Q28.** True or false: PCA always improves classifier accuracy.

<details><summary>Answer</summary>

**False.** PCA is unsupervised; the discarded low-variance directions may carry the class signal.
It helps with speed, memory, multicollinearity and noise, and can help when `n` is small relative
to `d`, but it can also destroy the signal. LDA is the supervised alternative.
</details>

**Q29.** `X` is `100 × 10000`. You need the top 5 components. Best approach?

<details><summary>Answer</summary>

Do not form the `10000 × 10000` covariance matrix. Use randomised/truncated SVD (`O(ndk)`), or
the Gram trick: eigendecompose the `100 × 100` matrix `X_cX_cᵀ`, then map `v = X_cᵀu / ‖X_cᵀu‖`.
</details>

**Q30.** In an ML pipeline with a train/test split, when do you fit PCA?

<details><summary>Answer</summary>

Fit (mean, scale and components) on the **training fold only**, then transform validation and
test with those fitted parameters. Inside cross-validation it must sit in the `Pipeline` so it is
refit per fold. Fitting on all data first is leakage and inflates your reported score. ⭐⭐⭐
</details>

---

## Scoring

| Correct | Read as |
|---|---|
| 27–30 | Math foundations solid — move on |
| 21–26 | Redo the derivations you missed on paper, then retest |
| 14–20 | Reread `01`–`04` in this folder before continuing |
| < 14 | Spend a full day here; everything downstream depends on it |
