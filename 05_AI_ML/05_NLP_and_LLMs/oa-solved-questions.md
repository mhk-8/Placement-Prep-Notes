
# NLP & LLMs — Solved OA Questions

30 questions spanning embeddings, transformers, pretraining, finetuning, decoding and serving.

---

## Set A — Representation

**Q1.** 1000 documents; "transformer" appears in 20 of them and 4 times in a 200-word document.
TF-IDF (natural log, `idf = log(N/(1+df)) + 1`)?

<details><summary>Answer</summary>

`tf = 4/200 = 0.02`; `idf = ln(1000/21) + 1 = 3.865 + 1 = 4.865`; `tfidf ≈ 0.0973`.
</details>

**Q2.** Why does word2vec use negative sampling?

<details><summary>Answer</summary>

The full softmax normalises over the whole vocabulary — `O(|V|)` per example. Negative sampling
replaces it with `k+1` binary logistic problems (one positive, `k` sampled negatives), reducing the
cost to `O(k)`.
</details>

**Q3.** What can FastText do that word2vec cannot?

<details><summary>Answer</summary>

Represent out-of-vocabulary and morphologically related words, because a word vector is the sum of
its character n-gram vectors.
</details>

**Q4.** In one sentence, what do contextual embeddings fix?

<details><summary>Answer</summary>

Polysemy: static embeddings give one vector per word type, so "bank" gets a single averaged vector;
contextual models give a different vector per occurrence based on the sentence.
</details>

**Q5.** Vocabulary 50 000, embedding dimension 768. Embedding-table parameters?

<details><summary>Answer</summary>

`50 000 × 768 = 38.4 M`.
</details>

**Q6.** Why does an LLM struggle to count the letters in a word?

<details><summary>Answer</summary>

It sees subword tokens, not characters; the character composition of a token is not directly
represented. Tokenisation, not reasoning, is the limitation.
</details>

---

## Set B — Attention and transformers

**Q7.** Write scaled dot-product attention.

<details><summary>Answer</summary>

`Attention(Q,K,V) = softmax(QKᵀ/√d_k)V`.
</details>

**Q8.** Why `√d_k` and not `d_k`?

<details><summary>Answer</summary>

With unit-variance independent components, `Var(q·k) = d_k`, so the standard deviation is `√d_k`.
Dividing by `√d_k` restores unit variance; dividing by `d_k` would over-shrink the logits. Without
scaling, the softmax saturates and its gradient vanishes.
</details>

**Q9.** `d_model = 512`, `h = 8`. What is `d_k`, and how many parameters are in the MHA block
(ignoring biases)?

<details><summary>Answer</summary>

`d_k = 512/8 = 64`. Parameters `= 4 · 512² = 1 048 576` (Q, K, V, O projections).
</details>

**Q10.** Sequence length 1024, `d_model = 768`. Memory for one attention score matrix per head, in
fp16?

<details><summary>Answer</summary>

`1024² × 2 bytes = 2 MB` per head; ×12 heads = 24 MB per layer per sequence — and this is exactly
what FlashAttention avoids materialising.
</details>

**Q11.** Why are positional encodings needed?

<details><summary>Answer</summary>

Attention is permutation-equivariant — a weighted sum has no notion of order — so without them
"dog bites man" and "man bites dog" would be identical.
</details>

**Q12.** What makes RoPE relative?

<details><summary>Answer</summary>

It rotates `q` and `k` by angles proportional to their positions, so `q'_mᵀk'_n = qᵀR_{n−m}k`
depends only on `n − m`.
</details>

**Q13.** True or false: FlashAttention makes attention linear in sequence length.

<details><summary>Answer</summary>

**False.** The arithmetic is still `O(n²)`; FlashAttention reduces *memory traffic* and peak memory
to `O(n)` by tiling and never materialising the score matrix.
</details>

**Q14.** Where do most of a transformer's parameters live?

<details><summary>Answer</summary>

The FFN: `8·d_model²` per block versus attention's `4·d_model²` — roughly two thirds.
</details>

**Q15.** What does the causal mask do and why does it enable efficient training?

<details><summary>Answer</summary>

It sets scores for future positions to `−∞` so position `i` cannot attend to `j > i`. Because the
mask enforces causality, all positions can be trained **in parallel** in one forward pass while
still being valid next-token predictions.
</details>

---

## Set C — Pretraining and finetuning

**Q16.** Explain BERT's 80/10/10 masking.

<details><summary>Answer</summary>

Of the 15% selected tokens: 80% become `[MASK]`, 10% a random token, 10% unchanged. `[MASK]` never
appears at finetuning time, so training only on it would create a train/test mismatch; the random
and unchanged cases force good representations of every token.
</details>

**Q17.** Why does causal LM give more training signal per token than MLM?

<details><summary>Answer</summary>

MLM computes a loss on only ~15% of positions; causal LM gets a prediction target at every
position.
</details>

**Q18.** LoRA with `d = 4096` and `r = 16`. Parameters per weight matrix, versus full?

<details><summary>Answer</summary>

LoRA `= 2 · 4096 · 16 = 131 072`; full `= 4096² = 16 777 216`. A 128× reduction.
</details>

**Q19.** Why is LoRA's `B` initialised to zero?

<details><summary>Answer</summary>

So `BA = 0` at step 0 and the adapted model is exactly the pretrained model — training starts from
the known-good function rather than a random perturbation.
</details>

**Q20.** Your chatbot gives outdated facts about your company's products. Finetune or RAG?

<details><summary>Answer</summary>

**RAG.** Finetuning teaches behaviour and style, not reliable, updatable facts; documents change, and
retrieval gives citations and lets you update the index without retraining.
</details>

**Q21.** State Chinchilla's conclusion.

<details><summary>Answer</summary>

For a fixed compute budget, parameters and training tokens should scale roughly equally — about 20
tokens per parameter. Earlier models such as GPT-3 were badly under-trained.
</details>

**Q22.** Why does PPO in RLHF include a KL penalty?

<details><summary>Answer</summary>

To stop the policy drifting away from the SFT model and exploiting the imperfect reward model
(reward hacking), which produces degenerate high-scoring text.
</details>

**Q23.** What does DPO eliminate?

<details><summary>Answer</summary>

The separate reward model and the RL loop — preference optimisation becomes a stable supervised
classification loss on preferred/rejected pairs.
</details>

**Q24.** In SFT, on which tokens should the loss be computed?

<details><summary>Answer</summary>

Only the response tokens; the prompt is masked out. Otherwise capacity is spent learning to generate
instructions.
</details>

---

## Set D — Decoding, RAG, serving

**Q25.** Logits `[2, 1, 0]` at `T = 0.5`. Resulting probabilities?

<details><summary>Answer</summary>

Divide by `T`: `[4, 2, 0]`. `e⁴ = 54.6`, `e² = 7.39`, `e⁰ = 1`; sum `= 63.0` ⇒
`[0.867, 0.117, 0.016]` — sharper than at `T = 1` (`[0.665, 0.245, 0.090]`).
</details>

**Q26.** Top-k vs top-p — which adapts to the distribution and why does that matter?

<details><summary>Answer</summary>

Top-p (nucleus). When the model is confident, the nucleus is one or two tokens; when it is
uncertain, it widens. Top-k always keeps `k` tokens, which can either include nonsense or cut off
valid options.
</details>

**Q27.** Why is beam search a poor choice for story generation?

<details><summary>Answer</summary>

Maximising sequence likelihood yields short, generic, repetitive text; natural human text is not
the maximum-likelihood sequence.
</details>

**Q28.** KV cache for a 7B model — 32 layers, 32 heads, `d_head = 128`, fp16, 2 048 tokens, batch
8?

<details><summary>Answer</summary>

`2 × 32 × 32 × 128 × 2048 × 8 × 2 B ≈ 8.6 GB`. GQA/MQA reduce `n_kv_heads` and cut this by up to
8×.
</details>

**Q29.** A RAG system returns wrong answers. What do you check first?

<details><summary>Answer</summary>

Whether the correct chunk was retrieved at all — measure retrieval recall@k separately from
generation faithfulness. Most "hallucination" reports are retrieval failures (bad chunking, no
reranker, no hybrid search, missing metadata filter).
</details>

**Q30.** 50 000 requests/day, 1 500 input and 400 output tokens, \$3/M input and \$15/M output.
Monthly cost?

<details><summary>Answer</summary>

Input `50 000 × 1 500 = 75 M/day → $225/day`. Output `50 000 × 400 = 20 M/day → $300/day`.
Total `$525/day ≈ $15 750/month`.
</details>

---

## Scoring

| Correct | Read as |
|---|---|
| 26–30 | Ready for LLM-focused rounds |
| 20–25 | Redo the attention and serving arithmetic until automatic |
| 13–19 | Reread `02`, `04` and `05` of this folder |
| < 13 | This is the highest-signal folder for 2026 interviews — spend three days |
