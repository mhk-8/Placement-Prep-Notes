
# Finetuning, PEFT and Alignment ⭐⭐⭐

> **Core idea in 3 lines**
> 1. Adapting a pretrained model runs from cheapest to most expensive: prompt → RAG → PEFT/LoRA →
>    full finetune → pretrain. Always justify why you went as far as you did.
> 2. LoRA works because weight *updates* during finetuning are approximately low-rank, so you can
>    train two thin matrices instead of a big one.
> 3. Alignment (RLHF/DPO) is what turns a text predictor into an assistant; understanding the
>    reward model and why DPO removed it is a standard senior question.

---

## 1. The adaptation ladder ⭐⭐⭐

```
                          cost / data needed ─────────────────────►
 prompt engineering  →  few-shot  →  RAG  →  PEFT (LoRA)  →  full finetune  →  pretrain
  0 examples            k examples   docs    100s–10k        10k–1M           trillions
  minutes               minutes      hours   hours           days             months
```

**How to choose — the decision rule interviewers want:**

| Problem | Right tool |
|---|---|
| The model lacks *knowledge* (your documents, recent facts) | **RAG** — finetuning is a poor way to inject facts ⚠️ |
| The model lacks *behaviour/format/style/domain tone* | **finetuning (LoRA)** ⭐ |
| The task is simple and the model can already do it | prompt engineering + few-shot |
| You need lower latency/cost at fixed quality | distillation into a small finetuned model |
| Both knowledge and behaviour | RAG **and** LoRA together |

✅ Say this crisply: **"RAG for knowledge, finetuning for behaviour."** ⭐⭐⭐

---

## 2. Full finetuning and its problems

Update every parameter on your task data.

⚠️ Costs and risks:
- Memory: ~16 bytes/parameter with Adam ⇒ a 7B model needs >100 GB.
- One full copy of the model per task.
- **Catastrophic forgetting** — the model loses general capability. Mitigations: a low learning
  rate (`1e−5` range), few epochs (1–3), mixing in some general data, or freezing lower layers.
- Small datasets overfit quickly.

---

## 3. LoRA ⭐⭐⭐

**Hypothesis:** the *change* `ΔW` needed to adapt a pretrained model has low intrinsic rank.

```
W' = W₀ + ΔW = W₀ + B A ,      A ∈ R^{r×d},  B ∈ R^{d×r},  r ≪ d
W₀ is FROZEN;  only A and B are trained.
```

```
        x
        │
   ┌────┴─────┐
   │          │
 [W₀ frozen] [A]  (r × d)   ← init: A ~ N(0, σ²)
   │          │
   │         [B]  (d × r)   ← init: B = 0  ⇒  BA = 0 at step 0,
   │          │                so the model starts EXACTLY as the base model ⚠️⭐
   └────(+)───┘
        │ (scaled by α/r)
        ▼
```

📐 **Parameter saving.** A `d × d` weight has `d²` parameters; LoRA has `2dr`. For `d = 4096`,
`r = 8`: `16.7M → 65.5k`, a **256× reduction**. Across a 7B model this is typically ~0.1–1% of the
parameters trained. ⭐⭐

**Key details that get asked:**
- `B` is initialised to zero so training starts from the exact pretrained function — no random
  perturbation of a good model. ⭐
- The output is scaled by `α/r`, so changing `r` does not require retuning the learning rate; `α`
  is commonly set to `2r`.
- LoRA is usually applied to the attention projections (`q_proj`, `v_proj` at minimum; often all
  four plus the FFN matrices).
- **Zero inference latency**: `W₀ + BA` can be merged into a single matrix after training. ⭐
- You can keep many small adapters (a few MB each) for one shared base model and swap them per
  request — the reason LoRA dominates in production multi-tenant serving.
- Typical hyperparameters: `r = 8–64`, `α = 16–64`, dropout `0.05`, LR `1e−4` to `3e−4` (much
  higher than full finetuning).

**QLoRA ⭐⭐**: quantise the frozen base model to 4-bit (NF4 data type) and train LoRA adapters in
bf16 on top, with double quantisation and paged optimisers. This puts finetuning a 65B model on a
single 48 GB GPU. Gradients flow through the quantised weights; only the adapters are updated.

**Other PEFT methods:** prefix/prompt tuning (learn virtual tokens prepended to the input),
adapters (small bottleneck MLPs inserted in each block — Houlsby; they *do* add inference latency),
IA³ (learned rescaling vectors), BitFit (train only biases), DoRA (decomposes magnitude and
direction).

---

## 4. Instruction tuning (SFT)

Train on `(instruction, response)` pairs with the standard causal LM loss, **masked so the loss is
computed only on the response tokens**. ⚠️ Computing the loss on the prompt too is a common bug that
wastes capacity teaching the model to generate instructions.

Data quality beats quantity: LIMA showed 1 000 carefully curated examples can outperform 50 000
noisy ones. Chat templates (special role tokens) must match what the base model expects. ⭐

---

## 5. RLHF ⭐⭐⭐

```
 ┌──────────────┐    ┌─────────────────────┐    ┌──────────────────────┐
 │ 1. SFT model │ →  │ 2. Reward model      │ →  │ 3. PPO optimisation  │
 │              │    │    humans rank k      │    │    policy = SFT model│
 │              │    │    responses; train   │    │    maximise RM score │
 │              │    │    r_θ on pairs       │    │    − β·KL(π ‖ π_SFT) │
 └──────────────┘    └─────────────────────┘    └──────────────────────┘
```

📐 **Reward-model loss** (Bradley–Terry pairwise preference model):

```
L = − E_{(x, y_w, y_l)} [ log σ( r_θ(x, y_w) − r_θ(x, y_l) ) ]
```

i.e. maximise the score gap between the preferred (`y_w`) and rejected (`y_l`) response. Ranking is
used rather than absolute scoring because humans are far more consistent at comparisons. ⭐

**The PPO objective:**

```
maximise  E[ r_θ(x, y) ]  −  β · KL( π_RL(y|x) ‖ π_SFT(y|x) )
```

⚠️ **Why the KL penalty?** Without it the policy drifts to whatever exploits the reward model —
**reward hacking** — producing degenerate text that scores highly and reads terribly. The KL term
anchors the policy near the SFT model. Explaining this is a standard senior-level question. ⭐⭐⭐

---

## 6. DPO and the successors ⭐⭐

**Direct Preference Optimisation** removes the reward model and the RL loop entirely. Its insight:
the optimal policy for the KL-regularised reward objective has a closed form, which can be inverted
to express the reward in terms of the policy — turning preference learning into a simple
classification loss:

```
L_DPO = − E [ log σ( β log (π_θ(y_w|x)/π_ref(y_w|x))  −  β log (π_θ(y_l|x)/π_ref(y_l|x)) ) ]
```

| | RLHF/PPO | DPO |
|---|---|---|
| Reward model | separate, trained first | implicit |
| Training loop | RL, unstable, 4 models in memory | supervised, stable, 2 models |
| Compute | high | much lower |
| Quality | still strong, more controllable | comparable on most benchmarks ⭐ |

Related: **RLAIF/Constitutional AI** (AI feedback against a written set of principles instead of
human labels), **KTO**, **ORPO**, **GRPO** (used for reasoning models; a group-relative advantage
that removes the value network), and **RLVR** — RL against *verifiable* rewards (unit tests, exact
maths answers), which is how modern reasoning models are trained.

---

## 7. Distillation and quantisation ⭐

**Knowledge distillation:** train a small student on the large teacher's soft output distribution.

```
L = α · CE(student, hard labels) + (1−α) · T² · KL( student_T ‖ teacher_T )
```

The temperature `T` softens both distributions to expose the teacher's "dark knowledge" (its
relative confidence over wrong classes); the `T²` factor rescales the gradient, which would
otherwise shrink as `1/T²`. DistilBERT keeps ~97% of BERT's quality at 40% of the size.

**Quantisation:** fp16 → int8 → int4.

| Method | Note |
|---|---|
| PTQ (post-training) | no retraining; GPTQ, AWQ — 4-bit with small quality loss ⭐ |
| QAT (quantisation-aware training) | simulate quantisation during training; better, more expensive |
| GGUF / bitsandbytes | practical formats for CPU/consumer-GPU inference |

⚠️ Outlier activation channels are what makes LLM quantisation hard; LLM.int8() and AWQ handle them
by keeping a small fraction of channels in higher precision.

---

## 8. Common finetuning failure modes ⚠️

| Symptom | Cause |
|---|---|
| Model forgets general ability | LR too high, too many epochs — use 1–3 epochs at `1e−5`–`2e−4` (LoRA) |
| Output format ignored | chat template mismatch with the base model |
| Loss goes to zero, quality unchanged | loss computed on prompt tokens, or the dataset is trivial/duplicated |
| Great on eval, bad in production | eval set drawn from the same narrow distribution as training |
| Hallucinations unchanged | you needed RAG, not finetuning ⭐ |

---

## Recall questions

1. Give the adaptation ladder and the rule for choosing between RAG and finetuning.
2. Write the LoRA formulation and compute the parameter saving for `d = 4096`, `r = 8`.
3. Why is `B` initialised to zero, and what is `α/r` for?
4. Why does LoRA add no inference latency, and why does that matter in production?
5. What does QLoRA add on top of LoRA?
6. Why must the SFT loss be masked to the response tokens?
7. Write the reward-model loss and explain why ranking beats absolute scoring.
8. Why does PPO include a KL penalty against the SFT policy?
9. What does DPO remove, and what is the trade-off?
10. Write the distillation loss and explain the `T²` factor.
