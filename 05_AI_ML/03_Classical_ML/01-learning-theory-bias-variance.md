
# Learning Theory, Bias–Variance and Model Selection ⭐⭐⭐

> **Core idea in 3 lines**
> 1. Generalisation error splits into bias², variance and irreducible noise — and you can prove it
>    in five lines.
> 2. Every knob you turn (depth, regularisation, data size, ensembling) moves you along that
>    trade-off; naming *which* term it changes is what separates a good answer from a vague one.
> 3. The deep-learning era complicates the classical U-curve (double descent), and knowing that
>    earns extra credit.

---

## 1. The supervised-learning setup

```
unknown joint distribution  P(x, y)
training set  D = {(xᵢ, yᵢ)}ⁿ  ~  P,  i.i.d.
hypothesis class  H
learning algorithm  A : D ↦ ĥ ∈ H

risk (what we want)        R(h)  = E_{(x,y)~P}[ L(h(x), y) ]
empirical risk (what we get) R̂(h) = (1/n) Σ L(h(xᵢ), yᵢ)
```

ERM picks `ĥ = argmin_{h∈H} R̂(h)`. The entire theory is about the gap `R(ĥ) − R̂(ĥ)`.

```
R(ĥ) − R*  =  [ R(h*) − R* ]   +   [ R(ĥ) − R(h*) ]
                 approximation          estimation
                 error (bias)           error (variance)
     h* = best in H                 grows with |H|, shrinks with n
     shrinks as H grows
```

⭐ This decomposition **is** the bias–variance trade-off stated in learning-theory language. Say
both versions.

---

## 2. 📐 The bias–variance decomposition — full proof

Assume `y = f(x) + ε`, `E[ε] = 0`, `Var(ε) = σ²`. Let `ĥ_D` be the model trained on a random
dataset `D`, and write `h̄(x) = E_D[ĥ_D(x)]` for the average prediction over datasets.

Fix a test point `x`. The expected squared error is

```
E_{D,ε}[ (y − ĥ_D(x))² ]
```

Insert `f(x)` and `h̄(x)`:

```
y − ĥ_D(x) = (f(x) + ε) − ĥ_D(x)
           = ε + ( f(x) − h̄(x) ) + ( h̄(x) − ĥ_D(x) )
```

Square and take expectations. The three cross-terms vanish:

- `E[ε · (f − h̄)] = E[ε](f − h̄) = 0`  (ε independent of `D`, zero mean)
- `E[ε · (h̄ − ĥ_D)] = E[ε]E[h̄ − ĥ_D] = 0`
- `E_D[(f − h̄)(h̄ − ĥ_D)] = (f − h̄)·E_D[h̄ − ĥ_D] = (f − h̄)·0 = 0`

Hence

```
E[(y − ĥ_D(x))²] =  σ²            +  (f(x) − h̄(x))²  +  E_D[(ĥ_D(x) − h̄(x))²]
                    ────────         ───────────────     ─────────────────────
                    irreducible          Bias²                Variance
                       noise
```

∎ ⭐⭐⭐ **Be able to write these five lines from memory.** It is asked verbatim.

**Reading the terms**

| Term | Meaning | Reduced by |
|---|---|---|
| Noise `σ²` | label noise, missing features, inherent randomness | nothing (except better features/labels) |
| Bias² | the model class is too rigid to represent `f` | bigger model, more features, less regularisation, boosting |
| Variance | the fit swings with the particular training sample | more data, regularisation, bagging, feature selection, simpler model |

⚠️ This exact decomposition is for **squared loss**. For 0–1 loss there is no clean additive
version (Domingos gives an analogue where variance can even *help*). Say so if pressed — it is a
senior-level detail.

---

## 3. The classical picture

```
error
  │\                                              ← total test error
  │ \                                    /
  │  \                                 /
  │   \        ┌── sweet spot ──┐    /
  │    \______/                  \__/
  │     ‾‾‾‾‾‾‾‾‾‾‾ bias² ‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾  (falls with capacity)
  │    ____________________________________ variance (rises with capacity)
  │   /
  │__/______________ irreducible noise ______
  └────────────────────────────────────────────► model capacity
      underfit                         overfit
```

**Diagnosing from learning curves ⭐⭐⭐** — plot train and validation error against training-set
size:

```
HIGH BIAS (underfit)                 HIGH VARIANCE (overfit)
err                                  err
 │                                    │
 │ ───────── val                      │ ──────────────── val
 │ ───────── train  (converged,       │        ╲
 │            both high, small gap)   │         ╲______ train (low)
 └───────────────► n                  └────── large gap ──────► n

 more data will NOT help              more data WILL help
 → bigger model, better features,     → regularise, simplify, augment,
   less regularisation, train longer    ensemble, collect more data
```

| Symptom | Diagnosis | Actions |
|---|---|---|
| train error high, val ≈ train | underfitting | more capacity, richer features, train longer, lower regularisation, check LR |
| train error low, val ≫ train | overfitting | more data, augmentation, L1/L2, dropout, early stopping, simpler model, bagging |
| train error ≈ 0 and val ≈ 0 but production bad | distribution shift / leakage | audit the split, check for leaked features, monitor drift |
| val better than train | leakage, or dropout/BN artefact | check the split; note that train loss is measured *during* updates with regularisation active |

---

## 4. What moves which term ⭐⭐

| Action | Bias | Variance | Note |
|---|---|---|---|
| more training data | — | ↓↓ | the safest lever |
| more features | ↓ | ↑ | may also leak |
| deeper tree / higher degree | ↓ | ↑ | |
| stronger L1/L2 | ↑ | ↓ | ridge trades a little bias for a lot of variance |
| **bagging** / random forest | ≈ | ↓↓ | averages independent errors |
| **boosting** | ↓↓ | ↑ | fits residuals sequentially |
| early stopping | ↑ | ↓ | implicit regularisation |
| dropout | ↑ | ↓ | approximates an ensemble |
| k-NN with larger `k` | ↑ | ↓ | `k=1` is pure variance |

✅ The crisp interview line: **"bagging attacks variance, boosting attacks bias."** Follow it with
*why*: bagging averages `B` models fit on resamples (variance `→ ρσ²`); boosting adds weak learners
each fit to the current residual, which reduces bias but can overfit noise if you run too many
rounds.

---

## 5. Validation methodology ⭐⭐⭐

```
  all data
     │
     ├── train (fit parameters)
     ├── validation (choose hyperparameters, early stopping)
     └── test (touch ONCE, at the very end)
```

| Scheme | Use when |
|---|---|
| hold-out (70/15/15) | plenty of data, quick iteration |
| k-fold CV (k=5 or 10) | small/medium data; use the mean **and std** across folds |
| stratified k-fold | classification, especially imbalanced — preserves class ratios |
| **group** k-fold | repeated entities (same patient/user in several rows) ⚠️ |
| **time-series split** | any temporal data — train on the past, validate on the future ⚠️⭐⭐⭐ |
| nested CV | when you tune hyperparameters *and* need an unbiased performance estimate |
| leave-one-out | tiny `n`; high variance, expensive |

⚠️ **The two leakage traps interviewers probe:**
1. **Random shuffling of time-series data** lets the model see the future. Always split by time.
2. **Preprocessing fitted on all data** (scaler, imputer, PCA, target encoding, SMOTE) leaks test
   statistics into training. Everything goes inside a `Pipeline` so it is refit per fold.

**Nested CV** in one picture:

```
outer fold k:  ┌──── train_outer ────┐ ┌ test_outer ┐
                 └ inner CV: pick hyperparameters ┘
               fit on all of train_outer with the chosen setting
               score once on test_outer        ← unbiased estimate
```

---

## 6. Capacity, VC dimension and the bound

**VC dimension** = the largest set size that `H` can shatter (realise all `2^m` labelings).
Examples: a linear classifier in `R^d` has `VC = d + 1`; a 1-NN classifier has infinite VC.

```
with probability ≥ 1 − δ:     R(h) ≤ R̂(h) + O( √( (VC·log n + log(1/δ)) / n ) )
```

Read it as: **generalisation gap grows with capacity, shrinks as `√(1/n)`**. It is loose (it
predicts deep nets should not generalise at all), but it is the right intuition to quote, and the
`√(1/n)` matches the Hoeffding result from the statistics folder.

**Occam / MDL:** among models fitting the data equally well, prefer the one needing fewer bits to
describe. AIC (`2k − 2ℓ`) and BIC (`k log n − 2ℓ`) are practical versions.

**No Free Lunch:** averaged over *all* possible problems, no learner beats any other. Practical
meaning: there is no universally best algorithm — inductive bias matched to the domain is what
works. Use it to justify trying gradient boosting on tabular data and CNNs/transformers on
perceptual data. ⭐

---

## 7. Double descent — the modern caveat ⭐⭐

```
test
error │  classical U            modern regime
      │      ╱‾╲
      │     ╱   ╲         ╱‾╲
      │    ╱     ╲       ╱   ╲___________
      │___╱       ╲_____╱                 ‾‾‾‾‾‾‾
      └────────────┬──────┬──────────────────────► capacity
               sweet spot  interpolation
                           threshold (train err = 0)
```

Past the interpolation threshold (where the model can fit the training data exactly), test error
can **decrease again**. Explanation in one sentence: among the infinitely many zero-training-error
solutions, SGD plus implicit regularisation finds a low-norm, smoother one, and more capacity gives
a larger pool of such solutions. This is why enormous over-parameterised networks generalise.

Related: **grokking** (long after the training loss hits zero, validation accuracy suddenly jumps)
and the fact that deep nets can memorise random labels (Zhang et al.), which shows classical
capacity measures do not explain deep learning.

---

## 8. A practical protocol you can describe in an interview ⭐⭐⭐

```
1. Establish a baseline (majority class / simple linear model) and the metric that matters.
2. Overfit a small subset deliberately — if you cannot, there is a BUG, not a modelling problem.
3. Plot learning curves → diagnose bias vs variance.
4. Attack the dominant term (table in §4).
5. Regularise and tune with CV; keep preprocessing inside the pipeline.
6. Error-analyse the worst slices; look for a feature or a segment, not a global fix.
7. Touch the test set once.
8. Ship behind an A/B test with guardrails.
```

---

## Recall questions

1. Derive `E[(y − ĥ(x))²] = σ² + Bias² + Variance` and justify why each cross-term is zero.
2. For which loss does that decomposition hold exactly?
3. From a learning curve with both errors high and close, what do you do — and what will *not* help?
4. Does bagging reduce bias or variance? Boosting? Why?
5. What exactly is nested CV for?
6. Two ways to leak information through preprocessing, and the fix.
7. When must you use GroupKFold instead of KFold?
8. State the VC generalisation bound and what it says qualitatively.
9. What is double descent, and what does it mean for the classical U-curve?
10. State the No Free Lunch theorem and its practical implication.
