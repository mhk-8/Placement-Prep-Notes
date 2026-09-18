
# Pretraining: BERT, GPT and Modern LLMs ⭐⭐⭐

> **Core idea in 3 lines**
> 1. Pretraining creates labels out of raw text — masked-token prediction or next-token
>    prediction — which is why it scales to trillions of tokens.
> 2. BERT (bidirectional, encoder) is built to *understand*; GPT (causal, decoder) is built to
>    *generate*; the second family won because generation subsumes understanding via prompting.
> 3. The pipeline pretrain → SFT → preference optimisation is the shape of every modern chat model.

---

## 1. Self-supervised pretraining

```
supervised      : needs human labels                        → scarce, expensive
self-supervised : the label comes from the data itself      → unlimited ⭐
```

The two dominant objectives:

```
Masked language modelling (MLM)     "The [MASK] sat on the mat."        → cat
  sees both directions; good representations; not directly generative

Causal language modelling (CLM)     "The cat sat on the" → "mat"
  sees only the left context; directly generative; every token is a training signal ⭐
```

⚠️ A subtle efficiency point worth making: BERT computes a loss on only the ~15% masked positions,
while GPT gets a loss at **every** position — one of the reasons causal LM scales better per token.

---

## 2. BERT ⭐⭐

Encoder-only, bidirectional. Two pretraining tasks:

**Masked Language Modelling.** Choose 15% of tokens; of those:

```
80% → replaced with [MASK]
10% → replaced with a random token
10% → left unchanged
```

📐 **Why the 80/10/10 split** (a classic question): `[MASK]` never appears at finetuning time, so if
the model only ever saw `[MASK]`, there would be a train/inference mismatch. The random and
unchanged cases force the model to build a good representation of *every* token, not just masked
ones. ⭐⭐

**Next Sentence Prediction** — classify whether sentence B follows sentence A. ⚠️ Later shown to be
nearly useless (RoBERTa dropped it and improved); it is a good "what did the follow-up work change?"
answer.

**Input format:**

```
[CLS] tok tok tok [SEP] tok tok [SEP]
  │
  └─ the pooled representation used for classification
embeddings = token + segment (A/B) + learned position
```

**Finetuning BERT:** add a small head and train end-to-end.

```
sentence classification → [CLS] → linear head
token classification (NER) → per-token → linear head
question answering (SQuAD) → predict start and end token positions ⭐
sentence pair (NLI) → [CLS] over the concatenated pair
```

**Successors:** RoBERTa (more data, longer training, dynamic masking, no NSP), ALBERT (parameter
sharing, factorised embeddings), DeBERTa (disentangled content/position attention — still a strong
encoder), ELECTRA (replaced-token detection — a discriminator over a generator's substitutions,
giving a loss at *every* position and much better sample efficiency ⭐), DistilBERT (40% smaller,
60% faster, ~97% of the quality, via distillation).

---

## 3. GPT and the decoder-only family ⭐⭐⭐

Causal LM: `maximise Σ_t log p(x_t | x_{<t})`.

```
GPT-1 (2018)   117M   pretrain + task-specific finetuning
GPT-2 (2019)   1.5B   zero-shot task transfer: "language models are unsupervised multitask learners"
GPT-3 (2020)   175B   in-context / few-shot learning — no weight updates ⭐
InstructGPT    —      SFT + RLHF; the alignment step that made chat usable
GPT-4 / modern —      multimodal, tool use, much longer context; MoE widely suspected/used
LLaMA / Mistral / Qwen / DeepSeek — strong open-weight models; RoPE, RMSNorm, SwiGLU, GQA
```

**In-context learning ⭐⭐** is the property that changed the field: the model performs a new task
from examples in the prompt, with **no gradient updates**. Zero-shot (instruction only), few-shot
(k examples). The mechanistic explanation is still open research; "induction heads" that copy and
complete patterns are the best-understood ingredient.

### Architecture choices in modern LLMs ⭐ (name these; they signal currency)

| Component | Modern choice | Why |
|---|---|---|
| Normalisation | **RMSNorm**, pre-norm | cheaper (no mean subtraction), stable training |
| Positional encoding | **RoPE** | relative, KV-cache friendly, extensible context |
| FFN activation | **SwiGLU** | better quality per parameter than GELU (uses 3 matrices, hidden ≈ 8/3·d) |
| Attention | **GQA**, FlashAttention | smaller KV cache, faster |
| Bias terms | usually removed | negligible quality cost, simpler and faster |
| Sparsity | **Mixture of Experts** | route each token to `k` of `N` expert FFNs ⇒ many more parameters at constant FLOPs ⭐ |

---

## 4. The modern training pipeline ⭐⭐⭐

```
 ┌──────────────────────────────────────────────────────────────────┐
 │ 1. PRETRAINING                                                   │
 │    trillions of tokens of web/code/books, next-token prediction   │
 │    months on thousands of GPUs; produces a "base model"           │
 │    → knows language and facts, but does not follow instructions   │
 └───────────────────────────┬──────────────────────────────────────┘
                             ▼
 ┌──────────────────────────────────────────────────────────────────┐
 │ 2. SUPERVISED FINETUNING (SFT / instruction tuning)               │
 │    10k–1M curated (instruction, response) pairs                   │
 │    → now answers questions instead of continuing text             │
 └───────────────────────────┬──────────────────────────────────────┘
                             ▼
 ┌──────────────────────────────────────────────────────────────────┐
 │ 3. PREFERENCE OPTIMISATION (RLHF / DPO)                           │
 │    humans rank responses → reward model → PPO, or DPO directly    │
 │    → helpful, harmless, honest; better style; refusals            │
 └───────────────────────────┬──────────────────────────────────────┘
                             ▼
 ┌──────────────────────────────────────────────────────────────────┐
 │ 4. (optional) REASONING / RL ON VERIFIABLE REWARDS                │
 │    long chain-of-thought trained with RL against checkable answers │
 │    (maths, code) → "reasoning models"                             │
 └──────────────────────────────────────────────────────────────────┘
```

Being able to name all four stages and what each adds is one of the highest-value things you can
say in an LLM interview. ⭐⭐⭐

---

## 5. Data and scale

**Data pipeline:** crawl → deduplicate (exact and near-duplicate via MinHash) → quality filter
(classifiers, heuristics) → remove PII and toxic content → **decontaminate against benchmarks** →
mix domains (web, code, books, maths) with tuned proportions.

⚠️ **Benchmark contamination** is a real and frequently-raised concern: if the test set leaked into
pretraining, reported scores are meaningless. Good answers mention decontamination and held-out or
freshly-written evaluations.

**Scaling laws.** Loss follows a power law in parameters `N`, data `D` and compute `C`.
**Chinchilla**: for a fixed compute budget, `N` and `D` should scale *equally* — about **20 tokens
per parameter**. Kaplan's earlier laws over-weighted parameters, which is why GPT-3 (175B on 300B
tokens) was under-trained. In practice models are now trained far past compute-optimal because
inference cost, not training cost, dominates over a deployed model's lifetime. ⭐⭐

**Emergent abilities:** some capabilities appear abruptly with scale (multi-step arithmetic,
chain-of-thought). ⚠️ A well-known critique (Schaeffer et al.) argues much of the "emergence" is an
artefact of discontinuous metrics like exact-match; with continuous metrics the improvement is
smooth. Mentioning both sides is the mature answer.

---

## 6. BERT vs GPT — the comparison table ⭐⭐⭐

| | BERT | GPT |
|---|---|---|
| Architecture | encoder only | decoder only |
| Attention | bidirectional | causal (masked) |
| Objective | MLM (+NSP) | next-token prediction |
| Loss signal | ~15% of tokens | every token |
| Generation | not natively | native |
| Adaptation | finetune with a head | prompt, or finetune |
| Typical size | 110M–340M | 1B–1T+ |
| Best at | classification, NER, extractive QA, embeddings | generation, few-shot, everything via prompting |
| Still used for | cheap high-throughput classification and retrieval encoders ⭐ | general-purpose systems |

⚠️ Do not say "BERT is obsolete". For a high-volume classification or retrieval service, a 110M
encoder is orders of magnitude cheaper per request than an LLM and often more accurate after
finetuning. That practical judgement is exactly what a senior interviewer wants to hear. ⭐⭐

---

## Recall questions

1. Why is self-supervised pretraining the thing that unlocked scale?
2. Explain BERT's 80/10/10 masking and the problem it solves.
3. What happened to NSP, and what replaced it?
4. Why does causal LM get more learning signal per token than MLM?
5. What is in-context learning, and what makes it remarkable?
6. Name four architecture choices in modern LLMs and justify each.
7. Draw the four-stage training pipeline and say what each stage adds.
8. State the Chinchilla result and why models are now trained past it.
9. What is benchmark contamination and how is it mitigated?
10. When would you still choose BERT over an LLM in production?
