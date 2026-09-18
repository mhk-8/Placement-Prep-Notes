
# Attention and the Transformer ⭐⭐⭐

> **Core idea in 3 lines**
> 1. Attention lets every position look directly at every other position and take a weighted
>    average — a learned, content-based lookup.
> 2. The transformer is attention plus a position-wise MLP, wrapped in residual connections and
>    layer norm, repeated `N` times.
> 3. This is *the* most-asked topic in modern ML interviews. You must be able to write the
>    attention equation, explain every term including the `√d_k`, and draw the block.

---

## 1. Scaled dot-product attention ⭐⭐⭐

```
Attention(Q, K, V) = softmax( Q Kᵀ / √d_k ) V
```

Shapes: `Q` is `(n, d_k)`, `K` is `(m, d_k)`, `V` is `(m, d_v)`; the output is `(n, d_v)`.

**The database analogy — say this first.** Each position emits a **query** ("what am I looking
for?"), and every position offers a **key** ("what do I contain?") and a **value** ("what I will
contribute"). The dot product `q·k` scores how well a key matches the query; softmax turns the
scores into weights summing to 1; the output is the weighted average of the values. It is a soft,
differentiable dictionary lookup. ⭐⭐⭐

```
 token i's query ──┐
                   ├──► q·k₁ ─┐
 keys  k₁ k₂ k₃ ───┘   q·k₂  ├─ /√d_k ─► softmax ─► α₁ α₂ α₃
                       q·k₃ ─┘                          │
 values v₁ v₂ v₃ ───────────────────────────────────────┴──► Σ αⱼ vⱼ  = output_i
```

### 📐 Why divide by `√d_k` ⭐⭐⭐ (asked constantly)

Assume the components of `q` and `k` are independent with mean 0 and variance 1. Then

```
q·k = Σ_{i=1}^{d_k} qᵢkᵢ ,    E[q·k] = 0 ,    Var(q·k) = Σ Var(qᵢkᵢ) = d_k
```

so the dot product has standard deviation `√d_k`. With `d_k = 64` the logits have a spread of ±8 or
more; the softmax of such large-magnitude logits is nearly one-hot, and its gradient
`diag(p) − ppᵀ` becomes vanishingly small — training stalls. Dividing by `√d_k` normalises the
variance back to 1, keeping the softmax in its responsive regime. ∎

✅ Complete answer: *"The dot product's variance grows linearly with `d_k`, so without scaling the
softmax saturates and its gradients vanish; `√d_k` restores unit variance."*

### Masking

```
causal (decoder) mask:  set scores above the diagonal to −∞ before the softmax,
                        so position i cannot see j > i          ⭐ enables parallel training
padding mask:           −∞ on pad positions so they get zero weight
```

Using `−∞` (in practice a large negative number) rather than zeroing after the softmax is
important: the weights must still sum to 1 over the *allowed* positions.

---

## 2. Multi-head attention ⭐⭐⭐

```
head_i = Attention(Q W_i^Q, K W_i^K, V W_i^V)          W_i^Q: (d_model, d_k)
MHA(Q,K,V) = Concat(head_1 … head_h) W^O               W^O:  (h·d_v, d_model)
with  d_k = d_v = d_model / h        (so the total cost is the same as one big head)
```

**Why multiple heads:** a single softmax-weighted average can only attend to one "kind" of
relationship at a time; multiple heads let the model attend to different subspaces simultaneously —
in practice some heads track syntactic dependencies, some track coreference, some attend to the
previous token, some to delimiters. It is the attention analogue of having many filters in a
convolution layer. ⭐⭐

**Parameter count of an MHA block:** `4 · d_model²` (+ biases) — `W^Q, W^K, W^V, W^O` are each
`d_model × d_model` in total across heads.

**Efficient variants ⭐** (asked in LLM-focused interviews): **MQA** (multi-query — all heads share
one K and V) and **GQA** (grouped-query — heads share K/V in groups) shrink the KV cache
dramatically at inference with minimal quality loss; Llama-2 70B and most modern LLMs use GQA.

---

## 3. Self-attention vs cross-attention

```
self-attention : Q, K, V all come from the SAME sequence  (encoder layers; decoder's first block)
cross-attention: Q from the decoder, K and V from the ENCODER output (translation, T5, Whisper)
```

---

## 4. The transformer block ⭐⭐⭐

```
                ┌─────────────────────────────────────┐
   x ──────────►│                                     │
     │          │   ┌───────────────┐                 │
     │          │   │  LayerNorm    │  (Pre-LN)       │
     │          │   └───────┬───────┘                 │
     │          │           ▼                         │
     │          │   ┌───────────────┐                 │
     │          │   │ Multi-Head    │                 │
     │          │   │  Attention    │                 │
     │          │   └───────┬───────┘                 │
     └──────────┼──────► (+) ◄──────── residual       │
                │           │                         │
                │           ▼                         │
                │   ┌───────────────┐                 │
                │   │  LayerNorm    │                 │
                │   └───────┬───────┘                 │
                │           ▼                         │
                │   ┌───────────────────────────────┐ │
                │   │ FFN:  Linear(d→4d) → GELU     │ │
                │   │       → Linear(4d→d)          │ │
                │   └───────┬───────────────────────┘ │
                │           │                         │
                └──────► (+) ◄──────── residual       │
                            │                         │
                            ▼                      × N layers
```

**The FFN** is applied **position-wise and identically** to every token — it is a per-token
2-layer MLP with hidden size `4·d_model`. It holds roughly **two thirds of the model's
parameters** (`8·d_model²` versus attention's `4·d_model²`), and current interpretability work
treats it as the model's key–value memory of facts. ⭐⭐

**Residuals + LayerNorm** are what make depth trainable (see the deep-learning folder).
**Pre-LN** (normalise *inside* the residual branch) is what modern LLMs use: it trains stably with
little warmup. The original paper used Post-LN, which needs careful warmup. ⭐

---

## 5. Positional encoding ⭐⭐⭐

Attention is **permutation-equivariant** — it is a weighted sum, so shuffling the input shuffles the
output identically. Without positional information "dog bites man" and "man bites dog" are
indistinguishable. This is the motivation, and it is the expected first sentence. ⭐⭐⭐

**Sinusoidal (original paper):**

```
PE(pos, 2i)   = sin( pos / 10000^{2i/d} )
PE(pos, 2i+1) = cos( pos / 10000^{2i/d} )
```

Added to the embeddings. Each dimension is a sinusoid of a different wavelength (a positional
"binary code" in continuous form). The property that motivated it: `PE(pos+k)` is a **linear
function** of `PE(pos)` (a rotation), so relative offsets are learnable, and it extrapolates to
lengths not seen in training.

**Learned absolute** (BERT, GPT-2): a trainable embedding per position — simple, but cannot exceed
the trained maximum length.

**RoPE (rotary) ⭐⭐** — the modern standard (LLaMA, most open LLMs). Instead of adding anything, it
*rotates* the query and key vectors by an angle proportional to position:

```
q'_m = R_m q_m ,  k'_n = R_n k_n   ⇒   q'_mᵀ k'_n = qᵀ R_{n−m} k
```

The dot product therefore depends only on the **relative** distance `n − m`. Benefits: relative
positioning, no extra parameters, works with the KV cache, and can be extended to longer contexts by
scaling the frequencies (NTK/YaRN interpolation).

**ALiBi:** adds a linear distance penalty `−m·|i−j|` to the attention scores; trivially extrapolates
to longer sequences.

---

## 6. Complexity ⭐⭐⭐

```
attention:  QKᵀ is (n × d)·(d × n) → O(n²·d) time,  O(n²) memory for the score matrix
FFN:        O(n·d²)
```

So cost is **quadratic in sequence length** — the central constraint of the whole field.

| Mitigation | Idea |
|---|---|
| **FlashAttention** | exact attention, tiled to stay in SRAM; never materialises the `n×n` matrix ⇒ memory `O(n)` and a large speedup ⭐⭐ |
| Sparse/local/window attention | Longformer, BigBird — attend only to a neighbourhood plus a few globals |
| Linear attention | remove the softmax so `(QKᵀ)V` becomes `Q(KᵀV)` ⇒ `O(n·d²)` |
| State-space models | Mamba, S4 — linear-time recurrent alternatives |
| KV cache + GQA/MQA | reduce inference memory rather than training cost |

⚠️ FlashAttention does **not** change the `O(n²)` arithmetic — it changes the memory traffic. Saying
"FlashAttention makes attention linear" is wrong and is a known trap.

---

## 7. Encoder-only, decoder-only, encoder–decoder ⭐⭐

| | Attention | Examples | Good at |
|---|---|---|---|
| **Encoder-only** | bidirectional | BERT, RoBERTa, DeBERTa | classification, NER, retrieval embeddings |
| **Decoder-only** | causal (masked) | GPT, LLaMA, Mistral | generation; now dominant for everything |
| **Encoder–decoder** | bidirectional encoder + causal decoder + cross-attention | T5, BART, Whisper | translation, summarisation, seq2seq |

---

## 8. Minimal implementation (be able to write this)

```python
import torch, torch.nn as nn, torch.nn.functional as F

class MultiHeadAttention(nn.Module):
    def __init__(self, d_model, n_heads):
        super().__init__()
        assert d_model % n_heads == 0
        self.h, self.dk = n_heads, d_model // n_heads
        self.qkv  = nn.Linear(d_model, 3 * d_model)
        self.proj = nn.Linear(d_model, d_model)

    def forward(self, x, causal=True):
        B, T, C = x.shape
        q, k, v = self.qkv(x).split(C, dim=2)
        # (B, T, C) -> (B, h, T, dk)
        q = q.view(B, T, self.h, self.dk).transpose(1, 2)
        k = k.view(B, T, self.h, self.dk).transpose(1, 2)
        v = v.view(B, T, self.h, self.dk).transpose(1, 2)

        att = (q @ k.transpose(-2, -1)) / (self.dk ** 0.5)      # (B, h, T, T)
        if causal:
            mask = torch.triu(torch.ones(T, T, device=x.device), diagonal=1).bool()
            att = att.masked_fill(mask, float("-inf"))
        att = F.softmax(att, dim=-1)
        y = att @ v                                             # (B, h, T, dk)
        y = y.transpose(1, 2).contiguous().view(B, T, C)
        return self.proj(y)
```

⚠️ Details that are marked: the `√d_k` scaling, masking with `−inf` **before** the softmax,
`transpose(1,2)` + `contiguous()` before the final reshape, and a single fused `qkv` projection.

---

## 9. Scaling laws (a good extra) ⭐

Loss falls as a power law in parameters, data and compute. **Chinchilla** showed that models were
badly under-trained: the compute-optimal ratio is roughly **20 tokens per parameter**, so a 70B
model wants ~1.4T tokens. Later practice trains far past compute-optimal because inference cost
dominates over a model's lifetime. Mentioning both points is a strong signal.

---

## Recall questions

1. Write scaled dot-product attention and explain Q, K, V with the lookup analogy.
2. Derive why the scaling factor is `√d_k`, and what breaks without it.
3. Why multiple heads rather than one big one?
4. Draw a pre-LN transformer block from memory.
5. Where do most of the parameters live, and give the two formulas.
6. Why are positional encodings necessary at all?
7. Explain RoPE and the property that makes it relative.
8. State the time and memory complexity of attention and of the FFN.
9. What does FlashAttention change and what does it *not* change?
10. Encoder-only vs decoder-only vs encoder–decoder: one use case each.
