
# LLM Evaluation, Serving and Cost ⭐⭐

> **Core idea in 3 lines**
> 1. Evaluating generative systems is hard because there is no single correct output — so you
>    build task-specific evals, not a single number.
> 2. Serving cost and latency are dominated by the KV cache and by memory bandwidth, not by
>    arithmetic.
> 3. In an ML system design round, being able to size a deployment (tokens/s, GPU count, cost per
>    1k requests) is worth more than any model detail.

---

## 1. Evaluation ⭐⭐

### Classical automatic metrics

| Metric | Task | How | Weakness |
|---|---|---|---|
| **Perplexity** | language modelling | `exp(mean NLL)` — the model's average "branching factor" | only comparable with the same tokeniser and data ⚠️ |
| BLEU | translation | n-gram precision + brevity penalty | ignores meaning; poor for open generation |
| ROUGE-1/2/L | summarisation | n-gram / longest-common-subsequence recall | rewards copying |
| METEOR, chrF | translation | includes stems/synonyms | still surface-level |
| **BERTScore** | generation | cosine similarity of contextual embeddings | better semantics, still no factuality |
| Exact match / F1 | extractive QA | span overlap | brittle to formatting |
| pass@k | code | fraction solved in `k` samples against unit tests ⭐ | needs executable tests |

📐 **Perplexity:** `PPL = exp( −(1/N) Σ log p(x_t | x_{<t}) )`. A perplexity of 10 means the model
is on average as uncertain as if choosing uniformly among 10 tokens.

### Benchmarks worth naming

MMLU (knowledge), GSM8K and MATH (maths), HumanEval and MBPP (code), HellaSwag and ARC (commonsense),
TruthfulQA (factuality), BBH, GPQA (hard reasoning), MT-Bench and Chatbot Arena / LMSYS Elo (human
preference), SWE-bench (real software issues), HELM (holistic).

⚠️ **Benchmarks saturate and leak.** Always pair public scores with a private, freshly written
evaluation set drawn from your own traffic. Contamination makes public numbers unreliable. ⭐⭐

### LLM-as-a-judge ⭐⭐

Use a strong model to score outputs against a rubric or to compare pairs.

```
✅ scalable, cheap, correlates reasonably with human judgement on many tasks
⚠️ position bias (prefers the first option) → randomise order and average both orders
⚠️ verbosity bias (prefers longer answers)
⚠️ self-preference (prefers its own family's outputs)
⚠️ needs a rubric, few-shot anchors, and periodic calibration against human labels
```

### The evaluation stack you should describe

```
1. Unit-style assertions: format valid, schema parses, no PII, refusals where required
2. A golden set of 100–500 curated examples with expected behaviour, versioned in git
3. Task metrics: exact match / pass@k / groundedness, per slice
4. LLM-as-judge for open-ended quality, calibrated against human labels
5. Human review of a sample, weekly
6. Online: A/B test with product metrics + guardrails (latency, cost, complaint rate)
7. Regression suite run on every prompt or model change  ⭐ prompts are code
```

---

## 2. Inference mechanics ⭐⭐⭐

Generation has two distinct phases with completely different cost profiles:

```
PREFILL  — process the whole prompt in parallel      compute-bound,  O(n²) attention
DECODE   — generate one token at a time, reusing the memory-bandwidth-bound, one token per
           KV cache                                  forward pass ⚠️
```

**KV cache.** Without it, generating token `t` would recompute attention over all previous tokens
from scratch. Instead the keys and values of past tokens are cached.

📐 **KV cache size:**

```
bytes = 2 (K and V) × n_layers × n_kv_heads × d_head × seq_len × batch × bytes_per_value
```

*Example.* A 7B model: 32 layers, 32 heads, `d_head = 128`, fp16, 4096 tokens, batch 1:

```
2 × 32 × 32 × 128 × 4096 × 2 B ≈ 2.1 GB per sequence ⚠️
```

At batch 32 that is 68 GB — **more than the model weights**. This is why GQA/MQA (which cut
`n_kv_heads`) matter so much, and it is the number to produce in a serving interview. ⭐⭐⭐

**Latency metrics:**

```
TTFT  time to first token    ← dominated by prefill; what the user feels first
TPOT  time per output token  ← dominated by memory bandwidth
end-to-end = TTFT + TPOT × n_output
throughput = tokens/s across all concurrent requests
```

⚠️ **Latency and throughput trade off.** Larger batches raise throughput and raise per-request
latency. Streaming tokens to the user hides TPOT and transforms perceived latency.

### Serving optimisations ⭐⭐

| Technique | Effect |
|---|---|
| **Continuous batching** | new requests join the batch as others finish, instead of waiting for the whole batch — the single biggest throughput win (vLLM, TGI) ⭐ |
| **PagedAttention** | KV cache in fixed pages like virtual memory ⇒ near-zero fragmentation, much higher batch sizes |
| **Quantisation** (int8/int4) | smaller weights, less bandwidth, more room for KV cache |
| **GQA/MQA** | smaller KV cache |
| **FlashAttention** | faster prefill, less memory traffic |
| **Speculative decoding** | 2–3× faster decode with an identical output distribution |
| **Prefix/prompt caching** | reuse the KV cache of a shared system prompt across requests ⭐ |
| **Tensor parallelism** | split a model too large for one GPU |
| **Distillation / routing** | send easy requests to a small model |

---

## 3. Cost estimation ⭐⭐⭐ (practise producing these numbers)

**API pricing model:** priced per 1M input and output tokens, with output typically 3–5× the price
of input. Rule of thumb: 1 token ≈ 4 characters ≈ 0.75 words.

*Worked example.* 100 000 requests/day, 2 000 input tokens and 500 output tokens each, at
\$3/M input and \$15/M output:

```
input : 100 000 × 2 000 = 200 M tokens/day × $3/M  = $600/day
output: 100 000 ×   500 =  50 M tokens/day × $15/M = $750/day
total ≈ $1 350/day ≈ $40 500/month
```

Levers, in order of impact: shorten prompts and cache shared prefixes; cache identical or similar
answers semantically; route easy traffic to a smaller model; cap `max_tokens`; batch offline jobs;
finetune a small open model once volume justifies it.

*Self-hosting break-even.* An A100/H100 instance costs roughly \$2–4/hour ≈ \$1 500–3 000/month. A
7B model on one GPU with continuous batching serves on the order of 1 000–3 000 tokens/s. Self-hosting
wins at sustained high volume; APIs win for spiky or low volume, and there is engineering cost on
top.

---

## 4. Production concerns ⭐⭐

```
Safety      : input and output moderation, jailbreak resistance, PII redaction,
              refusal behaviour; red-teaming before launch
Privacy     : do not send sensitive data to third-party APIs without a data agreement;
              consider on-prem for regulated data
Reliability : timeouts, retries with backoff, fallback to a smaller model or a cached answer,
              circuit breakers, graceful degradation to a non-LLM path
Observability: log prompt, response, latency, token counts, tool calls, user feedback;
              trace the whole chain; sample for human review
Versioning  : pin model versions — provider updates silently change behaviour ⚠️
              version prompts in git and run the regression suite on every change ⭐
Drift       : user inputs shift over time; monitor topic distribution, refusal rate,
              thumbs-down rate, and retrieval recall
Caching     : exact-match cache, then semantic cache with a similarity threshold
Rate limits : per-user quotas; queueing; cost alerts
```

---

## 5. Choosing a model ⭐

```
1. Write the evaluation FIRST, from real examples of your task.
2. Start with the strongest available model to establish an achievable quality ceiling.
3. Then move down in cost until quality drops below the bar: big API model →
   small API model → finetuned open 7–13B → distilled task-specific model.
4. Check the non-quality constraints: latency budget, data residency, licence
   (some open weights forbid commercial use), context length, tool-calling support.
5. Re-run the evaluation on every model or prompt change.
```

---

## Recall questions

1. Define perplexity and say when it is *not* comparable across models.
2. Name the weakness of BLEU and ROUGE, and what BERTScore fixes.
3. What is pass@k?
4. List four biases of LLM-as-a-judge and the mitigation for each.
5. Distinguish prefill and decode, and say which resource bounds each.
6. Compute the KV cache for a 7B model at 4 096 tokens, and say why GQA matters.
7. Define TTFT and TPOT and explain the latency/throughput trade-off.
8. What do continuous batching and PagedAttention each solve?
9. Estimate the monthly API cost for 100k requests/day at 2k in / 500 out tokens.
10. List the production concerns you would raise before shipping an LLM feature.
