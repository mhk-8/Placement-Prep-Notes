
# Case Study: Enterprise RAG Assistant ⭐⭐⭐

*"Design an internal assistant that answers employee questions from company documents."*

This is the single most likely ML system design question in 2025–26 interviews.

---

## 1. Clarify

```
Corpus    : 500k documents — wikis, PDFs, tickets, code, Slack; ~2M chunks; updated daily
Users     : 10k employees, ~20k questions/day
Latency   : first token < 2 s, full answer < 10 s (streamed)
Accuracy  : must cite sources; must say "I don't know" rather than guess ⚠️⭐
Security  : ⭐⭐⭐ answers must respect per-document permissions (HR docs, finance,
            per-team spaces) — this is the requirement candidates forget
Constraints: data cannot leave the VPC (⇒ self-hosted or a vetted API with a DPA)
Budget    : cost per question target
```

---

## 2. Metrics ⭐⭐

```
BUSINESS  : tickets deflected from the helpdesk, time saved, adoption, thumbs-up rate
ML        : retrieval recall@k and MRR; answer faithfulness/groundedness;
            answer relevance; citation correctness
GUARDRAILS: p95 latency, cost/query, hallucination rate on a golden set,
            permission-violation rate (must be ZERO) ⭐, refusal rate
```

---

## 3. Architecture ⭐⭐⭐

```
 ═══════════════ INDEXING (offline, nightly + event-driven) ═══════════════
  sources ─► connectors ─► parse (PDF/HTML/code) ─► clean & dedup
        ─► CHUNK (structure-aware, 512–1024 tokens, 15% overlap)
        ─► attach metadata: source, URL, author, date, ACL/permission tags ⭐
        ─► embed (a self-hosted embedding model)
        ─► write to: vector index (HNSW) + BM25 index + a metadata store
        ─► incremental updates by document version; tombstone deletions ⚠️

 ═══════════════════════ SERVING (online) ═══════════════════════════════
   user question + identity
        │
        ▼
  ┌──────────────────────────────────────────┐
  │ GUARDRAIL IN: PII/abuse check, rate limit │
  └───────────────┬──────────────────────────┘
                  ▼
  ┌──────────────────────────────────────────┐
  │ QUERY PROCESSING                          │
  │  rewrite with conversation history,       │
  │  expand/decompose multi-part questions     │
  └───────────────┬──────────────────────────┘
                  ▼
  ┌──────────────────────────────────────────┐
  │ RETRIEVAL (hybrid)                        │
  │  dense (top 50) + BM25 (top 50)           │
  │  ⭐ ACL FILTER APPLIED IN THE QUERY,       │
  │     not after — never retrieve what the    │
  │     user may not see ⚠️⚠️                  │
  │  fuse with RRF                            │
  └───────────────┬──────────────────────────┘
                  ▼
  ┌──────────────────────────────────────────┐
  │ RERANK  cross-encoder, 100 → top 5-8      │  ⭐ biggest quality win per rupee
  └───────────────┬──────────────────────────┘
                  ▼
  ┌──────────────────────────────────────────┐
  │ PROMPT ASSEMBLY                           │
  │  system rules + numbered context chunks   │
  │  with source ids + question; instruct:    │
  │  "answer only from the context; cite      │
  │  chunk ids; say you don't know otherwise" │
  └───────────────┬──────────────────────────┘
                  ▼
  ┌──────────────────────────────────────────┐
  │ LLM (streamed)                            │
  └───────────────┬──────────────────────────┘
                  ▼
  ┌──────────────────────────────────────────┐
  │ POST: verify each citation resolves;      │
  │  optional groundedness check; PII redact;  │
  │  log everything; collect feedback ⭐       │
  └──────────────────────────────────────────┘
```

---

## 4. The decisions that carry the round ⭐⭐⭐

**(a) Permissions.** Filter at retrieval time using the user's ACL groups as a metadata predicate
in the vector and BM25 queries. Never retrieve a chunk the user cannot see and rely on the model to
withhold it — prompts are not a security boundary. Re-check permissions at render time, because
ACLs change. Index permission metadata with each chunk and re-index on ACL change. ⭐⭐⭐

**(b) Chunking.** Structure-aware (split on headings, keep tables and code blocks intact), 512–1024
tokens with overlap, plus **small-to-big**: embed small precise chunks but pass the enclosing
section to the LLM. Prepend a one-line document summary to each chunk (contextual retrieval) so a
chunk is interpretable out of context. ⭐⭐

**(c) Reranking.** A cross-encoder over 100 candidates down to 5–8 is consistently the single
largest quality improvement per unit of cost. Mention it unprompted.

**(d) Freshness.** Event-driven incremental indexing on document change, with tombstones for
deletions; show the document date in the citation so stale answers are visible. ⚠️ A deleted
document that stays in the index is both a correctness and a security bug.

**(e) When not to use RAG.** If the answer needs aggregation over structured data ("how many
tickets did team X close last quarter?"), route to **text-to-SQL** over the warehouse instead —
RAG over documents cannot count. A router that classifies the question type (document lookup,
structured query, action, small talk) is the mature design. ⭐⭐

---

## 5. Evaluation ⭐⭐

```
Build a golden set of 200–500 real questions with expert answers and the correct source documents.

retrieval  : recall@k (was the right chunk retrieved at all?), MRR
generation : faithfulness (every claim supported by the context — LLM-judged and
             spot-checked by humans), answer relevance, citation precision
end-to-end : human or calibrated LLM-judge score; thumbs-up rate in production
safety     : permission-violation tests (a user asks for a document they cannot see —
             must refuse and must not have retrieved it) ⭐
regression : run the whole suite on every prompt, model or index change ⭐
```

⚠️ Diagnose failures stage by stage: if the answer is wrong, first check whether the right chunk
was retrieved. Most "hallucinations" are retrieval failures.

---

## 6. Cost and latency ⭐⭐

```
20k questions/day × (4k context tokens + 400 output tokens)
  input  : 80M tokens/day
  output :  8M tokens/day
With a self-hosted 8B model on 2 GPUs (~$5k/month), this is comfortable;
with a large API model at $3/$15 per M it is ~$360/day ≈ $11k/month.

Latency budget (10 s):
  retrieval 150 ms | rerank 200 ms | prompt build 50 ms | TTFT ~1 s | stream the rest
Levers: cache embeddings; semantic cache for repeated questions ⭐; prefix-cache the
system prompt; route simple questions to a smaller model; cap context length.
```

---

## 7. Risks ⭐⭐

| Risk | Mitigation |
|---|---|
| **Prompt injection from documents** ⚠️⭐ | treat all retrieved text as untrusted data; never give the model tools with write access based on document content; strip instruction-like patterns; keep tool permissions outside the model |
| Permission leakage | ACL filtering at retrieval, re-check at render, automated permission tests |
| Hallucination | grounding + citations + "I don't know" + a faithfulness check |
| Stale answers | incremental indexing, tombstones, show dates |
| Over-reliance | show sources prominently; make it easy to open the original |
| Cost blowout | per-user quotas, caching, model routing, cost alerts |
| Silent model change | pin the model version; run the regression suite on every change ⚠️ |

---

## 8. Roadmap (a good closing)

```
v0  hybrid retrieval + a good prompt + citations, on one high-value document set
v1  reranking, structure-aware chunking, feedback collection, an evaluation suite
v2  query routing (documents vs SQL vs actions), conversation memory
v3  a finetuned small model for style and format; a distilled reranker for cost
v4  agentic multi-step research for complex questions, with strict budgets
```

---

## Recall questions

1. Draw the full indexing and serving pipeline.
2. Where exactly must permissions be enforced, and why not in the prompt?
3. Which single change usually gives the biggest quality gain, and why?
4. Explain small-to-big and contextual chunking.
5. How do you evaluate retrieval and generation separately?
6. When should a question be routed away from RAG?
7. How do you handle deleted documents?
8. What is prompt injection here, and what are the correct defences?
9. Estimate cost and latency for 20k questions/day.
10. Sketch a four-stage roadmap.
