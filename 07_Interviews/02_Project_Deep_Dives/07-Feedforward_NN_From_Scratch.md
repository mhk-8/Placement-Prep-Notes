
# Project: Feedforward Neural Network from Scratch

> **Course:** DA6401 Introduction to Deep Learning · **Feb 2026**
> **Track:** ML · **Priority: ⭐⭐** · **On the master resume only**

**How to use this project:** it is not a headline project, and the 0.69 F1 is modest. Its value is
as **proof that you can hand-derive backpropagation** — which makes every other ML claim you make
more credible. Bring it up when someone asks "have you implemented anything without a framework?"
or as supporting evidence when discussing gradients.

⚠️ Do not lead with it, and do not defend the 0.69 as a good result. Frame it as an exercise in
mechanism, which is what it was.

---

## 1. The 20-second version

> "I implemented a configurable feedforward network entirely in NumPy — manual forward and backward
> passes with hand-derived gradients, four optimisers and two initialisation schemes — on MNIST and
> Fashion-MNIST."

## 2. The 60-second version

> "No autograd — everything in NumPy, including the backward pass, with the gradients derived by
> hand. The network is fully configurable from the command line: number and width of hidden layers,
> activation (sigmoid, tanh, ReLU), initialisation (Xavier or random), and optimiser.
>
> I implemented four optimisers as modular components — plain SGD, momentum, Nesterov and RMSProp —
> which is the part I learned most from, because writing them side by side makes the progression
> obvious: momentum fixes SGD's oscillation in ill-conditioned directions, Nesterov fixes
> momentum's overshoot by evaluating the gradient at the look-ahead point, and RMSProp fixes
> AdaGrad's monotonically decaying learning rate by using an exponential moving average instead of
> a running sum.
>
> It reached 0.69 F1 with hyperparameter sweeps tracked in Weights & Biases. That is a modest
> number — a small MLP on Fashion-MNIST is not a strong model, and the point of the exercise was
> the mechanism rather than the accuracy."

---

## 3. The backprop equations — be able to write these ⭐⭐⭐

Forward:
```
z^(l) = W^(l) a^(l−1) + b^(l)
a^(l) = g( z^(l) )
```

Backward — define the error signal `δ^(l) = ∂L/∂z^(l)`:
```
(BP1)  δ^(L) = ∇_a L ⊙ g'(z^(L))
              → for softmax + cross-entropy this collapses to  δ^(L) = ŷ − y     ⭐
(BP2)  δ^(l) = ( W^(l+1)ᵀ δ^(l+1) ) ⊙ g'( z^(l) )
(BP3)  ∂L/∂W^(l) = δ^(l) (a^(l−1))ᵀ
(BP4)  ∂L/∂b^(l) = δ^(l)
```

⭐ **These four equations are the single most-asked derivation in an ML interview.** Having
implemented them without autograd means you should be able to write them on a whiteboard without
hesitation — that is the whole payoff of this project.

Full derivation: `../../05_AI_ML/04_Deep_Learning/01-neural-networks-and-backprop.md`.

---

## 4. The four optimisers ⭐⭐

| Optimiser | Update | What it fixes |
|---|---|---|
| **SGD** | `w ← w − η∇L` | — |
| **Momentum** | `v ← βv + ∇L` ; `w ← w − ηv` | Oscillation in ill-conditioned directions. On a constant gradient the velocity saturates at `g/(1−β)` — a 10× amplification at β = 0.9 ⭐ |
| **Nesterov** | Gradient evaluated at the look-ahead point `w − ηβv` | Momentum's overshoot — the correction responds to where you are *going* |
| **RMSProp** | `v ← βv + (1−β)g²` ; `w ← w − ηg/(√v + ε)` | AdaGrad's monotonically decaying LR (it accumulates `Σg²` forever and eventually stalls) |

⭐ **The narrative that makes this worth discussing:** each optimiser fixes the previous one's
specific failure. Adam is then just momentum plus RMSProp plus bias correction — and the bias
correction exists because `m` and `v` start at zero, so `E[m_t] = (1−β₁ᵗ)E[g]` is biased toward
zero in the early steps. Being able to tell that story in order is worth far more than listing the
formulas.

---

## 5. Initialisation ⭐⭐

```
XAVIER/GLOROT (for tanh/sigmoid):  Var(w) = 2/(n_in + n_out)
HE/KAIMING    (for ReLU):          Var(w) = 2/n_in
```
**The derivation to have ready:** we want `Var(z) = Var(x)` across layers. With
`z = Σ_{i=1}^{n_in} wᵢxᵢ` and independence, `Var(z) = n_in · Var(w) · Var(x)`, giving
`Var(w) = 1/n_in`. ReLU zeroes roughly half the inputs, halving the variance, so the factor of 2
compensates.

**Why not zeros ⚠️:** every unit in a layer would compute the same thing and receive the same
gradient — symmetry is never broken and the layer has the capacity of a single unit. Biases may be
zero.

---

## 6. Anticipated follow-ups ⭐⭐

<details><summary>"Derive the backward pass for a single layer."</summary>

Write BP1-BP4 (§3). The one to be able to derive rather than recite is BP2: since
`z^(l+1) = W^(l+1) g(z^(l)) + b`, the chain rule gives
`∂z^(l+1)_k/∂z^(l)_j = W^(l+1)_{kj} · g'(z^(l)_j)`, and summing over the `k` outputs that `z_j`
feeds gives `δ^(l) = (W^(l+1)ᵀ δ^(l+1)) ⊙ g'(z^(l))`.
</details>

<details><summary>"How did you verify your gradients were correct?"</summary>

**Gradient checking**: compare the analytic gradient against a central finite difference
`(L(θ+εe) − L(θ−εe)) / 2ε` with `ε ≈ 1e-5` in double precision, and check the *relative* error is
below about 1e-7. It is far too slow for training (two forward passes per parameter) but it is the
standard way to validate a hand-written backward pass. ⭐ If you did this, say so — it is exactly
what a careful implementer does.
</details>

<details><summary>"Why is backprop cheaper than finite differences?"</summary>

Reverse-mode automatic differentiation computes the gradient with respect to *all* parameters in
one backward sweep costing roughly 2× a forward pass. Finite differences need two forward passes
*per parameter* — `O(P)` forwards for `P` parameters. That asymmetry is the entire reason deep
learning is feasible.
</details>

<details><summary>"0.69 F1 seems low."</summary>

It is, and the honest answer is short: a small fully-connected network on Fashion-MNIST is a weak
model — there is no convolutional structure, so it cannot exploit spatial locality at all. A simple
CNN would reach well above 0.90. The exercise was about implementing the mechanism correctly, not
about the benchmark. ⭐ Say it plainly and move on; defending a weak number is worse than owning it.
</details>

<details><summary>"What did you learn from it?"</summary>

The genuinely useful answer: that the optimiser choice mattered more than the architecture at this
scale, and that most of the debugging time went into shape errors and the ordering of the backward
pass — which is exactly what autograd frameworks exist to eliminate. It also made every subsequent
project easier, because when a model does not train I now think in terms of gradient magnitudes
rather than hyperparameters.
</details>

---

## 7. Limitations to state proactively

```
- Fully connected only; no convolution, so it cannot exploit spatial structure. 0.69 F1
  reflects that, not a bug.
- NumPy on CPU; no GPU, no batching optimisation beyond mini-batches.
- No Adam (the natural next optimiser), no BatchNorm, no dropout.
- MNIST/Fashion-MNIST are toy datasets and conclusions from them rarely transfer.
```

---

## 8. The 30-second refresh

```
□ NumPy only, manual forward and backward, hand-derived gradients
□ BP1-BP4; δ^(L) = ŷ − y for softmax + cross-entropy
□ Four optimisers as a progression: SGD → momentum (oscillation) → NAG (overshoot)
  → RMSProp (AdaGrad's decaying LR)
□ Xavier 2/(n_in+n_out) for tanh; He 2/n_in for ReLU; never zeros (symmetry)
□ Gradient checking with central differences, relative error < 1e-7
□ 0.69 F1 — own it as a weak model, not a weak implementation
```
