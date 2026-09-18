
# Estimation: MLE, MAP and the Bayesian View ⭐⭐⭐

> **Core idea in 3 lines**
> 1. MLE picks the parameters that make the observed data most probable; MAP adds a prior.
> 2. Every loss function you use is a negative log-likelihood in disguise — MSE is Gaussian noise,
>    cross-entropy is Bernoulli/categorical.
> 3. Every regulariser you use is a prior in disguise — L2 is a Gaussian prior, L1 is a Laplace
>    prior. Being able to prove these two sentences is worth a lot in an interview.

---

## 1. Maximum likelihood

Data `D = {x₁ … x_n}` assumed i.i.d. from `p(x | θ)`.

```
Likelihood       L(θ) = Π p(xᵢ | θ)
Log-likelihood   ℓ(θ) = Σ log p(xᵢ | θ)
MLE              θ̂_MLE = argmax_θ ℓ(θ)
```

Why the log: turns products into sums (differentiable and numerically stable — a product of
`10⁴` small numbers underflows), and `log` is monotone so the argmax is unchanged. ⚠️

### 📐 Derivation 1 — Bernoulli

`p(x|θ) = θ^x(1−θ)^{1−x}`, `k = Σxᵢ` successes in `n` trials.

```
ℓ(θ) = k log θ + (n − k) log(1 − θ)
ℓ'(θ) = k/θ − (n − k)/(1 − θ) = 0
⇒ k(1 − θ) = (n − k)θ
⇒ k = nθ
⇒ θ̂ = k/n
```

The MLE is the sample proportion. Second derivative `−k/θ² − (n−k)/(1−θ)² < 0` confirms a maximum.

### 📐 Derivation 2 — Gaussian

`p(x|μ,σ²) = (2πσ²)^{−1/2} exp(−(x−μ)²/2σ²)`.

```
ℓ = −(n/2)log(2πσ²) − (1/(2σ²)) Σ(xᵢ − μ)²

∂ℓ/∂μ  = (1/σ²) Σ(xᵢ − μ) = 0        ⇒  μ̂ = x̄
∂ℓ/∂σ² = −n/(2σ²) + (1/(2σ⁴))Σ(xᵢ − μ̂)² = 0
                                      ⇒  σ̂² = (1/n)Σ(xᵢ − x̄)²
```

⚠️ **The MLE of the variance divides by `n`, so it is biased** (it underestimates by a factor
`(n−1)/n`). Bessel's `n−1` is the unbiased correction — MLE gives no guarantee of unbiasedness.

---

## 2. Every loss is a negative log-likelihood ⭐⭐⭐

### 📐 MSE ⟸ Gaussian noise

Model `y = f_w(x) + ε`, `ε ~ N(0, σ²)`. Then `p(y|x,w) = N(f_w(x), σ²)` and

```
ℓ(w) = Σ [ −½log(2πσ²) − (yᵢ − f_w(xᵢ))²/(2σ²) ]
```

Dropping constants in `w`,

```
argmax ℓ  =  argmin Σ (yᵢ − f_w(xᵢ))²
```

**Minimising squared error is exactly MLE under additive Gaussian noise.** ∎

Consequence worth saying: MSE is the "right" loss only if your noise really is Gaussian and
homoscedastic. Heavy-tailed noise ⇒ Laplace likelihood ⇒ **MAE** is the MLE loss, which is why
MAE is robust to outliers. ⭐

### 📐 Cross-entropy ⟸ Bernoulli

`p(y|x,w) = p^y(1−p)^{1−y}` with `p = σ(wᵀx)`:

```
ℓ(w) = Σ [ yᵢ log pᵢ + (1 − yᵢ) log(1 − pᵢ) ]
−ℓ(w) = Σ −[ yᵢ log pᵢ + (1 − yᵢ) log(1 − pᵢ) ]   ← binary cross-entropy
```

∎ Multiclass: categorical likelihood `Π_k p_k^{y_k}` ⇒ `−Σ_k y_k log p_k`.

| Assumed noise / likelihood | Resulting loss |
|---|---|
| Gaussian | MSE |
| Laplace | MAE |
| Bernoulli | binary cross-entropy |
| Categorical | cross-entropy |
| Poisson | Poisson deviance (count models) |
| Student-t | robust / Huber-like losses |

---

## 3. MAP and the prior

```
p(θ | D) ∝ p(D | θ) p(θ)
θ̂_MAP = argmax_θ [ log p(D|θ) + log p(θ) ]
```

MAP = MLE + a penalty term that comes from the prior.

### 📐 Gaussian prior ⇒ L2 / ridge ⭐⭐⭐

Prior `w ~ N(0, τ²I)`, so `log p(w) = −‖w‖²/(2τ²) + const`. With Gaussian likelihood:

```
θ̂_MAP = argmin  Σ(yᵢ − wᵀxᵢ)²/(2σ²) + ‖w‖²/(2τ²)
       = argmin  Σ(yᵢ − wᵀxᵢ)² + λ‖w‖² ,      λ = σ²/τ²
```

∎ Note what `λ` means: **the ratio of noise variance to prior variance**. A tight prior (small
`τ`) or noisy data (large `σ`) ⇒ strong regularisation. That sentence is a strong interview answer.

### 📐 Laplace prior ⇒ L1 / lasso ⭐⭐⭐

Prior `p(wⱼ) = (1/2b)exp(−|wⱼ|/b)`, so `log p(w) = −Σ|wⱼ|/b + const`:

```
θ̂_MAP = argmin Σ(yᵢ − wᵀxᵢ)² + λ‖w‖₁ ,     λ = 2σ²/b
```

∎ The Laplace density has a sharp peak at zero, which is the probabilistic counterpart of the
geometric "corners of the L1 ball" sparsity argument.

**Summary table ⭐⭐⭐**

| Prior on weights | Penalty | Effect |
|---|---|---|
| none (uniform) | none | MLE, can overfit |
| `N(0, τ²)` | `λ‖w‖²` | shrinks all weights smoothly |
| `Laplace(0, b)` | `λ‖w‖₁` | drives many weights exactly to 0 |
| mix | elastic net | grouped sparsity |

**As `n → ∞`, MAP → MLE**: the log-likelihood grows like `n` while `log p(θ)` stays `O(1)`, so the
prior is drowned out. Priors matter when data is scarce. ⭐

---

## 4. The full Bayesian view

MLE and MAP both return a **point estimate**. The Bayesian answer is a whole distribution:

```
             p(D | θ) p(θ)
p(θ | D) = ────────────────── ,      p(D) = ∫ p(D|θ)p(θ)dθ
                 p(D)

posterior predictive:   p(x* | D) = ∫ p(x* | θ) p(θ | D) dθ
```

```
    prior p(θ)              likelihood p(D|θ)
        │                          │
        └──────────┬───────────────┘
                   ▼
            posterior p(θ|D)          ← MAP = its mode
                   │                     Bayes estimator = its mean
                   ▼
        integrate out θ  ⇒  predictive distribution with calibrated uncertainty
```

The integral is usually intractable, hence MCMC, variational inference, Laplace approximation, or
cheap proxies such as **MC dropout** and **deep ensembles** for neural-network uncertainty. ⭐

**Conjugate priors** make it exact. The one to know:

```
Beta(α, β) prior  +  Binomial(k successes, n−k failures)
       ⇒  Beta(α + k, β + n − k) posterior
posterior mean = (α + k)/(α + β + n)
```

Which is literally **add-α smoothing**: pseudo-counts `α` and `β` are a prior. This connects
Naive Bayes smoothing, and it is also the maths behind Thompson sampling for bandits. ⭐⭐

| Likelihood | Conjugate prior |
|---|---|
| Bernoulli / Binomial | Beta |
| Categorical / Multinomial | Dirichlet |
| Poisson | Gamma |
| Normal (known σ²), mean | Normal |
| Normal, precision | Gamma |

---

## 5. EM — MLE when data is missing ⭐⭐

When there are latent variables `z` (cluster assignment, mixture component), the likelihood
`log Σ_z p(x, z|θ)` has a log-of-sum and no closed form. EM iterates:

```
E-step:  q(z) = p(z | x, θ_old)                  ← responsibilities
M-step:  θ_new = argmax_θ  E_{q}[ log p(x, z | θ) ]
```

```
 init θ
   │
   ▼
 ┌────────── E-step ──────────┐
 │ soft-assign each point to  │
 │ each component: γ_ik       │
 └────────────┬───────────────┘
              ▼
 ┌────────── M-step ──────────┐
 │ re-estimate π_k, μ_k, Σ_k  │
 │ as γ-weighted statistics   │
 └────────────┬───────────────┘
              ▼
   converged?  no → loop      yes → done
```

**Guarantee:** each iteration does not decrease the observed-data log-likelihood (proof via
Jensen: EM maximises the ELBO `E_q[log p(x,z|θ)] + H(q)`, which is tight after the E-step). It
converges to a **local** optimum, so initialisation matters — hence k-means++ style seeding.

⭐ **k-means is EM with hard assignments** on a Gaussian mixture with isotropic, equal-variance
components: the E-step becomes "assign to nearest centroid" and the M-step "recompute means".

---

## 6. Fisher information and why MLE is good

```
Score      s(θ) = ∂ log p(x|θ)/∂θ ,     E[s] = 0
Fisher     I(θ) = E[s²] = −E[∂²ℓ/∂θ²]
Cramér–Rao Var(θ̂_unbiased) ≥ 1/(n I(θ))
```

Asymptotically, `θ̂_MLE ≈ N(θ, 1/(nI(θ)))` — the MLE is consistent, asymptotically unbiased and
asymptotically efficient (attains the Cramér–Rao bound). That is *why* MLE is the default, and
the asymptotic normal form is where standard errors for logistic-regression coefficients come from.

---

## 7. Interview-ready comparisons

| | MLE | MAP | Full Bayes |
|---|---|---|---|
| Output | point estimate | point estimate | distribution |
| Prior | none | yes | yes |
| Overfits on small data | yes | less | least |
| Cost | cheap | cheap | expensive (integral) |
| ML analogue | unregularised fit | regularised fit | ensembles / BNN / MC dropout |

⚠️ **Traps**
- "MLE is unbiased" — false; Gaussian variance MLE is biased.
- "MAP is the Bayesian answer" — it is the posterior *mode*, a point estimate; the posterior mode
  is not reparameterisation-invariant, while the full posterior is.
- "More parameters always fit better" — the likelihood always improves, which is why model
  selection uses AIC (`2k − 2ℓ`) or BIC (`k log n − 2ℓ`) to penalise `k`.

---

## Recall questions

1. Derive the Bernoulli MLE from scratch.
2. Show that MSE is MLE under Gaussian noise, and say what loss heavy-tailed noise implies.
3. Show that a Gaussian prior gives ridge, and state what `λ` equals in terms of `σ²` and `τ²`.
4. Which prior gives lasso, and why does it produce exact zeros?
5. Why is the Gaussian variance MLE biased, and what is the correction?
6. State the Beta–Binomial conjugate update and connect it to Laplace smoothing.
7. Write the E and M steps, and state what EM is guaranteed to do.
8. In what precise sense is k-means a special case of EM?
9. What happens to MAP as `n → ∞`?
10. State the Cramér–Rao bound and what it says about the MLE.
