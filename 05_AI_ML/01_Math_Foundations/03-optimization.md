# Optimisation

> Training a model is minimising a function. This file covers convexity, why gradient descent works, and what each optimiser actually fixes.

---

## 1. Convexity ⭐⭐

**Definition.** `f` is convex if for all `x, y` and `t ∈ [0,1]`:
```
f(tx + (1−t)y) ≤ t f(x) + (1−t) f(y)
```
The chord lies above the function.

**First-order condition:** `f(y) ≥ f(x) + ∇f(x)ᵀ(y − x)` — the tangent lies **below** the function everywhere.

**Second-order condition:** `f` is convex ⟺ `∇²f ⪰ 0` everywhere.

**⭐⭐ Why convexity matters, in one sentence:** for a convex function, **every local minimum is a global minimum**, so a gradient method that stops at a stationary point has found the answer.

**📐 Proof.** Let `x*` be a local minimum and suppose some `y` has `f(y) < f(x*)`. For small `t > 0`, convexity gives
```
f(x* + t(y − x*)) ≤ (1−t)f(x*) + t f(y) < f(x*)
```
So every neighbourhood of `x*` contains a point with a strictly smaller value, contradicting local minimality. ∎

**Operations preserving convexity:** non-negative weighted sums; composition with an affine map (`f(Ax+b)`); pointwise maximum; and `g∘f` where g is convex non-decreasing and f is convex.

**Which ML losses are convex in the parameters:**

| Model | Convex? |
|---|---|
| Linear regression (MSE) | ✔ |
| Ridge, Lasso | ✔ |
| Logistic regression (cross-entropy) | ✔ |
| SVM (hinge loss) | ✔ |
| **Neural networks** | ✘ — non-convex, and heavily so |
| k-means objective | ✘ — NP-hard globally; Lloyd's algorithm finds a local optimum |

⚠️ **The reframing that matters:** deep learning works *despite* non-convexity. As shown in `02-matrix-calculus.md`, high-dimensional critical points are overwhelmingly **saddle points** rather than poor local minima, and empirically most local minima in large networks reach similar loss. So the practical concern is escaping saddles and plateaus, not being trapped in a bad basin.

---

## 2. Gradient descent ⭐⭐⭐

```
w_{t+1} = w_t − η ∇f(w_t)
```

**Why the negative gradient?** The directional derivative along a unit `u` is `∇fᵀu = ‖∇f‖cos θ`, minimised at `θ = 180°`, i.e. `u = −∇f/‖∇f‖`. **The negative gradient is the direction of steepest local decrease** — and "local" is the caveat: it is optimal only infinitesimally, which is why curvature-aware methods can do better.

**Convergence for a convex, L-smooth f** (`‖∇f(x) − ∇f(y)‖ ≤ L‖x−y‖`), with `η = 1/L`:
```
f(w_t) − f* ≤ L‖w₀ − w*‖² / (2t)          →   O(1/t)
```
With **strong convexity** (`∇²f ⪰ μI`) convergence becomes **linear**:
```
f(w_t) − f* ≤ (1 − μ/L)ᵗ (f(w₀) − f*)
```
The ratio `κ = L/μ` is the **condition number**. A large κ means elongated, valley-shaped contours, and gradient descent zig-zags across the valley instead of running along it. **This single fact motivates feature scaling, momentum, and adaptive methods.**

**The learning rate.**

| η | Behaviour |
|---|---|
| too small | slow; may stall in a flat region |
| good | steady decrease |
| too large | oscillates across the valley |
| far too large | **diverges** — loss becomes NaN |

⚠️ **Loss going to NaN is almost always too large a learning rate** (or a log of zero). It is the first thing to check.

---

## 3. Batch, stochastic and mini-batch ⭐⭐

| Variant | Gradient from | Per-step cost | Noise | Notes |
|---|---|---|---|---|
| **Batch GD** | all n samples | O(n) | none | smooth, slow, one update per epoch |
| **SGD** | 1 sample | O(1) | high | fast updates, noisy path |
| **Mini-batch** | B samples | O(B) | moderate | **what everyone uses** |

**Why mini-batch wins on three counts:**
1. **Unbiased gradient estimate** — `E[∇f_B] = ∇f`, with variance scaling as `1/B`.
2. **Hardware.** A GPU computes a batch of 256 in barely more wall-clock time than a batch of 1, because the work is parallel. This is the dominant practical reason.
3. **The noise is useful.** Stochastic gradients help escape saddle points and sharp minima, and there is evidence that the resulting flatter minima generalise better.

**Batch size effects worth stating:**
- Larger batch ⟹ less noise ⟹ you can use a **larger learning rate**. The common heuristic is **linear scaling**: multiply the batch by k, multiply η by k, with a warmup.
- Very large batches often **generalise worse** ("the generalisation gap"), attributed to converging to sharper minima.
- Batch size is also bounded by memory, and gradient accumulation simulates a larger one.

---

## 4. Momentum ⭐⭐

**The problem:** in an ill-conditioned valley (large κ), plain GD oscillates across the narrow direction and creeps along the flat one.

**Classical momentum:**
```
v_t = β v_{t−1} + ∇f(w_t)
w_t = w_{t−1} − η v_t              typically β = 0.9
```

**What it does.** `v` is an exponentially weighted moving average of past gradients. Consistent components (along the valley) **accumulate**; oscillating components (across the valley) **cancel**. The effective step along a consistent direction is amplified by roughly `1/(1−β)` — a factor of 10 at β = 0.9.

The physical analogy is a heavy ball: it builds speed downhill and rolls through small bumps and plateaus rather than stopping in them.

**Nesterov accelerated gradient** evaluates the gradient **after** the momentum step:
```
v_t = β v_{t−1} + ∇f(w_{t−1} − ηβ v_{t−1})
```
It "looks ahead", so it decelerates earlier when approaching a minimum and overshoots less. It improves the convex convergence rate from O(1/t) to O(1/t²).

---

## 5. Adaptive methods ⭐⭐⭐

**The problem they solve:** one global learning rate is wrong when features have very different scales or frequencies. A rare but informative feature needs large steps; a common one needs small ones.

### AdaGrad
```
G_t = G_{t−1} + g_t²                 (element-wise)
w_t = w_{t−1} − η g_t / (√G_t + ε)
```
Per-parameter learning rates that shrink with accumulated gradient magnitude. **Fatal flaw:** `G` only grows, so the effective learning rate **decays monotonically to zero** and learning stops prematurely.

### RMSProp
```
E[g²]_t = β E[g²]_{t−1} + (1−β) g_t²        β ≈ 0.9
w_t = w_{t−1} − η g_t / (√E[g²]_t + ε)
```
Replaces the sum with an **exponentially weighted average**, so old gradients decay out and the learning rate can recover. This one change fixes AdaGrad.

### Adam ⭐⭐⭐ — the default
Combines momentum (first moment) with RMSProp (second moment):
```
m_t = β₁ m_{t−1} + (1−β₁) g_t                 β₁ = 0.9
v_t = β₂ v_{t−1} + (1−β₂) g_t²                β₂ = 0.999

m̂_t = m_t / (1 − β₁ᵗ)          ← bias correction
v̂_t = v_t / (1 − β₂ᵗ)

w_t = w_{t−1} − η m̂_t / (√v̂_t + ε)           η = 1e−3, ε = 1e−8
```

**📐 Why bias correction is needed — asked as a depth probe.** Initialise `m₀ = 0`. Then
```
m₁ = (1−β₁)g₁
m₂ = β₁(1−β₁)g₁ + (1−β₁)g₂
…
E[m_t] = (1 − β₁ᵗ) E[g]        for approximately stationary g
```
So `m_t` is biased **towards zero** early in training, severely at β₁ = 0.9 and catastrophically at β₂ = 0.999 (where `1 − β₂¹ = 0.001`, a 1000× underestimate of the second moment on step 1). Dividing by `(1 − βᵗ)` corrects the expectation exactly; the factor tends to 1 as t grows, so it matters only at the start. Without it, the first steps are enormous and training frequently diverges. ∎

**AdamW** decouples weight decay from the adaptive scaling (see `02-matrix-calculus.md` §8). **Use AdamW, not Adam with L2 in the loss** — this is the current default for transformers.

### Which optimiser, in practice

| Situation | Choice |
|---|---|
| Default, unknown problem | **AdamW**, η = 3e−4 |
| Transformers / NLP | **AdamW** with warmup + cosine decay |
| CNNs, image classification, chasing best accuracy | **SGD + momentum** with a schedule — often generalises slightly better |
| Sparse features | Adam or AdaGrad |
| Convex problem, well-conditioned | plain GD or L-BFGS |

⚠️ **"Adam always beats SGD" is false.** Adam converges faster in training loss; well-tuned SGD with momentum frequently reaches better *test* accuracy on vision tasks. Knowing this distinction is a real signal.

---

## 6. Learning rate schedules ⭐⭐

| Schedule | Formula | Use |
|---|---|---|
| Step decay | `η ← η·γ` every k epochs | classic CNN training |
| Exponential | `η_t = η₀ e^{−kt}` | smooth alternative |
| **Cosine annealing** | `η_t = η_min + ½(η₀−η_min)(1+cos(πt/T))` | **the modern default** |
| **Warmup** | linear ramp for the first few thousand steps | essential for transformers |
| One-cycle | warmup then anneal below the initial rate | fast convergence |
| ReduceLROnPlateau | drop when validation stalls | simple and robust |

**⭐⭐ Why warmup is necessary for transformers.** At initialisation, Adam's second-moment estimate `v` is based on very few samples and is unreliable, so the adaptive step size has enormous variance. Large early steps then destabilise the pre-LayerNorm residual stream. Ramping η from ~0 over a few thousand steps lets the moment estimates stabilise before full-size steps are taken. This is the standard question about why transformer training recipes look the way they do.

---

## 7. Constrained optimisation and Lagrange multipliers ⭐⭐

To minimise `f(x)` subject to `g(x) = 0`, form
```
L(x, λ) = f(x) − λ g(x)
```
and set `∇_x L = 0`, `∇_λ L = 0`.

**The geometric intuition:** at an optimum on the constraint surface, `∇f` must be **parallel to** `∇g`. If it had any component along the surface, you could move along the surface and improve — so no constrained optimum. `∇f = λ∇g` is exactly that parallelism.

**KKT conditions** extend this to inequalities `g(x) ≤ 0`:
```
Stationarity:            ∇f + Σ λᵢ ∇gᵢ = 0
Primal feasibility:      gᵢ(x) ≤ 0
Dual feasibility:        λᵢ ≥ 0
Complementary slackness: λᵢ gᵢ(x) = 0
```

⭐⭐ **Complementary slackness is the one to understand**: for each constraint, either the multiplier is zero (the constraint is inactive and irrelevant) or the constraint is tight. **In the SVM this is precisely what makes support vectors special** — only points on the margin have `λᵢ > 0`, and all others have `λᵢ = 0` and drop out of the solution entirely. That is why the decision boundary depends on a handful of points.

Where you will meet these: the SVM dual (`03_Classical_ML/05-svm.md`), PCA as a constrained maximisation (`04-pca-derivation.md`), and maximum-entropy derivations.

---

## 8. Second-order methods ⭐

**Newton's method:** `w_{t+1} = w_t − H⁻¹∇f`. Uses curvature, so it converges **quadratically** near the optimum and is invariant to the conditioning that cripples gradient descent.

**Why it is not used in deep learning:** the Hessian is `n × n` for n parameters. For a 100M-parameter model that is 10¹⁶ entries — storing it is impossible, let alone inverting it at O(n³).

**Quasi-Newton (BFGS, L-BFGS)** approximate `H⁻¹` from gradient differences. **L-BFGS** keeps only the last m updates and is genuinely useful for **small, convex, full-batch** problems — but it needs consistent gradients, so it interacts badly with mini-batch noise and dropout.

---

## 9. Diagnosing training problems ⚠️⭐⭐⭐

A question asked in essentially every ML interview: "your model isn't training — what do you check?"

| Symptom | Likely cause | Check |
|---|---|---|
| Loss is NaN | η too large; log(0); division by zero | lower η 10×; clip gradients; add ε inside logs |
| Loss flat from step 0 | η too small; dead ReLUs; bad init; wrong loss | overfit 10 samples first — if that fails, it is a bug not a tuning issue |
| Loss decreases then explodes | η too large late; exploding gradients in an RNN | gradient clipping; a decay schedule |
| Train loss falls, val loss rises | **overfitting** | regularisation, more data, early stopping |
| Both losses high and flat | **underfitting** | bigger model, train longer, better features |
| Loss oscillates violently | η too large; batch too small | lower η; raise batch size |
| Val loss below train loss | dropout/augmentation active at train time only | usually normal — not a bug |

⭐⭐⭐ **The single best debugging move: overfit a batch of ~10 samples to near-zero loss.** If the model cannot memorise ten examples, no amount of tuning will help — there is a bug in the data pipeline, the loss, or the label alignment. Saying this in an interview is a strong signal of having actually trained models.

---

## 10. Recall questions

1. Define convexity three ways, and prove that a local minimum is global.
2. Why is the negative gradient the steepest-descent direction, and what is the caveat?
3. What is the condition number κ, and what does a large κ do to gradient descent?
4. Give three reasons mini-batch beats both batch and pure SGD.
5. State the linear scaling rule and the generalisation-gap caveat.
6. Explain what momentum does to consistent and to oscillating gradient components, with the `1/(1−β)` factor.
7. What does Nesterov change, and what does it buy?
8. What is AdaGrad's fatal flaw, and how does RMSProp fix it?
9. Write the Adam update and **derive** why bias correction is required.
10. Why does AdamW exist?
11. Is "Adam always beats SGD" true? Explain.
12. Why do transformers need learning-rate warmup?
13. State the geometric intuition for Lagrange multipliers.
14. State complementary slackness and explain what it implies about SVM support vectors.
15. Why is Newton's method impractical for deep networks?
16. Give the diagnosis table for: NaN loss, flat loss, train falling while val rises.
17. What is the single best first debugging step when a model will not train?
