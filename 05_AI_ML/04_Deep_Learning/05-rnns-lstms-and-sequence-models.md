
# RNNs, LSTMs and Sequence Modelling ⭐⭐

> **Core idea in 3 lines**
> 1. An RNN applies the *same* weights at every timestep and carries a hidden state, so it can
>    handle variable-length sequences — but backprop through time multiplies the same matrix
>    repeatedly, which is the worst case for vanishing gradients.
> 2. LSTMs and GRUs fix this with gates and an **additive** cell state, giving the gradient a
>    highway.
> 3. Transformers replaced them because attention removes the sequential dependency and gives an
>    `O(1)` path between any two positions — knowing *why* is the real question.

---

## 1. The vanilla RNN

```
h_t = tanh( W_hh h_{t−1} + W_xh x_t + b_h )
y_t = W_hy h_t + b_y
```

```
   x₁      x₂      x₃            same W at every step  ⭐
    │       │       │
    ▼       ▼       ▼
 ┌────┐  ┌────┐  ┌────┐
 │ h₁ │─►│ h₂ │─►│ h₃ │─► …
 └────┘  └────┘  └────┘
    │       │       │
    ▼       ▼       ▼
   y₁      y₂      y₃
```

Weight sharing across time is what makes the parameter count independent of sequence length and
lets the model generalise a pattern to any position.

**Configurations:** one-to-many (image captioning), many-to-one (sentiment), many-to-many aligned
(POS tagging), many-to-many unaligned (encoder–decoder for translation).

---

## 2. 📐 BPTT and why vanilla RNNs fail

Unrolling, the gradient of the loss at time `T` with respect to `h_t` is

```
∂L_T/∂h_t = ∂L_T/∂h_T · Π_{k=t+1}^{T} ∂h_k/∂h_{k−1} ,
∂h_k/∂h_{k−1} = diag(1 − h_k²) · W_hh          (for tanh)
```

So the same matrix `W_hh` is multiplied `T − t` times. If its largest singular value `σ_max < 1`
the product decays geometrically; if `σ_max > 1` it explodes.

```
σ_max < 1  ⇒  gradient → 0   : the model cannot learn dependencies beyond ~10 steps ⚠️
σ_max > 1  ⇒  gradient → ∞   : NaNs   (fix: gradient clipping)
```

∎ ⚠️ This is worse than depth in a feed-forward net, because there the matrices differ and their
effects partly cancel; here it is a power of one matrix. ⭐⭐

**Truncated BPTT** backpropagates only `k` steps (e.g. 35 for language modelling) to bound memory
and compute, at the cost of never learning dependencies longer than `k`.

---

## 3. LSTM ⭐⭐⭐

```
f_t = σ(W_f [h_{t−1}, x_t] + b_f)          forget gate   — what to drop from the cell
i_t = σ(W_i [h_{t−1}, x_t] + b_i)          input gate    — how much new info to admit
c̃_t = tanh(W_c [h_{t−1}, x_t] + b_c)       candidate     — the new content
c_t = f_t ⊙ c_{t−1}  +  i_t ⊙ c̃_t          CELL STATE    ⭐ additive update
o_t = σ(W_o [h_{t−1}, x_t] + b_o)          output gate
h_t = o_t ⊙ tanh(c_t)                      hidden state
```

```
        c_{t−1} ──────────(×)──────────(+)────────────► c_t     ← the "conveyor belt":
                           ▲            ▲                         mostly linear, no repeated
                          f_t          i_t ⊙ c̃_t                  matrix multiply
        h_{t−1} ──┬──► [σ f][σ i][tanh c̃][σ o]
          x_t ────┘                        │
                                           ▼
                                 h_t = o_t ⊙ tanh(c_t) ──────────► h_t
```

### 📐 Why gates solve the vanishing gradient

```
∂c_t/∂c_{t−1} = f_t          (plus small terms through the gates)
⇒ ∂c_T/∂c_t = Π f_k
```

If the forget gates stay near 1, the product stays near 1 — the gradient flows unattenuated across
hundreds of steps. Compare the vanilla RNN, where the factor is `W_hh·diag(1−h²)` with `|tanh'| ≤ 1`
*and* a matrix multiply every step. ⭐⭐⭐

The mechanism is the same as a residual connection: **an additive path with a multiplier near 1**.

⚠️ **Practical trick:** initialise the forget-gate bias to `+1` (or 2) so the gate starts open and
the network remembers by default. Interviewers like this detail.

**Parameter count:** 4 gates × `((d_h + d_x)·d_h + d_h)`, i.e. `4(d_h(d_h + d_x) + d_h)`.

---

## 4. GRU

```
z_t = σ(W_z [h_{t−1}, x_t])                 update gate (merges forget and input)
r_t = σ(W_r [h_{t−1}, x_t])                 reset gate
h̃_t = tanh(W [r_t ⊙ h_{t−1}, x_t])
h_t = (1 − z_t) ⊙ h_{t−1} + z_t ⊙ h̃_t       ← still an additive/interpolating update
```

| | LSTM | GRU |
|---|---|---|
| Gates | 3 | 2 |
| Separate cell state | yes | no |
| Parameters | ~33% more | fewer |
| Speed | slower | faster |
| Performance | comparable; LSTM sometimes better on very long sequences | comparable, better with less data |

The honest answer to "which is better": empirically comparable; try both, prefer GRU when data or
compute is limited.

---

## 5. Architectural add-ons

- **Bidirectional RNN:** run forwards and backwards and concatenate. ⚠️ Only valid when the whole
  sequence is available — not for streaming or autoregressive generation.
- **Stacked/deep RNN:** feed `h_t` of one layer as the input of the next; 2–4 layers is typical.
- **Encoder–decoder (seq2seq):** the encoder compresses the source into a context vector; the
  decoder generates the target. ⚠️ The **bottleneck** is that a single fixed vector must hold an
  entire sentence — performance collapses on long inputs.
- **Attention (Bahdanau/Luong, 2014–15)** was invented precisely to fix that bottleneck: the
  decoder computes a weighted sum over *all* encoder states at each output step. This is the direct
  ancestor of the transformer, and it makes a great narrative answer. ⭐⭐

```
seq2seq without attention        seq2seq with attention
 x₁ x₂ x₃ → [c] → y₁ y₂          x₁ x₂ x₃ → h₁ h₂ h₃
            ▲                                ╲│╱
     one fixed vector                    weighted sum per output step
     = the bottleneck                    = no bottleneck
```

- **Teacher forcing:** during training, feed the *true* previous token rather than the model's
  prediction. Fast and stable, but causes **exposure bias** — at inference the model sees its own
  (possibly wrong) outputs, a distribution it never trained on. Mitigations: scheduled sampling,
  or sequence-level training.

---

## 6. Why transformers replaced RNNs ⭐⭐⭐

| | RNN/LSTM | Transformer |
|---|---|---|
| Parallelism over sequence | **none** — step `t` needs step `t−1` | full — all positions at once ⭐ |
| Path length between two positions | `O(n)` | `O(1)` |
| Compute per layer | `O(n·d²)` | `O(n²·d)` |
| Long-range dependencies | hard (even with gates) | direct |
| Memory at inference | `O(1)` state | `O(n)` KV cache |
| Best for | streaming, tiny devices, very long sequences with linear-time need | essentially everything else |

The decisive factor was **training parallelism**: an RNN cannot use a GPU's full width because of
the sequential dependency, which caps how large a model you can train. Attention's quadratic cost
was an acceptable price.

⚠️ Note the honest trade-off: transformers are `O(n²)` in sequence length, so for very long
sequences RNN-like linear-time models have returned (S4, Mamba/state-space models, RWKV,
linear attention). Mentioning that shows current awareness. ⭐

---

## 7. Practical notes

- **Padding and masking:** batch variable-length sequences by padding, then use
  `pack_padded_sequence` (PyTorch) or an attention/loss mask so padding does not contribute.
  ⚠️ Forgetting to mask the loss is a very common bug.
- **Gradient clipping is mandatory** for RNNs — `clip_grad_norm_(params, 1.0)`.
- **Dropout in RNNs** should be applied to the inputs/outputs of layers, or use variational
  (same mask at every timestep) dropout; naive per-step dropout destroys the recurrent signal.
- **Classical alternatives** still worth naming for time series: ARIMA, exponential smoothing,
  Prophet, and gradient boosting on lag/rolling features — which frequently beats deep models on
  small tabular time series. ⭐

---

## Recall questions

1. Write the vanilla RNN equations and explain what weight sharing across time buys.
2. Derive why BPTT gradients vanish or explode, and why it is worse than in a feed-forward net.
3. Write all six LSTM equations.
4. Show `∂c_t/∂c_{t−1} = f_t` and explain why that solves the problem.
5. Why initialise the forget-gate bias to 1?
6. LSTM vs GRU — differences and when to pick each.
7. What is the seq2seq bottleneck, and how did attention fix it?
8. What is teacher forcing, and what problem does it create?
9. Give four reasons transformers replaced RNNs, and one reason RNN-like models are returning.
10. When is a bidirectional RNN invalid?
