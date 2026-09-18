
# PCA — Derived Three Ways ⭐⭐⭐

> **Core idea in 3 lines**
> 1. PCA finds an orthonormal basis in which the data's variance is concentrated in as few coordinates as possible.
> 2. That basis is the eigenvectors of the covariance matrix, ordered by eigenvalue.
> 3. Equivalently it is the best low-rank approximation of the centred data matrix — which is exactly the SVD.

This is the single most-asked derivation in ML interviews. You should be able to do the
Lagrange version on a whiteboard in under four minutes.

---

## 1. Setup and the one step everyone forgets ⚠️

Let `X ∈ R^{n×d}` be `n` samples in `d` dimensions.

**Centre the data first:**

```
x̄ = (1/n) Σ xᵢ
X_c = X − 1 x̄ᵀ
```

The covariance matrix is

```
Σ = (1/(n−1)) X_cᵀ X_c        (d × d, symmetric, PSD)
```

⚠️ **If you do not centre, the first "principal component" points at the mean**, not at the
direction of maximum variance. `XᵀX` without centring is the second-moment matrix, not the
covariance. Interviewers ask "what happens if you skip centring?" precisely to check this.

---

## 2. Derivation A — maximise variance (Lagrange multipliers) 📐

**Goal.** Find a unit vector `v` such that the projection of the data onto `v` has maximum variance.

The projection of sample `xᵢ` onto direction `v` is the scalar `vᵀxᵢ`. Since the data is centred,
the mean of these scalars is zero, so their variance is

```
Var(vᵀx) = (1/(n−1)) Σᵢ (vᵀxᵢ)²
         = (1/(n−1)) Σᵢ vᵀxᵢ xᵢᵀ v
         = vᵀ [ (1/(n−1)) Σᵢ xᵢxᵢᵀ ] v
         = vᵀ Σ v
```

Without a constraint we could make this infinite by scaling `v`, so we require `vᵀv = 1`.

**Constrained problem:**

```
maximise   vᵀΣv       subject to   vᵀv = 1
```

**Lagrangian:**

```
L(v, λ) = vᵀΣv − λ(vᵀv − 1)
```

**Stationarity** (using `∇_v(vᵀAv) = (A + Aᵀ)v = 2Av` for symmetric `A`):

```
∇_v L = 2Σv − 2λv = 0
⇒  Σv = λv
```

**The optimality condition is literally the eigenvalue equation.** So the maximiser is an
eigenvector of `Σ`. Which one? Substitute back:

```
vᵀΣv = vᵀ(λv) = λ vᵀv = λ
```

The objective value *equals the eigenvalue*, so the maximum is attained at the eigenvector with
the **largest** eigenvalue `λ₁`. That is PC1.

**Subsequent components.** For PC2 add the constraint `v₂ᵀv₁ = 0`:

```
L = v₂ᵀΣv₂ − λ(v₂ᵀv₂ − 1) − μ(v₂ᵀv₁)
∇ ⇒ 2Σv₂ − 2λv₂ − μv₁ = 0
```

Left-multiply by `v₁ᵀ`:  `2v₁ᵀΣv₂ − 2λv₁ᵀv₂ − μv₁ᵀv₁ = 0`.
Since `Σ` is symmetric, `v₁ᵀΣv₂ = (Σv₁)ᵀv₂ = λ₁v₁ᵀv₂ = 0`, and `v₁ᵀv₂ = 0`, so `μ = 0`
and again `Σv₂ = λv₂`. By the spectral theorem the eigenvectors are already orthogonal, so PC2
is the eigenvector of the second-largest eigenvalue. Induction gives all `d` components.

✅ **The punchline to say out loud:** *"The principal components are the eigenvectors of the
covariance matrix, and the variance captured by each is its eigenvalue."*

---

## 3. Derivation B — minimise reconstruction error 📐

A completely different objective that lands on the same answer — good to mention, it signals depth.

**Goal.** Find a `k`-dimensional subspace, spanned by orthonormal `V_k = [v₁ … v_k]`, minimising
the squared distance from each point to the subspace.

The orthogonal projection of `x` onto the subspace is `P x = V_k V_kᵀ x`. Minimise

```
J = Σᵢ ‖xᵢ − V_kV_kᵀxᵢ‖²
```

Expand one term, writing `P = V_kV_kᵀ` (note `P² = P`, `Pᵀ = P`):

```
‖x − Px‖² = xᵀx − 2xᵀPx + xᵀPᵀPx
          = xᵀx − 2xᵀPx + xᵀPx
          = ‖x‖² − xᵀPx
```

So

```
J = Σᵢ ‖xᵢ‖² − Σᵢ xᵢᵀ V_kV_kᵀ xᵢ
```

The first term is a constant (does not depend on `V_k`). Therefore

```
minimise J   ⟺   maximise Σᵢ ‖V_kᵀxᵢ‖²  =  (n−1) · trace(V_kᵀ Σ V_k)
```

which is exactly the variance-maximisation objective of Derivation A generalised to `k`
directions. Its solution is the top-`k` eigenvectors, and the residual error equals the sum of
the discarded eigenvalues:

```
J_min = (n−1) Σ_{j=k+1}^{d} λⱼ
```

✅ **Say this:** *"Maximising retained variance and minimising reconstruction error are the same
optimisation problem, because total variance is fixed: retained + discarded = constant."*

---

## 4. Derivation C — straight from the SVD (what code actually does) ⭐⭐⭐

Take the thin SVD of the **centred** matrix:

```
X_c = U S Vᵀ ,   U ∈ R^{n×r}, S = diag(σ₁ ≥ … ≥ σ_r), V ∈ R^{d×r}
```

Then

```
Σ = X_cᵀX_c/(n−1) = V S Uᵀ U S Vᵀ /(n−1) = V (S²/(n−1)) Vᵀ
```

This is an eigendecomposition of `Σ`. Hence:

| PCA quantity | SVD quantity |
|---|---|
| Principal directions (loadings) | columns of `V` |
| Variance of component `j` | `λⱼ = σⱼ²/(n−1)` |
| Scores (projected data) | `X_c V = U S` |
| Rank-`k` reconstruction | `U_k S_k V_kᵀ` (Eckart–Young optimal) |

**Why libraries use SVD and not `eig(XᵀX)`:** forming `XᵀX` squares the condition number
(`κ(XᵀX) = κ(X)²`), so small singular values are destroyed by floating-point error. SVD works on
`X` directly. This is the same numerical-stability point as the normal equation vs QR. 📐⚠️

---

## 5. Explained variance and choosing `k`

```
explained variance ratio of component j  =  λⱼ / Σ_m λ_m
cumulative EVR(k)                        =  (Σ_{j≤k} λⱼ) / (Σ_m λ_m)
```

Ways to pick `k` that you can defend:

| Method | How | Caveat |
|---|---|---|
| Cumulative EVR threshold | smallest `k` with EVR ≥ 0.95 | 0.95 is arbitrary; tie it to the downstream task |
| Scree plot / elbow | plot `λⱼ`, look for the knee | subjective |
| Kaiser rule | keep `λⱼ > 1` on standardised data | over-retains for large `d` |
| Downstream CV | treat `k` as a hyperparameter | best answer in an applied interview ⭐ |

---

## 6. Standardise or only centre? ⚠️⭐⭐

- PCA on the **covariance** matrix (centre only) — correct when all features share units and
  scale (e.g. pixel intensities).
- PCA on the **correlation** matrix (centre **and** divide by std) — correct when units differ.
  Otherwise a feature measured in grams dominates the same feature in kilograms by a factor of
  10⁶ in variance.

Classic trap question: *"Salary in rupees and age in years — what happens?"* Salary's variance is
~10⁸ times larger, so PC1 is essentially the salary axis and PCA has learnt nothing.
**Standardise.**

---

## 7. Flow of the algorithm

```
        raw X (n × d)
             │
             ▼
   ┌──────────────────────┐
   │ 1. centre (and scale │   ⚠️ skipping this is the #1 bug
   │    if units differ)  │
   └──────────┬───────────┘
              ▼
   ┌──────────────────────┐        ┌──────────────────────────┐
   │ 2a. Σ = XᵀX/(n−1)    │   or   │ 2b. SVD:  X_c = U S Vᵀ   │
   │     eig(Σ) → λ, V    │        │     λ = σ²/(n−1)         │
   └──────────┬───────────┘        └────────────┬─────────────┘
              └──────────────┬──────────────────┘
                             ▼
              ┌────────────────────────────┐
              │ 3. sort λ desc, take top k │
              └─────────────┬──────────────┘
                            ▼
              ┌────────────────────────────┐
              │ 4. Z = X_c V_k   (n × k)   │  ← scores, the new features
              └─────────────┬──────────────┘
                            ▼
              ┌────────────────────────────┐
              │ 5. reconstruct  X̂ = Z V_kᵀ │  (+ add mean back)
              └────────────────────────────┘
```

**Fit on train only.** Compute mean, std and `V_k` from the training split and *apply* them to
validation/test. Fitting PCA on the full dataset before splitting is data leakage. ⚠️⭐⭐⭐

---

## 8. Complexity

| Route | Cost |
|---|---|
| Covariance + full eig | `O(nd² + d³)` |
| Full SVD of `X` | `O(min(n²d, nd²))` |
| Truncated / randomised SVD for top `k` | `O(ndk)` — what `sklearn` uses for large `d` |

When `d ≫ n` (e.g. 10 000 genes, 100 patients), use the **Gram trick**: eigendecompose the `n×n`
matrix `X_cX_cᵀ` instead; if `X_cX_cᵀu = λu` then `v = X_cᵀu/‖X_cᵀu‖` is an eigenvector of `X_cᵀX_c`
with the same `λ`.

---

## 9. What PCA is *not* ⚠️

| Misconception | Reality |
|---|---|
| "PCA is feature selection" | It is feature **extraction** — every PC is a dense linear combination of *all* original features, so interpretability is lost |
| "PCA improves accuracy" | It usually loses a little signal; it buys speed, memory, decorrelation and noise reduction |
| "PCA keeps the classes separable" | PCA is **unsupervised** — it maximises total variance, which may be orthogonal to the discriminative direction. Use **LDA** if you want class separation ⭐ |
| "PCA removes outliers" | Squared error makes PCA *very* sensitive to outliers |
| "PCA handles non-linear structure" | It is a linear projection. Use kernel PCA, t-SNE/UMAP (visualisation only), or an autoencoder |

**PCA vs LDA in one line:** PCA maximises `vᵀΣv` (total scatter); LDA maximises
`vᵀS_B v / vᵀS_W v` (between-class over within-class scatter). LDA is supervised and gives at most
`C−1` directions for `C` classes.

**PCA vs autoencoder:** a linear autoencoder with squared loss spans the same subspace as PCA
(though not necessarily the same orthogonal axes); non-linear activations make it strictly more
expressive.

---

## 10. Reference implementation (interviewers do ask you to code it)

```python
import numpy as np

class PCA:
    def __init__(self, k):
        self.k = k

    def fit(self, X):
        self.mean_ = X.mean(axis=0)
        Xc = X - self.mean_
        # SVD route: numerically stable, no explicit covariance matrix
        U, S, Vt = np.linalg.svd(Xc, full_matrices=False)
        self.components_ = Vt[:self.k]                  # (k, d)
        n = X.shape[0]
        var = (S ** 2) / (n - 1)
        self.explained_variance_ = var[:self.k]
        self.explained_variance_ratio_ = var[:self.k] / var.sum()
        return self

    def transform(self, X):
        return (X - self.mean_) @ self.components_.T    # (n, k)

    def inverse_transform(self, Z):
        return Z @ self.components_ + self.mean_
```

⚠️ **Sign ambiguity:** eigenvectors are defined up to sign, so `sklearn` and your code may return
components differing by a factor of `−1`. This is not a bug; projections differ only in sign.

---

## Recall questions

1. Write the constrained optimisation problem for PC1 and show that stationarity gives `Σv = λv`.
2. Why does the objective value equal the eigenvalue?
3. Prove that minimising reconstruction error is equivalent to maximising retained variance.
4. Why do libraries use SVD of `X` rather than eigendecomposition of `XᵀX`?
5. Features are salary (₹) and age (years). What goes wrong, and what is the fix?
6. What does PCA optimise that LDA does not, and when does that matter?
7. `d = 10000`, `n = 100`. How do you compute the top 5 components efficiently?
8. Where exactly in a train/test pipeline must PCA be fitted, and why?
