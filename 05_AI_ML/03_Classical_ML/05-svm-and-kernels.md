
# SVMs, Margins and Kernels ⭐⭐

> **Core idea in 3 lines**
> 1. An SVM picks the separating hyperplane with the largest margin, which is a convex quadratic
>    programme with a unique solution.
> 2. Its dual depends on the data only through inner products, so replacing them with a kernel
>    gives non-linear boundaries without ever computing the feature map.
> 3. Only the points on the margin (support vectors) matter — a direct consequence of KKT
>    complementary slackness.

---

## 1. The margin 📐

A hyperplane is `wᵀx + b = 0`. The signed distance from a point `x` to it is

```
distance = (wᵀx + b) / ‖w‖
```

📐 *Why:* write `x = x_p + r·w/‖w‖` where `x_p` lies on the plane. Then
`wᵀx + b = wᵀx_p + b + r‖w‖ = r‖w‖`, so `r = (wᵀx + b)/‖w‖`. ∎

With labels `yᵢ ∈ {−1, +1}`, the **functional margin** is `yᵢ(wᵀxᵢ + b)` and the **geometric
margin** is that divided by `‖w‖`. Because `(w, b)` and `(cw, cb)` describe the same plane, we can
fix the scale by requiring the closest points to satisfy `yᵢ(wᵀxᵢ + b) = 1`. Then the margin width
is `2/‖w‖`.

**Maximising `2/‖w‖` = minimising `½‖w‖²`**, giving the hard-margin primal:

```
minimise   ½‖w‖²
subject to yᵢ(wᵀxᵢ + b) ≥ 1   for all i
```

A convex QP — unique global optimum. ⭐

```
        ●    ●                       ● class +1
   ●        ●          ○             ○ class −1
        ●  ╱     ╱     ╱
          ╱     ╱     ╱   ← margin = 2/‖w‖
     ●   ╱  ○  ╱     ╱  ○
        ╱     ╱     ╱
   ────╱─────╱─────╱────
    wᵀx+b=+1   =0   =−1
    circled points touching the dashed planes = SUPPORT VECTORS
```

---

## 2. Soft margin

Real data is not separable, so allow violations with slack `ξᵢ ≥ 0`:

```
minimise   ½‖w‖² + C Σ ξᵢ
subject to yᵢ(wᵀxᵢ + b) ≥ 1 − ξᵢ ,  ξᵢ ≥ 0
```

`ξᵢ = 0` ⇒ correct and outside the margin; `0 < ξᵢ < 1` ⇒ inside the margin but correct;
`ξᵢ > 1` ⇒ misclassified.

**`C` is the key hyperparameter ⭐⭐**

```
large C  → violations are expensive → narrow margin, fits training data hard → LOW bias, HIGH variance
small C  → violations cheap         → wide margin, more tolerant            → HIGH bias, LOW variance
```

⚠️ Note that `C` behaves like `1/λ`: large `C` means *less* regularisation. Interviewers check the
direction.

### The hinge-loss view ⭐

Eliminating `ξᵢ = max(0, 1 − yᵢ(wᵀxᵢ + b))` turns the problem into unconstrained ERM:

```
minimise   (1/C)·½‖w‖²  +  Σ max(0, 1 − yᵢ f(xᵢ))
             L2 penalty          hinge loss
```

So **an SVM is just hinge loss + L2 regularisation**, and can be trained with SGD (`LinearSVC`,
`SGDClassifier`) for large `n`.

```
loss
  │╲                       hinge:  max(0, 1 − y f)
  │ ╲                      zero once the point is correct AND past the margin
  │  ╲
  │   ╲______________
  └────┴──────────────► y·f(x)
       1
logistic loss is a smooth version of this that never reaches exactly zero —
which is why logistic regression uses ALL points and the SVM uses only a few. ⭐
```

---

## 3. 📐 The dual — derive it

Lagrangian with multipliers `αᵢ ≥ 0` (hard margin for clarity):

```
L(w, b, α) = ½‖w‖² − Σ αᵢ [ yᵢ(wᵀxᵢ + b) − 1 ]
```

Stationarity:

```
∂L/∂w = w − Σ αᵢ yᵢ xᵢ = 0   ⇒   w = Σ αᵢ yᵢ xᵢ      ⭐ w is a combination of the data
∂L/∂b = − Σ αᵢ yᵢ = 0        ⇒   Σ αᵢ yᵢ = 0
```

Substituting back:

```
½‖w‖² = ½ ΣΣ αᵢαⱼ yᵢyⱼ xᵢᵀxⱼ
Σ αᵢ yᵢ wᵀxᵢ = ΣΣ αᵢαⱼ yᵢyⱼ xᵢᵀxⱼ
```

so `L = Σαᵢ − ½ΣΣ αᵢαⱼyᵢyⱼ xᵢᵀxⱼ`, giving the **dual problem**:

```
maximise   Σ αᵢ − ½ ΣᵢΣⱼ αᵢαⱼ yᵢyⱼ (xᵢᵀxⱼ)
subject to Σ αᵢ yᵢ = 0 ,  0 ≤ αᵢ ≤ C        (the upper bound C comes from the soft margin)
```

∎ Two facts follow, and both are examinable:

1. **The data enters only as inner products `xᵢᵀxⱼ`** ⇒ the kernel trick is possible. ⭐⭐⭐
2. **Complementary slackness** `αᵢ[yᵢ(wᵀxᵢ+b) − 1 + ξᵢ] = 0` means `αᵢ > 0` only for points
   exactly on or inside the margin. All other points have `αᵢ = 0` and drop out of
   `w = Σαᵢyᵢxᵢ`. **Those with `αᵢ > 0` are the support vectors** — move any other point and the
   solution does not change. ⭐⭐⭐

Prediction: `f(x) = Σ_{i∈SV} αᵢyᵢ (xᵢᵀx) + b`.

---

## 4. Kernels ⭐⭐⭐

Replace `xᵢᵀxⱼ` with `K(xᵢ, xⱼ) = φ(xᵢ)ᵀφ(xⱼ)` for some feature map `φ`, **without ever computing
`φ`**.

*Concrete example.* For `x, z ∈ R²` and `K(x,z) = (xᵀz)²`:

```
(x₁z₁ + x₂z₂)² = x₁²z₁² + 2x₁x₂z₁z₂ + x₂²z₂²
               = (x₁², √2 x₁x₂, x₂²) · (z₁², √2 z₁z₂, z₂²)
```

so `φ(x) = (x₁², √2x₁x₂, x₂²)` — an explicit 3-dimensional map computed with one 2-d dot product
and a square. For the RBF kernel `φ` is **infinite-dimensional**, yet `K` costs `O(d)`.

| Kernel | Formula | Use |
|---|---|---|
| Linear | `xᵀz` | high-dimensional sparse data (text); fast, interpretable |
| Polynomial | `(γxᵀz + r)^d` | explicit feature interactions of degree `d` |
| **RBF / Gaussian** | `exp(−γ‖x − z‖²)` | the default non-linear choice |
| Sigmoid | `tanh(γxᵀz + r)` | rarely; not always a valid kernel |

**`γ` in the RBF kernel ⭐⭐**: `γ = 1/(2σ²)` controls the radius of influence of a single point.

```
small γ → wide, smooth influence → almost linear boundary → underfit
large γ → each point only influences its immediate neighbourhood → islands around
          training points → severe overfit (looks like 1-NN)
```

Grid-search `C` and `γ` together on a log scale — they interact.

**Mercer's condition:** `K` is a valid kernel iff the Gram matrix `K_ij = K(xᵢ,xⱼ)` is symmetric
positive semi-definite for every finite sample. Then `K` corresponds to an inner product in some
Hilbert space. Closure rules: sums, positive scalar multiples, products and compositions with
polynomials of kernels are kernels.

⚠️ **Always standardise before an RBF SVM** — the kernel is a function of Euclidean distance, so
unscaled features dominate it. This is one of the most common practical mistakes.

---

## 5. Complexity and when to use an SVM

```
training   O(n²d) to O(n³)  for kernel SVMs (SMO)   ⇒ impractical beyond ~100k rows ⚠️
prediction O(n_SV · d)                              ⇒ slow if there are many SVs
linear SVM via SGD:  O(nd) per epoch                ⇒ scales fine
memory     the n×n kernel matrix                    ⇒ 100k rows ≈ 80 GB ⚠️
```

| Use an SVM when | Prefer something else when |
|---|---|
| `d` is large relative to `n` (text, genomics) | `n > 10⁵` → linear SVM/SGD, or gradient boosting |
| a clear margin exists | heavy class overlap / noisy labels |
| you need a strong non-linear boundary on modest data | you need calibrated probabilities (SVM needs Platt scaling ⚠️) |
| memory for the kernel matrix is available | tabular data with mixed types → gradient boosting usually wins |

**Multiclass:** SVMs are inherently binary; libraries use one-vs-one (`K(K−1)/2` models, the
`libsvm` default) or one-vs-rest (`K` models).

**SVR (regression):** uses an ε-insensitive tube — errors smaller than `ε` cost nothing, so only
points outside the tube become support vectors.

---

## 6. SVM vs logistic regression ⭐⭐

| | SVM (hinge) | Logistic regression |
|---|---|---|
| Loss | hinge — exactly zero past the margin | log loss — never exactly zero |
| Decides using | support vectors only | all points |
| Output | a score; probabilities need Platt scaling | calibrated probabilities natively |
| Non-linearity | kernel trick | manual feature engineering (or a net) |
| Outliers | robust-ish (bounded influence for `α ≤ C`) | far points still contribute |
| Scales to huge `n` | only the linear version | yes |
| Convex | yes | yes |

---

## Recall questions

1. Derive the distance from a point to a hyperplane, and show the margin is `2/‖w‖`.
2. Write the soft-margin primal and say what `C` does — including the direction.
3. Derive the dual and point out the two facts that make SVMs special.
4. What does complementary slackness tell you about support vectors?
5. Show explicitly that `K(x,z) = (xᵀz)²` corresponds to a 3-d feature map in `R²`.
6. What does `γ` control in an RBF kernel, and what happens at each extreme?
7. State Mercer's condition.
8. Why must you standardise before an RBF SVM?
9. Why don't SVMs scale to a million rows, and what do you use instead?
10. Compare hinge and logistic loss, and derive the consequence for which points matter.
