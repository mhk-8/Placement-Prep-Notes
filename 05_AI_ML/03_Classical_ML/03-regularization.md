
# Regularisation ⭐⭐⭐

> **Core idea in 3 lines**
> 1. Regularisation buys a reduction in variance at the price of a little bias — and the trade is
>    usually worth it.
> 2. L2 shrinks, L1 selects; both are MAP estimates under a prior, and both have a geometric
>    story you should be able to draw.
> 3. In deep learning the same job is done by dropout, batch norm, early stopping, augmentation
>    and simply more data.

---

## 1. The general form

```
J(w) = L(w)  +  λ Ω(w)
       data     penalty on complexity
       fit
```

`λ = 0` ⇒ pure ERM (overfits). `λ → ∞` ⇒ `w → 0` (underfits). Pick `λ` by cross-validation, on a
logarithmic grid (`1e-4, 1e-3, …, 1e2`). ⭐

---

## 2. Ridge (L2)

```
J = ‖Xw − y‖² + λ‖w‖²        ⇒   w* = (XᵀX + λI)⁻¹ Xᵀy
```

### 📐 What ridge does to the spectrum

Let `X = UΣVᵀ`. Then

```
w*_ridge = V diag( σⱼ/(σⱼ² + λ) ) Uᵀ y        vs    w*_OLS = V diag( 1/σⱼ ) Uᵀ y
```

and the fitted values shrink direction-by-direction:

```
ŷ = Σⱼ uⱼ ( σⱼ²/(σⱼ² + λ) ) uⱼᵀ y
```

Read it: directions with **large** `σⱼ` (high-variance data directions) are barely touched;
directions with **small** `σⱼ` — exactly the unstable, collinear ones that blow OLS up — are
shrunk hard. That is the precise answer to "why does ridge fix multicollinearity?" ⭐⭐

**Effective degrees of freedom:** `df(λ) = Σⱼ σⱼ²/(σⱼ² + λ)`, falling from `d` at `λ = 0` to `0`.

**Other facts**
- `XᵀX + λI` is always PD for `λ > 0`, so the solution exists and is unique even when `d > n`.
- Ridge is the MAP estimate under a `N(0, τ²)` prior with `λ = σ²/τ²` (proof in the
  probability folder). 📐
- Coefficients shrink towards zero but (almost) never reach it.

---

## 3. Lasso (L1)

```
J = ‖Xw − y‖² + λ‖w‖₁
```

No closed form (the penalty is non-differentiable at 0). Solved by coordinate descent, whose update
is **soft thresholding**:

```
wⱼ ← S(ρⱼ, λ/2) ,      S(ρ, γ) = sign(ρ)·max(|ρ| − γ, 0)
```

📐 In the orthonormal-design case (`XᵀX = I`) this is exact:

```
ridge:   wⱼ = w_ols,j / (1 + λ)          ← proportional shrink, never exactly 0
lasso:   wⱼ = sign(w_ols,j)(|w_ols,j| − λ/2)₊   ← EXACTLY 0 once |w_ols,j| ≤ λ/2
```

That single comparison proves why lasso gives sparsity and ridge does not.

### Why L1 zeroes coefficients — the geometric argument ⭐⭐⭐

```
        L1 (lasso)                        L2 (ridge)
       w₂                                w₂
        │     ___ loss contours           │     ___ loss contours
        │   /   \                         │   /   \
        │  (  ● ) ← OLS optimum           │  (  ● )
     ◆──┼──◆                              │  ╭───╮
    ╱   │   ╲                             │ (     )
   ◆────┼────◆ ← diamond                  │  ╰───╯ ← circle
        │                                 │
   contact happens at a CORNER        contact at a generic point
   ⇒ w₁ = 0 exactly                   ⇒ both small, neither zero
```

The constrained view: minimise `‖Xw − y‖²` subject to `‖w‖₁ ≤ t` (or `‖w‖² ≤ t`). The loss
contours are ellipses growing until they touch the constraint set; a diamond has corners on the
axes, so contact generically occurs where some coordinates are zero. A sphere is smooth, so it
does not.

### Practical notes ⚠️
- With correlated features lasso arbitrarily picks **one** of the group and zeroes the rest —
  unstable across resamples. Elastic net fixes this.
- Lasso selects at most `n` features when `d > n`.
- Lasso is not the same as running a significance test; it is a biased estimator, so do not read
  the surviving coefficients as unbiased effect sizes.

---

## 4. Elastic net

```
J = ‖Xw − y‖² + λ( α‖w‖₁ + (1 − α)‖w‖² )
```

Gets sparsity from L1 and the grouping/stability of L2: correlated features enter or leave
together. The default choice when `d ≫ n` with correlated features (genomics, text). ⭐

---

## 5. Comparison ⭐⭐⭐

| | L1 (lasso) | L2 (ridge) | Elastic net |
|---|---|---|---|
| Penalty | `Σ\|wⱼ\|` | `Σwⱼ²` | both |
| Sparsity | yes (exact zeros) | no | yes |
| Closed form | no | yes | no |
| Prior | Laplace | Gaussian | mixture |
| Correlated features | picks one arbitrarily | shrinks the group together | keeps the group |
| Differentiable at 0 | no (subgradient) | yes | no |
| Use when | you want feature selection / interpretability | all features plausibly matter; multicollinearity | `d ≫ n`, correlated groups |

⚠️ **Always standardise first.** The penalty is applied to raw coefficient magnitudes, so a
feature in kilometres and the same feature in metres get penalised 1000× differently.
⚠️ **Never regularise the intercept** — you would force the predictions towards zero rather than
towards the mean.

---

## 6. Regularisation in deep learning ⭐⭐

| Technique | Mechanism | Notes |
|---|---|---|
| **L2 / weight decay** | shrinks weights each step | use **AdamW** so decay is decoupled from the adaptive scaling ⚠️ |
| **Dropout** | randomly zero units with prob `p` during training | approximates averaging an exponential ensemble; scale by `1/(1−p)` at train time (inverted dropout) so inference is unchanged ⚠️ |
| **Early stopping** | stop when validation stops improving | for a quadratic loss it is provably similar to L2 |
| **Data augmentation** | enlarges the effective dataset with invariances | usually the single highest-value regulariser in vision/audio |
| **Batch/Layer norm** | stabilises activation statistics | mild regularisation via batch noise (BN only); primary purpose is optimisation |
| **Label smoothing** | target `1 − ε` instead of `1` | prevents over-confident logits, improves calibration |
| **Mixup / CutMix** | trains on convex combinations of examples and labels | strong vision regulariser |
| **Gradient clipping** | caps the update norm | stability, not really capacity control |
| **Ensembling** | averages independent models | variance reduction, like bagging |
| **More data** | — | strictly dominates every entry above ⭐ |

⚠️ **Dropout and batch norm interact badly** when stacked naively (the variance shift between
train and eval time); modern architectures typically use BN/LN without heavy dropout, or place
dropout only after the final feature layer.

⚠️ **Dropout at inference is off.** With inverted dropout, activations are scaled by `1/(1−p)`
during training and nothing is changed at test time.

---

## 7. Choosing `λ` in practice

```
for λ in np.logspace(-4, 2, 13):
    score = cross_val_score(Pipeline([("sc", StandardScaler()),
                                      ("m", Ridge(alpha=λ))]), X, y, cv=5)
pick argmax mean(score);  the "1-SE rule" picks the LARGEST λ whose mean score is
within one standard error of the best — a simpler model with statistically
indistinguishable performance. ⭐
```

The pipeline matters: the scaler must be refit inside each fold. ⚠️

---

## 8. Cheat answers to common follow-ups

**"Why does regularisation reduce overfitting?"** It restricts the effective hypothesis space, so
the estimation (variance) term of the error shrinks; equivalently it encodes a prior that large
weights are implausible, so noise in the training set cannot be fitted by huge coefficients.

**"Why does L2 not produce sparsity?"** Its gradient `2λw` shrinks proportionally — it gets weaker
as `w → 0` and never pushes a coefficient across zero. L1's subgradient is a constant `λ·sign(w)`,
so it keeps pushing with full strength until the coefficient hits exactly zero.

**"Is early stopping really regularisation?"** Yes — the number of steps limits how far the weights
can travel from the (small) initialisation, which bounds their norm; for a quadratic objective the
correspondence with an L2 penalty can be made explicit.

**"Can regularisation increase training error?"** Yes, always — that is the point. It should
decrease *validation* error.

---

## Recall questions

1. Write the ridge solution and explain, via the SVD, why it fixes multicollinearity.
2. Give the soft-thresholding update and use it to show lasso yields exact zeros.
3. Draw the L1/L2 constraint-set picture and explain sparsity from it.
4. Which priors correspond to L1 and L2?
5. Why must you standardise before regularising, and why not penalise the intercept?
6. What does elastic net fix that lasso does not?
7. Why does AdamW exist?
8. Explain inverted dropout and what happens at inference.
9. What is the 1-SE rule?
10. Name five regularisers used in deep learning and say what each one actually constrains.
