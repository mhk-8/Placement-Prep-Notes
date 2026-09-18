
# Linear and Logistic Regression ⭐⭐⭐

> **Core idea in 3 lines**
> 1. Linear regression is the Gaussian-likelihood MLE with a closed-form solution; logistic
>    regression is the Bernoulli-likelihood MLE with no closed form but a convex loss.
> 2. Both are linear in the *parameters*; feature engineering is what makes them non-linear in
>    the inputs.
> 3. They are the models interviewers use to check whether you truly understand assumptions,
>    gradients, regularisation and interpretation.

---

## 1. Linear regression

```
ŷ = w₀ + w₁x₁ + … + w_d x_d = wᵀx     (absorb the intercept with x₀ = 1)
Loss (OLS): J(w) = ‖Xw − y‖² = Σ (yᵢ − wᵀxᵢ)²
```

### Closed form 📐

```
∇J = 2Xᵀ(Xw − y) = 0  ⇒  XᵀXw = Xᵀy  ⇒  w* = (XᵀX)⁻¹Xᵀy
```

**Geometry:** `Xw*` is the orthogonal projection of `y` onto the column space of `X`; the residual
`y − Xw*` is orthogonal to every feature (`Xᵀ(y − Xw*) = 0`). Say this — it explains *why* the
normal equation has that form.

**Existence/uniqueness:** unique iff `X` has full column rank (`rank(XᵀX) = rank(X) = d`).
Multicollinearity ⇒ near-singular `XᵀX` ⇒ huge, unstable, sign-flipping coefficients. ⚠️

**Cost:** `O(nd² + d³)` for the normal equation. Use gradient descent (`O(nd)` per step) when
`d` is large, and never `inv()` — use QR or Cholesky (`np.linalg.lstsq`). ⚠️

### The five OLS assumptions ⭐⭐

| Assumption | Violated ⇒ | Detect / fix |
|---|---|---|
| **L**inearity in parameters | biased fit | residual-vs-fitted plot; add polynomial/interaction terms |
| **I**ndependence of errors | wrong standard errors | Durbin–Watson; use time-series models / clustered SE |
| **N**ormality of errors | CIs and tests invalid (predictions still fine) | Q–Q plot; transform `y` (log) |
| **E**qual variance (homoscedasticity) | inefficient, wrong SEs | funnel shape in residual plot; log-transform, weighted LS, robust SEs |
| **No multicollinearity** | unstable coefficients | VIF > 5–10; drop/combine features, use ridge or PCA |

Mnemonic: **LINE** + no multicollinearity.
⚠️ Normality is about the **residuals**, not the features or the target.

### Evaluating it

```
R²      = 1 − SS_res/SS_tot      fraction of variance explained
adj R²  = 1 − (1−R²)(n−1)/(n−d−1)   penalises extra features
RMSE    = √(Σ(y−ŷ)²/n)           in the units of y
MAE                                robust to outliers
MAPE                               scale-free but blows up near y = 0 ⚠️
```

⚠️ `R²` **never decreases** when you add a feature, even a random one — always report adjusted `R²`
or a held-out score. `R²` can be negative on a test set (worse than predicting the mean).

### Interpretation ⭐

`wⱼ` = expected change in `y` per one-unit increase in `xⱼ` **holding all other features fixed**.
That clause is the whole answer; drop it and the interpretation is wrong. Coefficient magnitudes
are only comparable if the features are standardised.

---

## 2. Regularised linear models

```
Ridge  J = ‖Xw − y‖² + λ‖w‖²    ⇒  w* = (XᵀX + λI)⁻¹Xᵀy      (always invertible)
Lasso  J = ‖Xw − y‖² + λ‖w‖₁    ⇒  no closed form; coordinate descent / LARS
Elastic net  J = ‖Xw−y‖² + λ₁‖w‖₁ + λ₂‖w‖²
```

Full treatment in `03-regularization.md`. Headlines: ridge shrinks and stabilises; lasso selects;
elastic net handles correlated groups; **always standardise before regularising** (the penalty is
scale-dependent) and **never penalise the intercept**. ⚠️⭐

---

## 3. Logistic regression ⭐⭐⭐

Despite the name it is a **classifier**. It models

```
p = P(y = 1 | x) = σ(wᵀx) = 1/(1 + e^{−wᵀx})
```

### Why not linear regression for classification?

- Predictions escape `[0,1]` and cannot be read as probabilities.
- Squared loss with a sigmoid is non-convex in `w` and has vanishing gradients when saturated.
- The fitted line is dragged by points far from the boundary.

### The log-odds view ⭐⭐

```
log( p/(1−p) ) = wᵀx
```

**Logistic regression is linear in the log-odds.** So `wⱼ` is the change in log-odds per unit of
`xⱼ`, and `e^{wⱼ}` is the **odds ratio**: `w = 0.7 ⇒ e^{0.7} ≈ 2`, i.e. the odds double. Giving the
odds-ratio interpretation immediately marks you out. ⭐

### Loss and gradient 📐

Bernoulli likelihood ⇒ negative log-likelihood = binary cross-entropy:

```
J(w) = −Σ [ yᵢ log pᵢ + (1 − yᵢ) log(1 − pᵢ) ]
```

Derivation of the gradient (using `σ' = σ(1−σ)`, `z = wᵀx`):

```
∂J/∂pᵢ = −yᵢ/pᵢ + (1−yᵢ)/(1−pᵢ) = (pᵢ − yᵢ)/(pᵢ(1−pᵢ))
∂pᵢ/∂zᵢ = pᵢ(1 − pᵢ)
⇒ ∂J/∂zᵢ = pᵢ − yᵢ
⇒ ∇_w J = Σ (pᵢ − yᵢ) xᵢ = Xᵀ(p − y)
```

∎ The sigmoid derivative cancels — the reason cross-entropy is the right loss. **Same form as
linear regression's gradient**, with `p` in place of `ŷ`.

**Convexity 📐:** the Hessian is `H = XᵀSX` with `S = diag(pᵢ(1−pᵢ)) ⪰ 0`, so
`vᵀHv = ‖S^{1/2}Xv‖² ≥ 0` — positive semi-definite, hence convex, hence any local optimum is
global. ⭐⭐

No closed form (the equations are transcendental), so fit with gradient descent, Newton /
IRLS, or L-BFGS.

### Multiclass: softmax regression

```
p_k = exp(w_kᵀx) / Σ_j exp(w_jᵀx)       ∂L/∂z_k = p_k − y_k
```

One-vs-rest is the alternative (`K` binary models); softmax gives properly normalised
probabilities and is preferred for mutually exclusive classes. Use `K` independent sigmoids for
**multi-label** problems. ⚠️ Softmax is over-parameterised by one degree of freedom (adding a
constant to all logits changes nothing), which is why regularisation makes the solution unique.

### Separable data ⚠️

If the classes are perfectly separable, the likelihood is maximised by pushing `‖w‖ → ∞`, so the
weights diverge. Regularisation (or early stopping) is required — `sklearn` therefore applies L2
by default.

---

## 4. Decision threshold and calibration ⭐⭐

The model outputs a probability; **0.5 is a choice, not a law**. Move it to trade precision
against recall (fraud detection uses a much lower threshold; a costly intervention uses a higher
one). Choose the threshold on the validation set by optimising the business cost, or pick the
point on the precision–recall curve that meets a required precision.

Logistic regression is **naturally well calibrated** (it directly optimises the log-likelihood of
the probabilities) — unlike SVMs, boosted trees or naive Bayes, which need Platt scaling or
isotonic regression. That is a real reason to prefer it when the probability itself is used
downstream (expected-value decisions, ranking with thresholds, pricing). ⭐⭐

---

## 5. Implementation (write this cleanly on a whiteboard)

```python
import numpy as np

def sigmoid(z):
    # numerically stable: avoid exp of a large positive number
    out = np.empty_like(z, dtype=float)
    pos, neg = z >= 0, z < 0
    out[pos] = 1.0 / (1.0 + np.exp(-z[pos]))
    ez = np.exp(z[neg])
    out[neg] = ez / (1.0 + ez)
    return out

def fit_logistic(X, y, lr=0.1, epochs=1000, lam=0.0):
    n, d = X.shape
    w = np.zeros(d)
    for _ in range(epochs):
        p = sigmoid(X @ w)
        grad = X.T @ (p - y) / n + lam * w      # do not penalise the intercept column
        w -= lr * grad
    return w

def predict_proba(X, w):
    return sigmoid(X @ w)
```

⚠️ Points that get marked: stable sigmoid, dividing the gradient by `n`, not penalising the
intercept, and standardising `X` before fitting.

---

## 6. Generalised linear models (one line of extra credit)

Linear, logistic and Poisson regression are all GLMs: a linear predictor `η = wᵀx`, a link
function `g(μ) = η`, and an exponential-family likelihood.

| Target | Distribution | Link | Loss |
|---|---|---|---|
| continuous | Normal | identity | MSE |
| binary | Bernoulli | logit | cross-entropy |
| counts | Poisson | log | Poisson deviance |
| positive skewed | Gamma | log | Gamma deviance |

---

## 7. Comparison table ⭐

| | Linear regression | Logistic regression |
|---|---|---|
| Task | regression | classification |
| Output | real number | probability in (0,1) |
| Likelihood | Gaussian | Bernoulli |
| Loss | MSE | cross-entropy |
| Closed form | yes | no |
| Convex | yes | yes |
| Gradient | `Xᵀ(ŷ − y)` | `Xᵀ(p − y)` |
| Assumption tested | LINE + no multicollinearity | linear in log-odds, independent observations |

---

## Recall questions

1. Derive the normal equation and give its geometric meaning.
2. Name the OLS assumptions and one diagnostic for each.
3. Why can `R²` never decrease when a feature is added? What do you report instead?
4. Why is squared loss a bad idea for classification — give two distinct reasons.
5. Derive `∇J = Xᵀ(p − y)` for logistic regression.
6. Prove that the logistic loss is convex.
7. Interpret a logistic coefficient of `0.7`.
8. What happens when the classes are perfectly separable, and what fixes it?
9. Why is logistic regression better calibrated than a boosted tree?
10. When would you choose one-vs-rest over softmax?
