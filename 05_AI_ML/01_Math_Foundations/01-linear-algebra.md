# Linear Algebra for ML

> Every model in this vault is linear algebra with something on top. This file covers what is actually used, with the theorems proved.

---

## 1. Vectors and the geometry that matters ⭐⭐

**Inner product** `⟨x, y⟩ = xᵀy = Σ xᵢyᵢ = ‖x‖‖y‖cos θ`.

Three consequences used constantly:
- `xᵀy = 0` ⟺ orthogonal.
- **Cosine similarity** `cos θ = xᵀy / (‖x‖‖y‖)` is the standard similarity in embedding spaces, because it ignores magnitude and compares direction.
- The **projection** of x onto y is `(xᵀy / ‖y‖²) y`. This single formula is the engine behind least squares, PCA and Gram–Schmidt.

**Norms.**
```
‖x‖₁ = Σ|xᵢ|            L1 — induces sparsity, robust to outliers
‖x‖₂ = √(Σxᵢ²)          L2 — Euclidean, differentiable everywhere
‖x‖∞ = max|xᵢ|          L∞
‖x‖₀ = number of non-zeros   (not a true norm)
```

⚠️ **Why the L1 ball induces sparsity** is geometric and is asked directly: the L1 ball `{x : ‖x‖₁ ≤ t}` is a diamond with **vertices on the axes**. The L2 ball is a sphere with no corners. When you shrink a convex loss's contours until they first touch the constraint set, touching a diamond most often happens **at a vertex**, where some coordinates are exactly zero. A sphere has no preferred points, so the contact point generically has all coordinates non-zero.

```
    L1 ball (diamond)              L2 ball (circle)
         │                              │
      ╱  │  ╲                      ╭────┼────╮
    ╱    │    ╲                   │     │     │
  ──┼────┼────┼──               ──┼─────┼─────┼──
    ╲    │    ╱                   │     │     │
      ╲  │  ╱                      ╰────┼────╯
         │                              │
   contours hit a VERTEX        contours hit a generic point
   → some coefficients = 0      → all coefficients ≠ 0
```

---

## 2. Matrices as linear maps

A matrix `A ∈ ℝ^{m×n}` is a linear map `ℝⁿ → ℝᵐ`. Everything else is a property of that map.

| Concept | Definition | Why it matters in ML |
|---|---|---|
| **Column space** | span of the columns | the set of achievable predictions `Xw` |
| **Null space** | `{x : Ax = 0}` | directions in which the data says nothing — the source of non-identifiability |
| **Rank** | dim(column space) | number of independent features; rank deficiency ⟹ multicollinearity |
| **Trace** | `Σ Aᵢᵢ` | `tr(AB) = tr(BA)`, the identity behind many gradient derivations |
| **Determinant** | volume scaling factor | `det = 0` ⟺ singular ⟺ not invertible |

**Rank–nullity theorem:** `rank(A) + dim(null(A)) = n`. Directly explains why a design matrix with more features than samples (n > m) has a non-trivial null space, so the least-squares solution is **not unique** — which is one of the two motivations for regularisation.

---

## 3. Eigenvalues and eigenvectors ⭐⭐⭐

**Definition.** `Av = λv`, `v ≠ 0`. The map leaves the *direction* of v unchanged and scales it by λ.

Found by solving `det(A − λI) = 0` (the characteristic polynomial), then `(A − λI)v = 0` for each root.

**Properties worth knowing cold:**
- `Σλᵢ = tr(A)` and `Πλᵢ = det(A)`.
- A is invertible ⟺ no eigenvalue is 0.
- Eigenvalues of `A^k` are `λ^k`; of `A⁻¹` are `1/λ`.

### 📐 Theorem: a real symmetric matrix has real eigenvalues and orthogonal eigenvectors

This is the **spectral theorem**, and it is what makes PCA work. Both halves are short enough to prove on a whiteboard.

**Part 1 — eigenvalues are real.**
Let `Av = λv` with A real symmetric, allowing complex v. Take the conjugate transpose:
```
v*Av = λ v*v                                  … (i)
Conjugate-transposing both sides of Av = λv:  v*Aᵀ = λ̄ v*
Since A is real symmetric, Aᵀ = A, so        v*A = λ̄ v*
Right-multiply by v:                          v*Av = λ̄ v*v     … (ii)
```
Comparing (i) and (ii): `λ v*v = λ̄ v*v`. Since `v*v = ‖v‖² > 0`, we get `λ = λ̄`, so λ is real. ∎

**Part 2 — eigenvectors for distinct eigenvalues are orthogonal.**
Let `Av₁ = λ₁v₁` and `Av₂ = λ₂v₂` with `λ₁ ≠ λ₂`.
```
λ₁(v₁ᵀv₂) = (Av₁)ᵀv₂ = v₁ᵀAᵀv₂ = v₁ᵀAv₂ = λ₂(v₁ᵀv₂)
⇒ (λ₁ − λ₂)(v₁ᵀv₂) = 0
⇒ v₁ᵀv₂ = 0   since λ₁ ≠ λ₂
```
∎

**The consequence — eigendecomposition.** A real symmetric A can be written `A = QΛQᵀ` where Q is **orthogonal** (`QᵀQ = I`, columns are the orthonormal eigenvectors) and Λ is diagonal with the eigenvalues. Because Q is orthogonal, `Q⁻¹ = Qᵀ`, which is why this factorisation is numerically pleasant and why covariance matrices — which are always symmetric — decompose so cleanly.

---

## 4. Positive definiteness ⭐⭐

A symmetric A is **positive definite** if `xᵀAx > 0` for all `x ≠ 0` (**semi-definite** if ≥ 0).

**Equivalent characterisations:** all eigenvalues > 0; all leading principal minors > 0; `A = BᵀB` for some full-column-rank B.

**Where it appears:**
- A **covariance matrix is always positive semi-definite**, because `xᵀΣx = Var(xᵀz) ≥ 0` — a variance cannot be negative. That one-line proof is worth knowing.
- A twice-differentiable function is **convex** ⟺ its Hessian is positive semi-definite everywhere. This is the bridge between linear algebra and optimisation.
- A kernel matrix must be PSD for the SVM dual to be a valid convex problem (Mercer's condition).

---

## 5. Singular Value Decomposition ⭐⭐⭐

**Every** matrix — square or not, symmetric or not — factorises as

```
A = U Σ Vᵀ       A ∈ ℝ^{m×n}
U ∈ ℝ^{m×m}  orthogonal    (left singular vectors)
Σ ∈ ℝ^{m×n}  diagonal, σ₁ ≥ σ₂ ≥ … ≥ 0   (singular values)
V ∈ ℝ^{n×n}  orthogonal    (right singular vectors)
```

**📐 Where it comes from.** `AᵀA` is symmetric PSD, so by the spectral theorem `AᵀA = VΛVᵀ`. Set `σᵢ = √λᵢ` and `uᵢ = Avᵢ/σᵢ`. Then:
```
AᵀA vᵢ = λᵢ vᵢ
uᵢᵀuⱼ = (Avᵢ)ᵀ(Avⱼ)/(σᵢσⱼ) = vᵢᵀAᵀAvⱼ/(σᵢσⱼ) = λⱼ vᵢᵀvⱼ/(σᵢσⱼ) = δᵢⱼ
```
so the uᵢ are orthonormal, and `Avᵢ = σᵢuᵢ` assembles into `AV = UΣ`, i.e. `A = UΣVᵀ`. ∎

**The geometric reading:** every linear map is a **rotation, then a scaling along axes, then another rotation**. That is the entire content of SVD, and it is the sentence to say in an interview.

### Eckart–Young: the best low-rank approximation ⭐⭐

Truncating to the top k singular values, `A_k = Σᵢ₌₁ᵏ σᵢuᵢvᵢᵀ`, gives the **best** rank-k approximation in both Frobenius and spectral norm:
```
‖A − A_k‖_F = √(σ²_{k+1} + … + σ²_r)    and this is minimal over all rank-k B
```

This single theorem underpins PCA, latent semantic analysis, recommender-system matrix factorisation, and model compression by low-rank factorisation — **including LoRA**, which is exactly the observation that a weight update can be well approximated by a low-rank matrix.

**SVD vs eigendecomposition** — a standard question:

| | Eigendecomposition | SVD |
|---|---|---|
| Applies to | square matrices (diagonalisable) | **any** matrix |
| Factors orthogonal | only if symmetric | **always** |
| Values | can be negative or complex | **always real, ≥ 0** |
| Numerical stability | poorer | **better** |

---

## 6. Matrix identities used in derivations

```
(AB)ᵀ = BᵀAᵀ            (AB)⁻¹ = B⁻¹A⁻¹
tr(AB) = tr(BA)          tr(ABC) = tr(BCA) = tr(CAB)   (cyclic)
rank(AB) ≤ min(rank A, rank B)
xᵀAx  is a scalar, so  xᵀAx = (xᵀAx)ᵀ = xᵀAᵀx
```

That last one is used constantly: for the quadratic form only the symmetric part of A matters.

---

## 7. What appears in ML, concretely

| ML object | Linear algebra |
|---|---|
| A dataset | matrix `X ∈ ℝ^{n×d}` — n samples, d features |
| A linear model | `ŷ = Xw`, a map into the column space of X |
| Least squares | orthogonal projection of y onto col(X) |
| Covariance matrix | `Σ = (1/n)XᵀX` for centred X — symmetric PSD |
| PCA | eigendecomposition of Σ, or SVD of X |
| A neural network layer | `h = σ(Wx + b)` — affine map then a non-linearity |
| Attention | `softmax(QKᵀ/√d)V` — three matrix products |
| Embedding lookup | a row selection, i.e. multiplication by a one-hot vector |
| Multicollinearity | X is near rank-deficient, so XᵀX is near-singular |

---

## 8. Numerical realities ⚠️

- **Never invert a matrix to solve `Ax = b`.** Use a factorisation (LU, QR, Cholesky). Inversion is slower and numerically worse. `np.linalg.solve`, not `np.linalg.inv(A) @ b`.
- **Condition number** `κ = σ_max/σ_min`. Large κ means small input perturbations cause large output changes. Highly correlated features make `XᵀX` ill-conditioned — and **ridge regression fixes this directly**, because `XᵀX + λI` shifts every eigenvalue up by λ.
- Prefer **Cholesky** for symmetric positive definite systems: twice as fast as LU.

---

## 9. Recall questions

1. Explain geometrically why the L1 ball induces sparsity and the L2 ball does not.
2. State and prove that a real symmetric matrix has real eigenvalues.
3. State and prove that eigenvectors of distinct eigenvalues of a symmetric matrix are orthogonal.
4. Why is a covariance matrix always positive semi-definite? Give the one-line proof.
5. What is the relationship between convexity and the Hessian?
6. Derive the SVD from the eigendecomposition of `AᵀA`.
7. State the geometric interpretation of SVD in one sentence.
8. State the Eckart–Young theorem and name three ML applications.
9. Give four differences between eigendecomposition and SVD.
10. Why does ridge regression fix ill-conditioning? Express it in terms of eigenvalues.
11. State the rank–nullity theorem and explain what it implies when d > n.
12. Why should you never use `inv(A) @ b`?
