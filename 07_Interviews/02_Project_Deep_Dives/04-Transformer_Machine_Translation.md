
# Project: Transformer for Machine Translation (German → English)

> **Course:** DA6401 Introduction to Deep Learning · **Instructor:** Prof. Ganapathy Krishnamurthy
> **Apr 2026** · **Track:** ML/DL · **Priority: ⭐⭐⭐ — your flagship ML project**

**Why this is strong:** "from scratch" is the differentiator, not the BLEU score. Implementing
multi-head attention, positional encoding and masked encoder-decoder stacks yourself means you can
answer gradient-level questions that candidates who called `nn.Transformer` cannot. In an era where
everyone has fine-tuned something, having built the thing is rarer than having used it.

**The corresponding obligation ⚠️:** claiming "from scratch" invites questions at the level of the
attention equation, the mask shapes, and why the scheduler exists. If you cannot answer those, the
claim actively damages you. Everything below is the preparation for that.

> Supporting theory: `../../05_AI_ML/05_NLP_and_LLMs/02-attention-and-transformers.md` has the full
> derivations. This file is the *interview presentation* of the same material.

---

## 1. The 20-second version

> "I implemented the original Transformer from scratch in PyTorch for German-English translation on
> Multi30k, reaching 34.8 BLEU with greedy decoding at about 65M parameters."

## 2. The 60-second version

> "I built the Vaswani et al. 2017 architecture from scratch — no `nn.Transformer` — for
> German-English translation on Multi30k. That meant implementing scaled dot-product attention,
> multi-head attention with the projection and concatenation, sinusoidal positional encoding, and
> the masked encoder-decoder stacks, including getting the causal and padding masks right, which is
> where most of the debugging time went.
>
> On the training side I used label smoothing and the Noam learning-rate schedule with warmup —
> warmup matters more than people expect, because Adam's second-moment estimates are unreliable in
> the first few hundred steps and the model diverges without it. I tracked loss, perplexity and
> sample translations in Weights & Biases.
>
> It reached 34.8 BLEU on the held-out test set with greedy decoding, at roughly 65 million
> parameters. The honest caveat is that Multi30k is small, clean and single-domain — it's image
> captions — so that number would not transfer to a harder corpus."

⭐ Note the built-in limitation at the end. It pre-empts the obvious challenge and makes the 34.8
more credible, not less.

---

## 3. The architecture, as you should draw it ⭐⭐⭐

```
 SOURCE (German)                              TARGET (English, shifted right)
      │                                                  │
  Embedding × √d_model                          Embedding × √d_model
      │ + sinusoidal PE                                  │ + sinusoidal PE
      ▼                                                  ▼
 ┌─────────────────┐                          ┌──────────────────────┐
 │  ENCODER × N    │                          │   DECODER × N        │
 │  ┌───────────┐  │                          │  ┌────────────────┐  │
 │  │ Multi-Head│  │                          │  │ MASKED Self-   │  │
 │  │ Self-Attn │  │                          │  │ Attention      │  │  ← causal mask
 │  └─────┬─────┘  │                          │  └────────┬───────┘  │
 │   Add & Norm    │                          │    Add & Norm        │
 │        │        │                          │         │            │
 │        │        │      ┌───────────────────┼──► ┌────▼────────┐   │
 │        │        │      │  K, V from encoder│    │ CROSS-Attn  │   │  ← Q from decoder
 │        │        │      │                   │    └────┬────────┘   │
 │        │        │      │                   │    Add & Norm        │
 │  ┌─────▼─────┐  │      │                   │  ┌──────▼─────────┐  │
 │  │ FFN 4d    │  │      │                   │  │ FFN 4d         │  │
 │  └─────┬─────┘  │      │                   │  └──────┬─────────┘  │
 │   Add & Norm ───┼──────┘                   │    Add & Norm        │
 └─────────────────┘                          └──────────┬───────────┘
                                                          ▼
                                                  Linear → Softmax → vocab
```

Practise drawing this in **under 60 seconds**. It is the single most-requested diagram in an ML
interview.

---

## 4. The five things you must be able to state exactly ⭐⭐⭐

### (a) Scaled dot-product attention
```
Attention(Q, K, V) = softmax( Q Kᵀ / √d_k ) V
```
**Why `√d_k`:** with unit-variance independent components, `Var(q·k) = d_k`, so the logits scale as
`√d_k`. Without rescaling, the softmax saturates into a near-one-hot distribution and its Jacobian
`diag(p) − ppᵀ` goes to zero — the gradient vanishes and training stalls.

### (b) Multi-head attention
```
head_i = Attention(Q W_i^Q, K W_i^K, V W_i^V)
MHA    = Concat(head_1 … head_h) W^O,    d_k = d_v = d_model / h
```
**Why multiple heads:** one softmax-weighted average captures one relationship at a time. Multiple
heads attend to different subspaces simultaneously — empirically some track syntactic dependency,
some coreference, some just the previous token. It is the attention analogue of having many filters
in a convolution layer.

### (c) The two masks ⚠️⭐⭐⭐
This is where the from-scratch debugging actually happens, so be precise:
```
PADDING MASK  : shape (B, 1, 1, S).  Zeroes attention to <pad> positions in BOTH encoder
                self-attention and cross-attention. Applied as −inf BEFORE the softmax so
                the remaining weights still sum to 1.

CAUSAL MASK   : shape (T, T), upper-triangular.  Used ONLY in decoder self-attention, so
                position i cannot attend to j > i.
                ⭐ This is what allows the whole target sequence to be trained in ONE
                  parallel forward pass while every position is still a valid next-token
                  prediction.

The decoder self-attention mask is the ELEMENT-WISE AND of both.
```
⚠️ **The classic bug:** applying the mask *after* the softmax instead of before. Then the weights no
longer sum to 1 over the allowed positions, and the model trains to a plausible-looking but wrong
objective. Having found and fixed this is a good "hardest bug" story if it was yours.

### (d) Sinusoidal positional encoding
```
PE(pos, 2i)   = sin( pos / 10000^(2i/d) )
PE(pos, 2i+1) = cos( pos / 10000^(2i/d) )
```
**Why needed at all:** attention is permutation-equivariant — it is a weighted sum, so shuffling the
input shuffles the output identically. Without positional information, "dog bites man" and "man
bites dog" are indistinguishable.
**Why sinusoidal rather than learned:** `PE(pos+k)` is a fixed linear function (a rotation) of
`PE(pos)`, so relative offsets are learnable, and it extrapolates to lengths unseen in training.
(Modern models use **RoPE**, which rotates Q and K so the dot product depends only on the relative
distance — a good thing to mention as awareness of what came after.) ⭐

### (e) Label smoothing and the Noam schedule
```
LABEL SMOOTHING (ε = 0.1): target becomes 1−ε for the true token and ε/(V−1) elsewhere.
   Prevents over-confident logits, improves calibration, and — counter-intuitively — usually
   improves BLEU while making perplexity look WORSE. ⭐ Know that trade-off; it gets asked.

NOAM SCHEDULE:  lr = d_model^(-0.5) · min( step^(-0.5), step · warmup^(-1.5) )
   Linear warmup, then inverse-square-root decay.
   WHY warmup: Adam's second-moment estimate v̂ is unreliable in the first few hundred steps
   (it is initialised at zero and heavily biased), and early attention gradients are large.
   A full learning rate immediately destabilises LayerNorm statistics and the model diverges.
```

---

## 5. Evaluation ⭐

```
BLEU = BP · exp( Σ w_n log p_n )          modified n-gram precision, n = 1..4
BP (brevity penalty) = 1 if c > r, else exp(1 − r/c)      ⚠️ penalises short output
```
**What BLEU does badly:** it is a surface n-gram overlap measure. It cannot see a correct paraphrase,
it correlates only moderately with human judgement, and it is not comparable across tokenisers or
across corpora. ⚠️ **"34.8 BLEU" is meaningless without naming the dataset**, which is why you
always say "34.8 on Multi30k".

**Greedy vs beam ⭐:** you used greedy. Beam search with width 4-5 would typically add a point or
two on a task like this. Volunteer that — it shows you know what you left on the table.
(Note the contrast with open-ended generation, where beam search is actively *bad* because
maximising sequence likelihood yields short, generic text. For translation, where there is
essentially one right answer, beam is appropriate.)

---

## 6. Anticipated follow-ups ⭐⭐⭐

<details><summary>"Derive the gradient of softmax + cross-entropy."</summary>

The softmax Jacobian is `∂p_i/∂z_j = p_i(δ_ij − p_j)`. Combined with `L = −Σ y_i log p_i`, the
terms collapse to
```
∂L/∂z_j = p_j − y_j
```
That clean form is exactly why cross-entropy is paired with softmax rather than MSE: with MSE the
gradient carries a `σ'(z)` factor that vanishes when the unit is saturated — i.e. precisely when
the model is confidently wrong. Full derivation in
`../../05_AI_ML/01_Math_Foundations/02-matrix-calculus.md`.
</details>

<details><summary>"Where do most of the parameters live?"</summary>

The FFN, not attention. Per block: attention is `4·d_model²` (Q, K, V, O projections) and the FFN
is `8·d_model²` (two layers with a 4× hidden dimension). So roughly **two thirds of the
non-embedding parameters are in the FFN**. Plus the embedding/output matrix, which at
`V × d_model` is substantial for a large vocabulary and is often weight-tied.
</details>

<details><summary>"65M parameters — where did that come from?"</summary>

Be ready to break it down roughly: `N` blocks × `12·d_model²` for encoder, similar for decoder plus
cross-attention, plus embeddings `V × d_model` for both languages and the output projection. If you
tied the embeddings, say so. If you do not remember the exact configuration, say that and give the
structure — do not invent `d_model = 512, N = 6` unless it is true.
</details>

<details><summary>"Why not just use a pretrained model?"</summary>

For the task, you would — mBART, Marian or a fine-tuned multilingual model would beat 34.8
comfortably. The point of the project was to implement and understand the architecture. That is the
right answer, said without apology: *"the goal was the mechanism, not the leaderboard."* ⭐
</details>

<details><summary>"What is the computational complexity of attention?"</summary>

`O(n²·d)` time and `O(n²)` memory for the score matrix, versus `O(n·d²)` for the FFN. That
quadratic term in sequence length is the central constraint of the whole field. FlashAttention
reduces the *memory traffic* and peak memory to `O(n)` by tiling — but ⚠️ it does **not** change
the `O(n²)` arithmetic, and saying it makes attention linear is a known trap.
</details>

<details><summary>"How would you scale this to a much longer context?"</summary>

FlashAttention for the memory, sparse or windowed attention (Longformer, BigBird), linear attention
by dropping the softmax so `(QKᵀ)V` becomes `Q(KᵀV)`, or a state-space model like Mamba. For
inference specifically, the KV cache becomes the binding constraint, which is why GQA/MQA exist.
</details>

<details><summary>"What was the hardest bug?"</summary>

Have a real answer ready. Candidates for this project: mask shape broadcasting; applying the mask
after rather than before the softmax; forgetting the `√d_model` scaling on the embeddings; the
target shift (teacher forcing input is `y[:-1]`, labels are `y[1:]`) being off by one; or training
loss falling while sample translations stayed garbage, which points at a decoding bug rather than a
training one. ⭐ **Pick the one that actually happened** and tell it with the symptom, the
hypothesis, and how you isolated it.
</details>

<details><summary>"What is teacher forcing and what problem does it cause?"</summary>

During training you feed the *true* previous token rather than the model's own prediction, which is
fast and stable. At inference the model consumes its own outputs — a distribution it never trained
on — which is **exposure bias**: one early mistake compounds. Mitigations: scheduled sampling, or
sequence-level training objectives.
</details>

---

## 7. Limitations to state proactively ⭐⭐

```
- Multi30k is small (~29k sentence pairs), clean, and single-domain (image captions).
  34.8 BLEU there does not transfer to WMT-scale or to a more distant language pair.
- Greedy decoding only; beam search would likely add 1-2 BLEU.
- No pretrained initialisation, no back-translation, no data augmentation.
- BLEU is a weak proxy — no human evaluation, no COMET/chrF cross-check.
- Post-LN (the original formulation), which needs careful warmup; modern practice is Pre-LN,
  which trains far more stably. ⭐ Mentioning this shows currency.
```

---

## 8. Connecting it to LLMs ⭐⭐ (use this for any LLM-adjacent role)

> "This is the encoder-decoder form from the original paper. Modern LLMs are decoder-only — the
> same block, causal mask only, no cross-attention — plus a set of changes that all came later:
> RMSNorm and pre-norm instead of post-LN LayerNorm, RoPE instead of sinusoidal positional
> encoding, SwiGLU instead of ReLU/GELU in the FFN, and GQA to shrink the KV cache at inference.
> Having implemented the original makes those substitutions easy to reason about, because each one
> is replacing a component I had to write myself."

⭐ That paragraph turns a 2017 course project into evidence of 2026 relevance. Have it ready.

---

## 9. The 30-second refresh

```
□ Attention = softmax(QKᵀ/√d_k)V ; √d_k because Var(q·k) = d_k and the softmax saturates
□ Multi-head: h subspaces, d_k = d_model/h, concat then W^O
□ Padding mask (both) + causal mask (decoder self-attn only), applied as −inf BEFORE softmax
□ Sinusoidal PE because attention is permutation-equivariant; RoPE is the modern successor
□ Label smoothing 0.1 (helps BLEU, hurts perplexity); Noam warmup because Adam's v̂ is unreliable early
□ 34.8 BLEU on Multi30k, greedy, ~65M params
□ Two thirds of parameters are in the FFN, not attention
□ Limitation to volunteer: small clean corpus; greedy only; post-LN
```
