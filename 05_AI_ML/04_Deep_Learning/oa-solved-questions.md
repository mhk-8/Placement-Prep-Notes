
# Deep Learning — Solved OA Questions

32 questions: arithmetic (shapes, parameters, FLOPs), theory, and debugging scenarios.

---

## Set A — Shapes and parameter counts (OAs love these)

**Q1.** Input `32×32×3`, conv with 16 filters of `5×5`, stride 1, padding 0. Output shape and
parameter count?

<details><summary>Answer</summary>

`O = (32 − 5 + 0)/1 + 1 = 28` ⇒ output `28×28×16`.
Params `= 5·5·3·16 + 16 = 1200 + 16 = 1216`.
</details>

**Q2.** Input `224×224`, `K=7`, `S=2`, `P=3`. Output size?

<details><summary>Answer</summary>

`(224 − 7 + 6)/2 + 1 = 111 + 1 = 112`.
</details>

**Q3.** What padding gives "same" output size for `K=5`, `S=1`?

<details><summary>Answer</summary>

`P = (K−1)/2 = 2`. Odd kernels are used so this is an integer and symmetric.
</details>

**Q4.** Max-pool `2×2` stride 2 applied to `28×28×16`. Output shape and parameters?

<details><summary>Answer</summary>

`14×14×16`; **zero** parameters.
</details>

**Q5.** Flatten `7×7×512` into a fully-connected layer with 4096 units. Parameters?

<details><summary>Answer</summary>

`7·7·512 = 25 088` inputs; `25 088 · 4096 + 4096 ≈ 102.8 M`. This single layer is most of VGG-16's
parameters — the motivation for global average pooling.
</details>

**Q6.** Receptive field of three stacked `3×3` stride-1 convolutions?

<details><summary>Answer</summary>

`1 + 3(3−1) = 7`. Same as one `7×7` but with `27C²` instead of `49C²` parameters and two extra
non-linearities.
</details>

**Q7.** LSTM with input size 100 and hidden size 256. Parameter count?

<details><summary>Answer</summary>

`4 · [ (256 + 100)·256 + 256 ] = 4 · (91 136 + 256) = 4 · 91 392 = 365 568`.
</details>

**Q8.** A `3×3` conv, 64 → 128 channels, on a `56×56` map. Parameters and approximate FLOPs?

<details><summary>Answer</summary>

Params `= 3·3·64·128 + 128 = 73 856`.
FLOPs `≈ 2 · 3·3·64·128 · 56·56 ≈ 462 M`.
</details>

---

## Set B — Backprop and activations

**Q9.** Two linear layers with no activation. What function class does this represent?

<details><summary>Answer</summary>

Exactly the linear functions: `W₂(W₁x + b₁) + b₂ = W'x + b'`. Depth without non-linearity adds
nothing.
</details>

**Q10.** `∂L/∂z` for softmax + cross-entropy?

<details><summary>Answer</summary>

`p − y`. The softmax Jacobian cancels against the log.
</details>

**Q11.** Give BP2, the backward recursion.

<details><summary>Answer</summary>

`δ^{(l)} = (W^{(l+1)ᵀ} δ^{(l+1)}) ⊙ g'(z^{(l)})`.
</details>

**Q12.** Maximum value of the sigmoid derivative, and the consequence for a 10-layer network?

<details><summary>Answer</summary>

`0.25` at `z = 0`. Ten layers multiply by at most `0.25¹⁰ ≈ 9.5×10⁻⁷` — vanishing gradients.
</details>

**Q13.** All weights initialised to zero. What happens?

<details><summary>Answer</summary>

Every unit in a layer computes the same output and receives the same gradient, so they remain
identical — symmetry is never broken and the layer has the capacity of a single unit. Biases may be
zero.
</details>

**Q14.** He initialisation variance, and why the 2?

<details><summary>Answer</summary>

`Var(w) = 2/n_in`. ReLU zeroes roughly half the inputs, halving the variance; the factor 2
compensates so activation variance is preserved across layers.
</details>

**Q15.** A network with ReLU has 95% zero activations in one layer. Diagnosis?

<details><summary>Answer</summary>

Dying ReLU — units pushed permanently negative, usually by too high a learning rate or large
negative biases. Fix with LeakyReLU/ELU/GELU, a lower LR, He init, or batch norm.
</details>

**Q16.** Why residual connections?

<details><summary>Answer</summary>

`y = x + F(x)` ⇒ `∂y/∂x = I + ∂F/∂x`, so there is always a gradient path multiplied by 1. It also
lets a block learn the identity by driving `F → 0`, so extra depth cannot hurt.
</details>

---

## Set C — Normalisation and regularisation

**Q17.** BatchNorm vs LayerNorm — what does each normalise over?

<details><summary>Answer</summary>

BN: each feature/channel across the batch. LN: each sample across its features. LN is
batch-size-independent and identical at train and inference, which is why transformers use it.
</details>

**Q18.** What does BatchNorm use at inference, and what bug follows?

<details><summary>Answer</summary>

Running (EMA) mean and variance. Forgetting `model.eval()` makes inference use batch statistics,
so predictions depend on the batch composition — a classic production bug.
</details>

**Q19.** Is "reducing internal covariate shift" the accepted explanation for BN?

<details><summary>Answer</summary>

No — later work (Santurkar et al.) showed the benefit comes from a smoother loss landscape
allowing higher learning rates; the ICS story has been largely discredited.
</details>

**Q20.** Dropout `p = 0.5`. What happens at train and at test time?

<details><summary>Answer</summary>

Train: each unit is zeroed with probability 0.5 and the survivors are scaled by `1/(1−p) = 2`
(inverted dropout). Test: dropout is off and nothing is rescaled.
</details>

**Q21.** Why exclude biases and LayerNorm parameters from weight decay?

<details><summary>Answer</summary>

They have few parameters, do not contribute to over-fitting capacity in the same way, and shrinking
LN gain/bias or the output bias distorts the layer's normalisation and offsets for no
regularisation benefit.
</details>

**Q22.** What is label smoothing and what does it improve?

<details><summary>Answer</summary>

Replacing the one-hot target with `1 − ε` and `ε/(K−1)`. It prevents over-confident logits,
improves calibration and usually slightly improves accuracy.
</details>

---

## Set D — Optimisation and training

**Q23.** What should the initial loss be for a balanced 10-class classification problem?

<details><summary>Answer</summary>

`ln 10 ≈ 2.303`. A very different value at step 0 indicates a wiring bug (wrong reduction, logits
vs probabilities, label mismatch). ⭐
</details>

**Q24.** Loss becomes NaN after a few steps. Three checks?

<details><summary>Answer</summary>

Learning rate too high (divergence); `log(0)` or a division by zero in the loss (use the fused
logits version); fp16 overflow with a broken loss scaler; unnormalised inputs; missing gradient
clipping.
</details>

**Q25.** Why do transformers need learning-rate warmup?

<details><summary>Answer</summary>

Adam's second-moment estimate is unreliable in the first steps, and early attention gradients are
large; a full LR immediately destabilises training. Warmup ramps up while the moment estimates
settle. Pre-LN needs much less than post-LN.
</details>

**Q26.** You quadruple the batch size. What do you do to the learning rate?

<details><summary>Answer</summary>

Multiply it by 4 (linear scaling rule), with warmup. Some use `√4 = 2` for Adam.
</details>

**Q27.** With gradient accumulation over 8 steps, what must you do to the loss?

<details><summary>Answer</summary>

Divide it by 8 before `backward()` (or divide the accumulated gradients), otherwise the effective
learning rate is 8× too large.
</details>

**Q28.** Estimate the memory to train a 1B-parameter model with Adam in mixed precision.

<details><summary>Answer</summary>

Roughly 16 bytes/parameter (fp32 master weights 4, gradients 4, Adam `m` 4, `v` 4) ⇒ ~16 GB before
activations. Cut it with bf16, 8-bit optimisers, ZeRO/FSDP sharding, gradient checkpointing, or
LoRA.
</details>

**Q29.** fp16 vs bf16?

<details><summary>Answer</summary>

Both are 2 bytes. fp16 has more mantissa but a narrow exponent range, so gradients underflow —
hence loss scaling. bf16 keeps fp32's exponent range with less precision, so it needs no loss
scaling; it is the modern default on A100/H100.
</details>

---

## Set E — Sequences

**Q30.** Why do vanilla RNNs fail on long sequences?

<details><summary>Answer</summary>

BPTT multiplies `diag(1−h²)·W_hh` once per timestep, so the gradient scales like `σ_max^{T}` —
vanishing if `σ_max < 1` and exploding if `> 1`. It is the *same* matrix each step, so there is no
cancellation.
</details>

**Q31.** Which LSTM quantity provides the gradient highway, and what is `∂c_t/∂c_{t−1}`?

<details><summary>Answer</summary>

The cell state; `∂c_t/∂c_{t−1} = f_t`. With forget gates near 1 the product stays near 1 across
many steps. Same principle as a residual connection.
</details>

**Q32.** Give the complexity per layer and the maximum path length for an RNN and a transformer.

<details><summary>Answer</summary>

RNN: `O(n·d²)` compute, path length `O(n)`, no parallelism over the sequence.
Transformer: `O(n²·d)` compute, path length `O(1)`, fully parallel over positions. Parallelism is
why transformers won despite the quadratic cost.
</details>

---

## Scoring

| Correct | Read as |
|---|---|
| 28–32 | Deep-learning fundamentals are solid |
| 21–27 | Redo the arithmetic questions until they are automatic |
| 14–20 | Reread `01`–`05` of this folder |
| < 14 | Two days here; nearly every ML/AI interview starts with this material |
