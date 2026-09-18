
# Activations, Vanishing and Exploding Gradients ⭐⭐⭐

> **Core idea in 3 lines**
> 1. Backprop multiplies many Jacobians together, so gradients decay or blow up exponentially with
>    depth unless something keeps the product near 1.
> 2. ReLU, careful initialisation, normalisation layers and residual connections are four
>    independent answers to that one problem.
> 3. "Why ReLU?" and "why residual connections?" are among the most frequently asked deep-learning
>    questions — both answers are about the gradient, not about accuracy.

---

## 1. The activation zoo

| Activation | `g(z)` | `g'(z)` | Range | Notes |
|---|---|---|---|---|
| Sigmoid | `1/(1+e^{−z})` | `g(1−g)` ≤ **0.25** | (0,1) | saturates; not zero-centred; use only for binary output ⚠️ |
| Tanh | `(e^z−e^{−z})/(e^z+e^{−z})` | `1 − g²` ≤ **1** | (−1,1) | zero-centred; still saturates |
| **ReLU** | `max(0,z)` | `1` if `z>0` else `0` | [0,∞) | default; cheap; sparse; **dying ReLU** ⚠️ |
| LeakyReLU | `max(αz, z)`, `α≈0.01` | `1` or `α` | ℝ | fixes dying ReLU |
| PReLU | learned `α` | | ℝ | one extra parameter per channel |
| ELU | `z` / `α(e^z−1)` | | (−α,∞) | smooth, negative saturation |
| **GELU** | `z·Φ(z)` | | ℝ | smooth; the transformer default (BERT, GPT) ⭐ |
| SiLU/Swish | `z·σ(z)` | | ℝ | smooth, non-monotonic; common in vision |
| **Softmax** | `e^{z_k}/Σe^{z_j}` | Jacobian `diag(p) − ppᵀ` | simplex | output layer only, mutually exclusive classes |
| Linear | `z` | `1` | ℝ | regression output |

### Why ReLU won ⭐⭐⭐

1. **Gradient is exactly 1 on the positive side** — no shrinking factor per layer, so the signal
   survives depth. Sigmoid's derivative is at most 0.25, so ten layers multiply by at most
   `0.25¹⁰ ≈ 10⁻⁶`.
2. **Cheap:** a comparison, no exponentials.
3. **Sparsity:** about half the units are off, giving an efficient, more linearly-separable code.
4. **No saturation for `z > 0`**, so training does not stall on large activations.

⚠️ **Dying ReLU.** If a large gradient step drives a unit's pre-activation permanently negative for
every input, its gradient is zero forever and the unit is dead. Causes: learning rate too high, a
large negative bias. Fixes: LeakyReLU/ELU/GELU, lower LR, He init, batch norm.

### Choosing quickly ⭐

```
hidden layers      → ReLU (vision/CNN), GELU (transformers), SiLU (modern CNNs)
binary output      → sigmoid
multiclass output  → softmax
multi-label output → K independent sigmoids  ⚠️ not softmax
regression output  → linear (or softplus/exp when the target must be positive)
RNN gates          → sigmoid (gates) + tanh (candidate state)
```

---

## 2. 📐 The vanishing/exploding gradient problem

From BP2, `δ^{(l)} = (W^{(l+1)ᵀ}δ^{(l+1)}) ⊙ g'(z^{(l)})`. Unrolling from layer `L` to layer `l`:

```
δ^{(l)} = [ Π_{k=l+1}^{L} W^{(k)ᵀ} D^{(k−1)} ] δ^{(L)} ,     D = diag(g'(z))
```

So the gradient at an early layer is a **product of `L − l` matrices**. Let `γ` be a typical
magnitude of `‖Wᵀ D‖`:

```
γ < 1  ⇒  ‖δ^{(l)}‖ ~ γ^{L−l} → 0        VANISHING: early layers stop learning
γ > 1  ⇒  ‖δ^{(l)}‖ ~ γ^{L−l} → ∞        EXPLODING: NaNs, wild oscillation
```

∎ Exponential in depth either way. Everything below is a way of keeping `γ ≈ 1`.

**Symptoms**

| | Vanishing | Exploding |
|---|---|---|
| Loss | plateaus early, barely moves | spikes, oscillates, becomes NaN |
| Gradient norms | ~1e−7 in early layers, normal in late ones | 1e3+ |
| Weights | early layers barely change from init | huge values |
| Typical cause | sigmoid/tanh, deep plain stacks, long RNN sequences | high LR, bad init, RNNs, no clipping |

**Diagnostic:** log the gradient norm **per layer** each epoch. A monotone decay towards the input
is the signature of vanishing gradients. ⭐

---

## 3. The five fixes ⭐⭐⭐

| Fix | Mechanism |
|---|---|
| **ReLU-family activations** | derivative 1 instead of ≤ 0.25 removes the per-layer shrink factor |
| **He/Xavier init** | sets `Var(W)` so the forward and backward variance is preserved at layer 1 |
| **Batch/Layer norm** | renormalises activations every layer, so the scale cannot drift over depth |
| **Residual connections** | adds an identity path so the Jacobian is `I + ∂F/∂x` — the gradient has a route that is not multiplied down |
| **Gradient clipping** | `g ← g · min(1, c/‖g‖)` caps the explosion (the standard fix for RNNs/transformers) |

📐 **Why residual connections work — the one-line proof.** For `y = x + F(x)`,

```
∂y/∂x = I + ∂F/∂x        ⇒    through L blocks:  Π (I + ∂F_l/∂x) = I + (higher-order terms)
```

The identity term guarantees a path along which the gradient is multiplied by **1**, so even if
every `∂F/∂x` is tiny the signal reaches the first layer. This is why ResNets train at 152 layers
and why every transformer block is `x + Attention(x)` and `x + FFN(x)`. ⭐⭐⭐

Two further points that earn credit: residuals also make it trivial for a block to learn the
identity (just push `F → 0`), so adding depth cannot hurt in principle; and empirically they
smooth the loss landscape.

---

## 4. Normalisation layers ⭐⭐⭐

```
BatchNorm:  normalise each FEATURE over the BATCH      μ, σ over (N, H, W) per channel
LayerNorm:  normalise each SAMPLE over its FEATURES    μ, σ over (C, H, W) per sample
             ŷ = γ · (x − μ)/√(σ² + ε) + β     γ, β are learned
```

```
   tensor (N samples × C features)
        C ──────────────►
   N  ┌───────────────────┐
   │  │ █ █ █ █ █ █ █ █ █ │  ← LayerNorm normalises ACROSS a row (one sample)
   ▼  │ █ █ █ █ █ █ █ █ █ │
      │ █ █ █ █ █ █ █ █ █ │
      └─┬─────────────────┘
        ▲
        BatchNorm normalises DOWN a column (one feature, across the batch)
```

| | BatchNorm | LayerNorm |
|---|---|---|
| Statistics over | the batch | the feature dimension |
| Depends on batch size | **yes** ⚠️ | no |
| Train ≠ inference | yes — uses running averages at inference ⚠️ | identical |
| Works with variable-length sequences | poorly | yes |
| Standard in | CNNs | **transformers, RNNs** ⭐ |

**What BN actually buys:** it allows much higher learning rates and reduces sensitivity to
initialisation, smoothing the optimisation landscape. (The original "internal covariate shift"
explanation has been largely discredited — Santurkar et al. showed the benefit is a smoother loss
surface. Saying this is a strong signal. ⭐⭐)

⚠️ **BatchNorm traps interviewers probe:**
- At inference it uses **running** mean/variance, so `model.eval()` is mandatory; forgetting it is
  a classic production bug.
- With batch size 1 or 2 the statistics are garbage — use GroupNorm or LayerNorm instead.
- BN before or after the activation is a real debate; the original paper puts it before, most
  implementations follow.
- The bias in a layer immediately before BN is redundant (BN subtracts the mean anyway) — set
  `bias=False`.
- BN leaks information across a batch, which breaks some contrastive/metric-learning setups.

Other variants: **GroupNorm** (channels in groups; batch-size independent, used in detection and
diffusion), **InstanceNorm** (per-sample per-channel; style transfer), **RMSNorm** (LayerNorm
without mean subtraction; used in LLaMA and most modern LLMs for speed).

**Pre-LN vs Post-LN** ⭐: `x + Attn(LN(x))` (pre) trains stably with little warmup and is what
modern LLMs use; `LN(x + Attn(x))` (post, the original transformer) gives slightly better final
quality but needs careful warmup or it diverges.

---

## 5. Gradient clipping

```
by norm (preferred):  if ‖g‖ > c:  g ← g·c/‖g‖       preserves direction
by value:             g ← clip(g, −c, c)              distorts direction
```

Typical `c = 1.0` for transformers, `0.25–5` for RNNs. It does not fix vanishing gradients — only
explosion.

---

## 6. Putting it together: what to check when training won't converge ⭐⭐⭐

```
1. Can you overfit 10 examples to ~zero loss?   NO → it is a BUG, not a modelling problem.
2. Are inputs normalised, and are labels aligned with inputs?
3. Is the learning rate sane?  Run an LR range test (loss vs LR sweep).
4. Print gradient norms per layer.  Decaying towards the input → vanishing.
                                    1e3+ anywhere → exploding; clip.
5. Any dead ReLUs?  Fraction of zero activations per layer; > 90% is a red flag.
6. Init: He for ReLU, Xavier for tanh.  Not all zeros.
7. Normalisation present?  model.train()/model.eval() set correctly?
8. optimizer.zero_grad() actually being called?
9. Loss: logits vs probabilities mix-up; log(0); wrong reduction.
10. Mixed precision: NaNs → check the loss scaler; fp16 overflows where bf16 does not.
```

---

## Recall questions

1. Derive why the gradient at layer `l` is a product of `L − l` Jacobians, and the consequence.
2. Why is sigmoid bad in hidden layers — give the number.
3. What is dying ReLU, what causes it, and three fixes?
4. Prove in one line why residual connections fix vanishing gradients.
5. BatchNorm vs LayerNorm: what does each normalise over, and why do transformers use LN?
6. What does BatchNorm actually do at inference, and what bug follows from forgetting it?
7. What is the modern explanation of why BN helps?
8. Pre-LN vs post-LN.
9. Clip by norm or by value — which and why?
10. A model's loss is flat from step 0. Walk through your diagnosis.
