
# NLP & LLMs — Flashcards

---

## Questions

**Representation**
1. TF-IDF formula and the purpose of each factor.
2. Distributional hypothesis.
3. Skip-gram vs CBOW; the softmax problem; negative sampling.
4. What FastText adds.
5. The one-line limitation of static embeddings.
6. Why not raw `[CLS]` for similarity; what to use instead.
7. BPE in four steps; what byte-level BPE guarantees.
8. Three downstream consequences of tokenisation.
9. Bi-encoder vs cross-encoder.
10. Hybrid search and why it wins.

**Attention and transformers**
11. Scaled dot-product attention, with the lookup analogy.
12. Derivation of the `√d_k` factor.
13. Why multiple heads?
14. MQA and GQA — what problem do they solve?
15. Draw a pre-LN block.
16. Parameter counts: attention vs FFN.
17. Why positional encodings at all?
18. Sinusoidal, learned, RoPE, ALiBi — one line each.
19. Complexity of attention and of the FFN.
20. What FlashAttention changes and does not change.
21. Causal mask: what it does and why training is parallel.
22. Encoder-only / decoder-only / encoder–decoder.

**Pretraining**
23. MLM vs CLM; the loss-signal difference.
24. BERT's 80/10/10 and why.
25. What happened to NSP.
26. In-context learning.
27. Four modern LLM architecture choices.
28. The four-stage training pipeline.
29. Chinchilla, and why models are trained past compute-optimal.
30. Benchmark contamination.
31. When to prefer BERT over an LLM.

**Finetuning and alignment**
32. The adaptation ladder; RAG vs finetuning.
33. LoRA maths and the parameter saving.
34. Why `B = 0`; what `α/r` does; zero added latency.
35. QLoRA.
36. SFT loss masking.
37. Reward-model loss; why ranking not scoring.
38. The PPO objective and the role of the KL term.
39. DPO — what it removes and the trade-off.
40. Distillation loss and the `T²` factor.
41. PTQ vs QAT; why LLM quantisation is hard.

**Decoding, RAG, agents**
42. Temperature, top-k, top-p.
43. Why beam search fails for open generation.
44. Speculative decoding.
45. Root cause of hallucination; the mitigation ladder.
46. Full RAG pipeline.
47. Four chunking strategies.
48. RAG evaluation, split by stage.
49. Why RAG survives long context.
50. Prompt injection and the correct defences.
51. Agent failure modes.

**Evaluation and serving**
52. Perplexity: formula and when it is incomparable.
53. BLEU/ROUGE/BERTScore/pass@k.
54. LLM-as-judge biases.
55. Prefill vs decode.
56. KV cache formula and a worked number.
57. TTFT vs TPOT; the latency/throughput trade-off.
58. Continuous batching and PagedAttention.
59. API cost estimation method.
60. Production checklist before shipping an LLM feature.

---

## Answers

1. `tf(t,d)·idf(t)` with `idf = log(N/(1+df)) + 1`: frequent here, rare elsewhere.

2. Words occurring in similar contexts have similar meanings; the basis of all embedding methods.

3. Skip-gram predicts context from the centre word (better for rare words); CBOW the reverse
   (faster). The softmax normalises over the entire vocabulary; negative sampling replaces it with
   `k+1` binary logistic problems using noise distribution `∝ U(w)^{3/4}`.

4. Character n-gram composition ⇒ OOV handling and morphology.

5. One vector per word type, so polysemy cannot be represented, and there is no sentence-level or
   order information.

6. Raw `[CLS]` produces a poor metric space; use Sentence-BERT/SimCSE, mean pooling, or a dedicated
   embedding model.

7. Start from characters/bytes; count adjacent pairs; merge the most frequent; repeat to the target
   vocabulary size. Byte-level BPE guarantees no out-of-vocabulary input ever.

8. Non-English text costs more tokens (cost and effective context); character-level tasks are hard;
   inconsistent number splitting harms arithmetic.

9. Bi-encoder embeds query and document separately (indexable, fast); cross-encoder scores the pair
   jointly (accurate, `O(N)` per query) — hence retrieve then rerank.

10. BM25 plus dense retrieval fused with reciprocal rank fusion; BM25 handles exact and rare terms,
    embeddings handle paraphrase.

11. `softmax(QKᵀ/√d_k)V`; queries ask, keys advertise, values contribute — a soft differentiable
    dictionary lookup.

12. `Var(q·k) = d_k` for unit-variance components, so the logits have scale `√d_k`; without
    rescaling the softmax saturates and its gradient `diag(p) − ppᵀ` vanishes.

13. One softmax average captures one relationship; heads attend to different subspaces
    simultaneously — the analogue of multiple convolution filters.

14. They shrink the KV cache at inference by sharing K and V across all heads (MQA) or across groups
    (GQA), with minimal quality loss.

15. `x → LN → MHA → +x → LN → FFN(4d, GELU) → +x`, repeated `N` times.

16. Attention `4·d_model²`; FFN `8·d_model²` — the FFN holds roughly two thirds.

17. Attention is permutation-equivariant, so word order would carry no information.

18. Sinusoidal: fixed multi-frequency, extrapolates, relative offsets are linear. Learned: a
    trainable vector per position, capped at the trained length. RoPE: rotates q and k so the dot
    product depends on relative distance. ALiBi: a linear distance penalty on the scores.

19. Attention `O(n²d)` time and `O(n²)` score memory; FFN `O(nd²)`.

20. It changes memory traffic and peak memory (to `O(n)`) via tiling; the arithmetic remains
    `O(n²)`.

21. Sets future scores to `−∞`; because causality is enforced by the mask, every position's
    next-token loss can be computed in one parallel forward pass.

22. Encoder-only (BERT) for classification/NER/embeddings; decoder-only (GPT) for generation;
    encoder–decoder (T5) for seq2seq with cross-attention.

23. MLM predicts masked tokens with bidirectional context but gets a loss on ~15% of positions;
    CLM predicts the next token with a loss at every position and is natively generative.

24. 80% `[MASK]`, 10% random, 10% unchanged — `[MASK]` never appears at finetuning time, so the
    split avoids a train/inference mismatch and forces good representations of all tokens.

25. RoBERTa dropped it with no loss (indeed an improvement); dynamic masking and more data mattered
    instead.

26. Performing a new task from examples or instructions in the prompt, with no weight updates.

27. RMSNorm with pre-norm, RoPE, SwiGLU, GQA (+ no biases, and MoE for sparse scaling).

28. Pretrain (next-token on trillions of tokens) → SFT (instruction following) → preference
    optimisation (RLHF/DPO) → optionally RL on verifiable rewards for reasoning.

29. Parameters and tokens should scale together, ~20 tokens/parameter. Inference cost dominates over
    a model's lifetime, so models are now over-trained relative to compute-optimal.

30. Test data leaking into pretraining, making public scores meaningless; mitigated by
    decontamination and private/fresh evaluations.

31. High-volume classification or retrieval, where a 110M encoder is orders of magnitude cheaper per
    request and often more accurate after finetuning.

32. Prompt → few-shot → RAG → PEFT → full finetune → pretrain. RAG for knowledge, finetuning for
    behaviour.

33. `W' = W₀ + BA` with `A ∈ R^{r×d}`, `B ∈ R^{d×r}`; `2dr` vs `d²` parameters — 256× smaller at
    `d = 4096`, `r = 8`.

34. `B = 0` makes the initial function identical to the base model; `α/r` decouples the learning
    rate from `r`; `W₀ + BA` can be merged after training, so inference cost is unchanged and many
    adapters can share one base model.

35. 4-bit NF4 quantisation of the frozen base plus bf16 LoRA adapters, double quantisation and
    paged optimisers — finetuning a 65B model on one 48 GB GPU.

36. Only on the response tokens; the prompt is masked.

37. `−log σ(r(x,y_w) − r(x,y_l))`; humans rank far more consistently than they score absolutely.

38. `max E[r(x,y)] − β·KL(π ‖ π_SFT)`; the KL term prevents reward hacking by anchoring the policy
    near the SFT model.

39. The reward model and the RL loop, by using the closed-form optimal policy to express reward in
    terms of the policy; simpler and more stable, slightly less controllable.

40. `α·CE(student, y) + (1−α)·T²·KL(student_T ‖ teacher_T)`; `T` exposes the teacher's relative
    confidences and `T²` compensates for the `1/T²` gradient shrinkage.

41. PTQ quantises after training (GPTQ, AWQ); QAT simulates quantisation during training. Outlier
    activation channels dominate the error, so methods keep a small fraction in higher precision.

42. Temperature rescales logits before the softmax; top-k keeps the `k` highest-probability tokens;
    top-p keeps the smallest set with cumulative probability ≥ `p`, adapting to the distribution.

43. It maximises sequence likelihood, which favours short, generic, repetitive text; human text is
    not maximum-likelihood.

44. A small draft model proposes `k` tokens that the large model verifies in one pass; the output
    distribution is provably unchanged, with a 2–3× speedup.

45. The model optimises fluency/likelihood, not truth. Ladder: RAG with citations → permit "I don't
    know" → lower temperature → structured output → self-consistency → verification pass → tools →
    confidence-based abstention.

46. Chunk and embed offline; at query time rewrite, hybrid-retrieve, fuse, rerank with a
    cross-encoder, build the prompt with citations, generate, then optionally check groundedness.

47. Fixed-size with overlap; structure-aware (headings/paragraphs); semantic (similarity drops);
    small-to-big (embed small, return parent). Contextual chunk summaries are a strong addition.

48. Retrieval: recall@k, MRR, NDCG. Generation: faithfulness, answer relevance, context precision.
    Separating them tells you which stage failed.

49. Cheaper, faster, auditable via citations, updatable without retraining, and it avoids the
    "lost in the middle" degradation of very long contexts.

50. Untrusted text carrying instructions. Defences: treat retrieved content as data, enforce
    permissions and tool scopes outside the model, validate and sandbox tool inputs, human approval
    for destructive actions, no secrets in prompts.

51. Non-termination, compounding per-step error (0.95¹⁰ ≈ 0.6), poor tool-error recovery, runaway
    cost/latency, missing observability, over-broad credentials.

52. `PPL = exp(mean NLL)`; comparable only with the same tokeniser and evaluation data.

53. BLEU: n-gram precision for translation. ROUGE: n-gram/LCS recall for summarisation. BERTScore:
    embedding similarity, better semantics. pass@k: fraction of problems solved in `k` samples
    against unit tests.

54. Position bias (randomise order), verbosity bias, self-preference, and rubric sensitivity —
    calibrate against human labels.

55. Prefill processes the whole prompt in parallel and is compute-bound; decode generates one token
    at a time and is memory-bandwidth-bound.

56. `2 × layers × kv_heads × d_head × seq × batch × bytes`; a 7B model at 4 096 tokens, fp16 is
    ~2.1 GB per sequence — often larger than the weights at batch scale.

57. TTFT is dominated by prefill, TPOT by decode bandwidth. Larger batches raise throughput but
    also per-request latency; streaming hides TPOT.

58. Continuous batching lets new requests join mid-flight instead of waiting for a whole batch;
    PagedAttention stores the KV cache in fixed pages, removing fragmentation and raising batch
    size.

59. Tokens/request × requests/day × per-token price, separately for input and output (output is
    3–5× dearer); then apply prompt shortening, prefix caching, semantic caching, model routing and
    `max_tokens` caps.

60. Safety/moderation, privacy and data residency, reliability (timeouts, retries, fallbacks),
    observability and tracing, model and prompt version pinning with a regression suite, drift
    monitoring, caching, rate limits and cost alerts.
