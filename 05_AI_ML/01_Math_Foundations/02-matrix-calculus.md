# Matrix Calculus

> Backpropagation, the normal equation, the logistic gradient and the softmax derivative are all applications of about eight identities. This file establishes them and derives the four results you will be asked for.

---

## 1. Layout convention ⚠️

Two conventions exist and mixing them is the commonest source of transposed-gradient bugs.

**Denominator layout (used here, and standard in ML):** the gradient has **the same shape as the variable**.
```
f : ℝⁿ → ℝ        ∇_x f ∈ ℝⁿ           (a column vector)
f : ℝ^{m×n} → ℝ   ∂f/∂W ∈ ℝ^{m×n}      (same shape as W)
```

**The practical rule:** if you are ever unsure of a transpose, **check shapes**. There is usually exactly one arrangement that is dimensionally valid, and that is the answer.

---

## 2. The identities you need

Let `x, a ∈ ℝⁿ`, `A ∈ ℝ^{n×n}`, `y = f(x)`.

| Expression | Gradient |
|---|---|
| `aᵀx` | `a` |
| `xᵀa` | `a` |
| `xᵀx` | `2x` |
| `xᵀAx` | `(A + Aᵀ)x`, and `2Ax` if A is symmetric |
| `Ax` (Jacobian) | `A` |
| `‖x‖₂` | `x/‖x‖₂` |
| `‖Ax − b‖²` | `2Aᵀ(Ax − b)` |
| `tr(AᵀB)` w.r.t. A | `B` |
| `log det(A)` w.r.t. A | `A⁻ᵀ` |

**📐 Proof of the quadratic form**, since it is the one used most:
```
f(x) = xᵀAx = Σᵢ Σⱼ xᵢ Aᵢⱼ xⱼ

∂f/∂x_k = Σⱼ A_kj xⱼ  +  Σᵢ xᵢ A_ik
        = (Ax)_k + (Aᵀx)_k

⇒ ∇f = (A + Aᵀ)x,   which is 2Ax when A = Aᵀ
```
(The two terms come from `k` appearing once as the row index and once as the column index.) ∎

---

## 3. Jacobian and Hessian

**Jacobian.** For `f : ℝⁿ → ℝᵐ`, `J ∈ ℝ^{m×n}` with `Jᵢⱼ = ∂fᵢ/∂xⱼ`. Row i is the gradient of output i.

**Hessian.** For `f : ℝⁿ → ℝ`, `H ∈ ℝ^{n×n}` with `Hᵢⱼ = ∂²f/∂xᵢ∂xⱼ`.

**Schwarz's theorem:** if the second partials are continuous, `∂²f/∂xᵢ∂xⱼ = ∂²f/∂xⱼ∂xᵢ`, so **the Hessian is symmetric**. That is why the spectral theorem applies to it and why its eigenvalues are real — which is what makes the second-derivative test work.

**The second-derivative test at a critical point** (`∇f = 0`):

| Hessian | Point |
|---|---|
| positive definite (all λ > 0) | local **minimum** |
| negative definite (all λ < 0) | local **maximum** |
| indefinite (mixed signs) | **saddle point** |
| singular | inconclusive |

⭐⭐ **Why this matters in deep learning:** in high dimensions, a critical point requires *all* n eigenvalues to share a sign. If signs were independent and equally likely, that has probability `2/2ⁿ`. So **critical points in high-dimensional non-convex landscapes are overwhelmingly saddle points, not local minima** — and this is the modern answer to "doesn't SGD get stuck in local minima?". It mostly does not; it slows near saddles, which is what momentum helps escape.

---

## 4. The chain rule in matrix form ⭐⭐⭐

Scalar: `dz/dx = (dz/dy)(dy/dx)`.

Vector: for `x → y → z` with z scalar,
```
∇_x z = Jᵀ ∇_y z          where J = ∂y/∂x
```

**The transpose is the entire content of backpropagation.** The forward pass applies J; the backward pass applies Jᵀ. Gradients flow backwards through the *transpose* of the forward map.

**📐 Worked example — a linear layer.** `y = Wx + b`, with a scalar loss L and `δ = ∂L/∂y` known.
```
∂L/∂x = Wᵀ δ                   shape: (n×m)(m×1) = (n×1) ✔ matches x
∂L/∂W = δ xᵀ                   shape: (m×1)(1×n) = (m×n) ✔ matches W
∂L/∂b = δ                      shape: (m×1)          ✔ matches b
```
**Memorise these three.** Every backprop derivation is repeated application of them, and the shape check confirms each one.

---

## 5. 📐 Derivation 1 — the normal equation ⭐⭐⭐

Minimise `L(w) = ‖Xw − y‖² = (Xw − y)ᵀ(Xw − y)`.

```
Expand:
L = wᵀXᵀXw − 2yᵀXw + yᵀy        (using yᵀXw = wᵀXᵀy, both scalars)

Differentiate, using ∇(wᵀAw) = 2Aw for symmetric A = XᵀX,
and ∇(aᵀw) = a:

∇L = 2XᵀXw − 2Xᵀy

Set to zero:
XᵀXw = Xᵀy              ← the normal equations
w* = (XᵀX)⁻¹Xᵀy         ← if XᵀX is invertible
```

**Three things to say about this result:**
1. **It is a minimum**, because the Hessian `2XᵀX` is positive semi-definite (`vᵀXᵀXv = ‖Xv‖² ≥ 0`), so the objective is convex.
2. **`XᵀX` is invertible ⟺ X has full column rank.** With d > n, or with perfectly collinear features, it is singular and the solution is not unique.
3. **The geometry:** the residual satisfies `Xᵀ(Xw − y) = 0`, i.e. the residual is **orthogonal to every column of X**. Least squares is exactly the orthogonal projection of y onto the column space of X. This is the elegant statement and the one to lead with.

**Cost:** O(nd² + d³). Fine for d in the hundreds; use gradient descent beyond that.

**Ridge version** — one line more:
```
L = ‖Xw − y‖² + λ‖w‖²    ⇒    ∇L = 2XᵀXw − 2Xᵀy + 2λw = 0
⇒ w* = (XᵀX + λI)⁻¹ Xᵀy
```
`XᵀX + λI` is **always invertible for λ > 0**, since it shifts every eigenvalue up by λ. That is the precise sense in which ridge fixes both non-identifiability and ill-conditioning.

---

## 6. 📐 Derivation 2 — the logistic regression gradient ⭐⭐⭐

Model: `p = σ(z)`, `z = wᵀx`, with `σ(z) = 1/(1 + e^{−z})`.

**Step 1 — the sigmoid derivative.**
```
σ'(z) = d/dz (1 + e^{−z})⁻¹
      = e^{−z}/(1 + e^{−z})²
      = [1/(1+e^{−z})] · [e^{−z}/(1+e^{−z})]
      = σ(z)(1 − σ(z))
```
This tidy self-referential form is why sigmoid was popular, and it is asked directly.

**Step 2 — the loss.** Binary cross-entropy for one sample:
```
L = −[y log p + (1 − y) log(1 − p)]
```

**Step 3 — differentiate.**
```
∂L/∂p = −y/p + (1 − y)/(1 − p) = (p − y)/(p(1 − p))
∂p/∂z = p(1 − p)

∂L/∂z = (p − y)/(p(1−p)) · p(1−p) = p − y        ← the p(1−p) cancels

∂L/∂w = (p − y) x
```

⭐⭐⭐ **The result `∂L/∂z = p − y` — prediction minus target — is the single most reused gradient in ML.** It holds for linear regression with MSE, logistic regression with cross-entropy, and softmax with cross-entropy. The cancellation is not a coincidence: it happens because each loss is the **canonical link** for its exponential-family distribution.

**⚠️ Why cross-entropy rather than MSE for classification** — a guaranteed question. With MSE the gradient would be `(p − y)·σ'(z) = (p − y)p(1 − p)`. When the model is **confidently wrong** (p ≈ 1, y = 0), `p(1−p) ≈ 0`, so the gradient **vanishes precisely when the error is largest** and learning stalls. Cross-entropy's cancellation removes that factor, so the gradient is proportional to the error. MSE is also non-convex in w for logistic regression, while cross-entropy is convex.

---

## 7. 📐 Derivation 3 — softmax and cross-entropy ⭐⭐⭐

Softmax: `pᵢ = e^{zᵢ} / Σⱼ e^{zⱼ}`.

**Step 1 — the Jacobian.**
```
∂pᵢ/∂zⱼ = pᵢ(δᵢⱼ − pⱼ)

  i = j:  ∂pᵢ/∂zᵢ = [e^{zᵢ}·S − e^{zᵢ}·e^{zᵢ}]/S² = pᵢ(1 − pᵢ)
  i ≠ j:  ∂pᵢ/∂zⱼ = −e^{zᵢ}e^{zⱼ}/S² = −pᵢpⱼ
```

**Step 2 — with cross-entropy `L = −Σᵢ yᵢ log pᵢ` and one-hot y:**
```
∂L/∂zⱼ = Σᵢ (∂L/∂pᵢ)(∂pᵢ/∂zⱼ)
       = Σᵢ (−yᵢ/pᵢ) · pᵢ(δᵢⱼ − pⱼ)
       = −Σᵢ yᵢ(δᵢⱼ − pⱼ)
       = −yⱼ + pⱼ Σᵢ yᵢ
       = pⱼ − yⱼ            since Σᵢ yᵢ = 1
```
So `∇_z L = p − y` again. ∎

**⚠️ Two implementation facts that follow:**
1. **Numerical stability.** `e^{z}` overflows for z ≈ 750 in float64. Subtract the max first: `softmax(z) = softmax(z − max z)`, which is mathematically identical because the constant cancels in the ratio.
2. **Never apply softmax then take the log separately.** Frameworks provide `log_softmax` and `cross_entropy_with_logits` precisely because computing `log(softmax(z))` in two steps loses precision. `nn.CrossEntropyLoss` in PyTorch takes **logits**, not probabilities — a genuinely common bug.

---

## 8. 📐 Derivation 4 — gradient of the L2 penalty, and weight decay

```
L = L_data + (λ/2)‖w‖²      ⇒    ∇L = ∇L_data + λw

Gradient descent step:
w ← w − η(∇L_data + λw) = (1 − ηλ)w − η∇L_data
```

⭐⭐ **L2 regularisation is multiplicative shrinkage of the weights at every step** — which is exactly why it is called **weight decay**. The `(1 − ηλ)` factor pulls every weight towards zero before the data term moves it.

**The equivalence breaks under Adam**, which is why AdamW exists: Adam divides the gradient by a running RMS, which also rescales the λw term inconsistently. AdamW applies the decay **separately from the adaptive scaling**, restoring the intended behaviour. This is a good detail to know for a DL interview.

---

## 9. Practical notes ⚠️

- **Gradient checking.** Compare the analytic gradient with `(f(x+ε) − f(x−ε))/(2ε)` for `ε ≈ 1e−5`; relative error should be under 1e−7. Use the **central** difference — the forward difference is O(ε) accurate, the central one O(ε²).
- **Shape debugging.** In almost every backprop bug, the shapes reveal the error. Print them.
- **Autodiff** applies the chain rule mechanically. **Reverse mode** (backprop) costs one forward and one backward pass for *all* parameters, which is why it wins when there are many parameters and one scalar output. **Forward mode** costs one pass per input variable, so it wins when inputs are few and outputs many.

---

## 10. Recall questions

1. State the denominator layout convention and the shape rule for checking gradients.
2. Derive `∇(xᵀAx)` from the summation form.
3. Why is the Hessian symmetric, and what does the second-derivative test say?
4. Why are high-dimensional critical points mostly saddle points? Give the probability argument.
5. State the matrix chain rule and explain why the transpose appears.
6. Give the three linear-layer gradients and verify their shapes.
7. Derive the normal equation and state the geometric interpretation.
8. When is `XᵀX` singular, and how does ridge fix it — in terms of eigenvalues?
9. Derive `σ'(z) = σ(z)(1 − σ(z))`.
10. Derive `∂L/∂z = p − y` for logistic regression, and explain the cancellation.
11. Explain precisely why MSE is a bad loss for classification.
12. Derive the softmax Jacobian and the cross-entropy gradient.
13. Why subtract the max before exponentiating in softmax?
14. Show that L2 regularisation is multiplicative weight decay, and explain why AdamW exists.
15. Why is reverse-mode autodiff the right choice for neural networks?
