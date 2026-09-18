
# Training Deep Networks: Optimisation in Practice ⭐⭐

> **Core idea in 3 lines**
> 1. The optimiser, learning rate, schedule and batch size are one coupled system — changing any
>    one forces you to change the others.
> 2. The learning rate is the single most important hyperparameter; everything else is
>    second-order.
> 3. Most "my model doesn't train" problems are bugs, not optimisation problems, and there is a
>    fixed order for finding out which.

(The mathematics of SGD, momentum, Adam and schedules is derived in
`01_Math_Foundations/03-optimization.md`. This file is the practitioner's view.)

---

## 1. The training loop

```python
for epoch in range(E):
    model.train()
    for xb, yb in train_loader:
        xb, yb = xb.to(dev), yb.to(dev)
        optimizer.zero_grad(set_to_none=True)     # ⚠️ gradients accumulate otherwise
        with torch.autocast("cuda", dtype=torch.bfloat16):
            loss = criterion(model(xb), yb)       # criterion takes LOGITS
        loss.backward()
        torch.nn.utils.clip_grad_norm_(model.parameters(), 1.0)
        optimizer.step()
        scheduler.step()                          # per-step for warmup/cosine
    model.eval()
    with torch.no_grad():
        validate(...)
```

⚠️ The five bugs hidden in a loop like this when written carelessly: missing `zero_grad`, missing
`model.eval()`, computing validation without `no_grad`, passing probabilities to a loss that
expects logits, and stepping the scheduler per epoch when it was configured per step.

---

## 2. Choosing the optimiser ⭐⭐

| Optimiser | When |
|---|---|
| **SGD + momentum (0.9) + weight decay** | CNNs / vision; best final accuracy when you can tune |
| **AdamW** | transformers, NLP, sparse features, RL, anything you cannot tune much — the default ⭐ |
| Adam | same but with the coupled (incorrect) weight decay — prefer AdamW |
| RMSProp | RNNs, older RL work |
| LAMB / LARS | very large batch (32k+) pretraining |
| Adafactor / 8-bit Adam | memory-constrained LLM training (Adam stores 2 extra tensors per parameter ⚠️) |

⚠️ **Memory arithmetic worth knowing:** Adam keeps `m` and `v` per parameter. In fp32 that is
`4 bytes × 3` for parameters + optimiser states, plus gradients — roughly **16 bytes per
parameter** for mixed-precision training with fp32 master weights. A 7B model therefore needs
~112 GB just for weights, gradients and optimiser states, before activations. This is the
arithmetic behind ZeRO, LoRA and 8-bit optimisers. ⭐⭐

**"Adam always beats SGD" is false** — well-tuned SGD+momentum still wins on many vision
benchmarks, and Adam's adaptivity can hurt generalisation. Adam wins when gradients are sparse or
scales differ wildly across parameters, which is exactly the transformer setting.

---

## 3. Learning rate ⭐⭐⭐

**Finding one — the LR range test:** train for a few hundred steps while increasing the LR
exponentially from `1e-7` to `1`, and plot loss against LR.

```
loss
 │‾‾‾‾╲
 │     ╲          ← pick an LR here, roughly one order below the minimum
 │      ╲___   ╱
 │          ╲_╱   ← loss explodes
 └──────────────────► log(LR)
```

Sensible starting points: `3e-4` for AdamW on transformers, `1e-3` for AdamW on small nets,
`0.1` for SGD+momentum on CNNs (with cosine decay).

**Schedules**

| Schedule | Shape | Use |
|---|---|---|
| Constant | flat | debugging only |
| Step decay | ÷10 at fixed epochs | classic CNN recipes |
| **Cosine with warmup** | linear ramp then cosine to ~0 | the modern default for transformers ⭐ |
| One-cycle | up then down, momentum inverse | fast convergence on small budgets |
| ReduceLROnPlateau | ÷ on validation stall | when you cannot plan the horizon |
| Inverse-sqrt | `1/√step` after warmup | original transformer paper |

**Warmup** is not optional for transformers: Adam's second-moment estimate is unreliable in the
first steps and early attention gradients are large, so a full LR immediately destabilises the
model. Typical warmup is 1–10% of total steps. ⭐⭐

---

## 4. Batch size ⭐

```
small batch (16–64)   noisier gradients → implicit regularisation, often better generalisation,
                      poor GPU utilisation
large batch (1k–32k)  smoother gradients, fast wall-clock, needs LR scaling + warmup,
                      risk of converging to sharp minima
```

**Linear scaling rule:** multiply batch size by `k` ⇒ multiply LR by `k`, with warmup (Goyal et
al.). Some prefer `√k` for Adam.

**Gradient accumulation** simulates a large batch on small memory:

```python
for i, (xb, yb) in enumerate(loader):
    loss = criterion(model(xb), yb) / ACC_STEPS      # ⚠️ divide, or the effective LR is ACC× too big
    loss.backward()
    if (i + 1) % ACC_STEPS == 0:
        optimizer.step(); optimizer.zero_grad(set_to_none=True)
```

---

## 5. Mixed precision and memory ⭐

| Precision | Notes |
|---|---|
| fp32 | baseline, 4 bytes |
| **fp16** | 2 bytes; narrow range ⇒ needs **loss scaling** to stop gradients underflowing to 0 ⚠️ |
| **bf16** | 2 bytes; same exponent range as fp32 ⇒ no loss scaling needed; the modern default on A100/H100 ⭐ |
| fp8 / int8 | inference and some training; needs calibration |

**Memory-saving techniques, in the order you would reach for them:**

```
1. mixed precision (bf16)                    ~2× activations
2. gradient accumulation                     decouples batch size from memory
3. gradient checkpointing                    ~√L activation memory, ~30% more compute
4. smaller/8-bit optimiser states            Adafactor, bitsandbytes 8-bit Adam
5. ZeRO / FSDP sharding                      shard optimiser state, gradients, then parameters
6. LoRA / parameter-efficient finetuning     train ~0.1% of the parameters
7. offload to CPU/NVMe                       last resort, slow
```

**Distributed training vocabulary ⭐**

```
Data parallel      : replicate the model, split the batch, all-reduce the gradients
                     (DDP — the default; ZeRO/FSDP shard states to save memory)
Tensor parallel    : split individual matrices across GPUs (within a layer)
Pipeline parallel  : split layers across GPUs, micro-batch to fill the "bubble"
Expert parallel    : route tokens to different expert FFNs (MoE)
```

---

## 6. Regularisation choices during training

Covered in `03_Classical_ML/03-regularization.md`; the deep-learning defaults are: weight decay
`0.01–0.1` with AdamW (⚠️ exclude biases and LayerNorm parameters from decay), dropout `0.1` in
transformers and `0.5` in older MLP heads, label smoothing `0.1` for classification, early stopping
on validation, plus augmentation appropriate to the modality.

**EMA of weights** (keep an exponential moving average of the parameters and evaluate with it)
often buys a free fraction of a point and is standard in diffusion and detection models.

---

## 7. Hyperparameter search ⭐

| Method | Notes |
|---|---|
| Grid search | exponential in dimensions; wasteful |
| **Random search** | better than grid for the same budget — most hyperparameters do not matter, and random sampling covers the important axes more finely (Bergstra & Bengio) ⭐ |
| Bayesian optimisation (TPE/GP) | sample-efficient; Optuna, Hyperopt |
| Hyperband / ASHA | early-stop bad trials; best wall-clock efficiency |
| Population-based training | evolves schedules during training |

Search LR on a **log** scale, always. Tune in this order: LR → batch size/schedule → regularisation
→ architecture size.

---

## 8. Reproducibility and experiment hygiene ⭐

```python
torch.manual_seed(s); np.random.seed(s); random.seed(s)
torch.backends.cudnn.deterministic = True    # slower
```

⚠️ Even seeded, GPU reductions are non-deterministic in order, so exact bit-reproducibility needs
`torch.use_deterministic_algorithms(True)` and often costs speed. Log: git commit, config, data
version, metrics per step, and the environment (MLflow / Weights & Biases).

---

## 9. Debugging protocol ⭐⭐⭐ (say this verbatim in an interview)

```
1. OVERFIT 10 EXAMPLES.  If the model cannot reach ~0 loss on a tiny batch, stop —
   there is a bug in the data, the loss, or the forward pass.
2. Check shapes and label alignment; visualise a few inputs with their labels.
3. Verify the loss at initialisation:  for K balanced classes it should be ≈ ln K
   (e.g. 2.303 for 10 classes).  A very different value means a wiring bug. ⭐
4. LR range test.
5. Per-layer gradient norms; dead-unit fraction.
6. Turn OFF all regularisation, get it to overfit, then add regularisation back.
7. Only now scale up the data and the model.
```

That "loss should be `ln K` at init" check is a detail interviewers notice.

---

## Recall questions

1. Write a correct training loop and name five bugs that a careless version would contain.
2. Why AdamW rather than Adam?
3. Estimate the memory needed to train a 7B-parameter model with Adam, and name three ways to cut it.
4. How do you find a learning rate empirically?
5. Why do transformers need warmup?
6. State the linear scaling rule, and what gradient accumulation does (including the `/ACC` detail).
7. fp16 vs bf16 — what problem does loss scaling solve and why does bf16 avoid it?
8. Distinguish data, tensor and pipeline parallelism.
9. Why is random search better than grid search?
10. Give the full debugging protocol for a model that will not train.
