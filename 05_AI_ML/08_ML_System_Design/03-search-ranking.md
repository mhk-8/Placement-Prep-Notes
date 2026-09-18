
# Case Study: Search Ranking ⭐⭐

*"Design search for an e-commerce site with 100M products."*

---

## 1. Clarify

```
Scale     : 100M products, 50M queries/day, peak 5k qps
Latency   : < 300 ms end-to-end including retrieval and ranking
Objective : the user finds and buys what they want — purchases, not just clicks
Surface   : a results page of 40 items, with filters and facets
Constraints: multilingual queries, typos, new products, sponsored slots, stock status
```

---

## 2. Metrics ⭐⭐

```
BUSINESS  : conversion rate, revenue per search, search abandonment rate,
            queries-per-session (lower is better — it means the first result worked)
ML        : NDCG@10, MRR, recall@100 for retrieval
GUARDRAILS: latency, zero-result rate ⭐, diversity, sponsored/organic balance,
            seller fairness
```

⚠️ **Zero-result rate** is the metric candidates forget and interviewers love: a query that returns
nothing is a total failure, and it is fixed by query relaxation, spelling correction, and synonym
expansion rather than by better ranking.

---

## 3. The pipeline ⭐⭐⭐

```
  raw query "runing shoos men size 9"
        │
        ▼
┌──────────────────────────────────────────────────────┐
│ QUERY UNDERSTANDING                                   │
│  spell correction ("running shoes"), tokenisation,    │
│  language ID, entity/attribute extraction             │
│  (category=shoes, gender=men, size=9), intent         │
│  classification, synonym/query expansion              │
└─────────────────────┬────────────────────────────────┘
                      ▼
┌──────────────────────────────────────────────────────┐
│ RETRIEVAL  (recall-oriented, ~50 ms)                  │
│  • lexical: BM25 over an inverted index (Elasticsearch)│
│  • semantic: query embedding → ANN over product        │
│    embeddings (handles paraphrase, "shoes for jogging")│
│  • HYBRID: fuse both with reciprocal rank fusion ⭐    │
│  • hard filters: in stock, ships to region, extracted  │
│    attributes as filters                               │
│  → ~1000 candidates                                    │
└─────────────────────┬────────────────────────────────┘
                      ▼
┌──────────────────────────────────────────────────────┐
│ RANKING  (~100 ms)                                    │
│  learning-to-rank model (LambdaMART / a neural ranker) │
│  features: relevance (BM25 score, embedding cosine,    │
│  attribute match), popularity (CTR, sales velocity),   │
│  quality (rating, return rate), business (margin,      │
│  delivery speed, stock), personalisation (user history)│
└─────────────────────┬────────────────────────────────┘
                      ▼
┌──────────────────────────────────────────────────────┐
│ RE-RANKING / BLENDING                                 │
│  cross-encoder rerank of the top ~50 ⭐, diversity     │
│  across sellers and brands, sponsored slot insertion,  │
│  dedup of variants, freshness boosts                   │
└──────────────────────────────────────────────────────┘
```

**Why hybrid retrieval ⭐⭐⭐:** BM25 nails exact matches — model numbers, SKUs, brand names, rare
terms — where embeddings are weak; embeddings handle paraphrase, synonyms and intent, where BM25
fails. Neither alone is sufficient, and reciprocal rank fusion (`Σ 1/(k + rank_i)`) is the simple,
robust way to combine them.

---

## 4. Learning to rank ⭐⭐

```
Pointwise : predict relevance per item (regression/classification). Simple; ignores
            that ranking is about ORDER.
Pairwise  : learn which of two items should rank higher (RankNet). Closer to the task.
Listwise  : optimise the list metric directly (LambdaRank/LambdaMART, ListNet) ⭐
            — LambdaMART weights each pair by the NDCG change swapping them would cause,
            which is why it dominated LTR benchmarks for years.
```

**Labels:** human relevance judgements (expensive, high quality) plus click logs (cheap, biased).
⚠️ Click logs suffer position bias, presentation bias and selection bias — the user can only click
what was shown. Corrections: position-aware training features, inverse-propensity weighting, and
interleaving experiments (see below).

---

## 5. Evaluation ⭐⭐

```
offline     : NDCG@10 on a judged set; recall@1000 for retrieval; per-query-type slices
              (head vs tail, navigational vs exploratory) ⭐
interleaving: mix two rankers' results in one list and see which side gets clicks —
              far more sensitive than an A/B test and needs far less traffic ⭐⭐
online      : A/B on conversion and revenue per search with guardrails
```

Interleaving is the detail that marks out someone who has worked on search: because both rankers
are compared within the *same* user session, it removes between-user variance and can detect
smaller effects with an order of magnitude less traffic.

---

## 6. Hard cases ⭐

| Case | Handling |
|---|---|
| Tail queries (few or no clicks ever) | semantic retrieval and attribute matching carry it; lexical-only fails |
| Zero results | progressive relaxation of filters, spelling correction, synonym expansion, "did you mean" |
| New products | content-based embeddings, an exploration budget, category priors |
| Typos and multilingual | character-level spell correction, multilingual embedding models |
| Ambiguous intent ("apple") | diversify across interpretations; personalise by history |
| Sponsored results | a separate auction, blended by expected value; label them clearly, monitor the effect on organic conversion |

---

## 7. Serving and scale

```
50M queries/day ≈ 600 qps average, 5k peak
inverted index: sharded Elasticsearch/OpenSearch cluster
ANN index: 100M × 256 dims × 4 B ≈ 100 GB → product quantisation + sharding ⭐
caching: head queries are heavily repeated — cache the top results for popular queries
         with a short TTL (invalidate on stock/price change)  ⚠️
cross-encoder rerank: only on the top 50, and only if the latency budget allows
```

---

## 8. Monitoring

```
zero-result rate, click-through@1, conversion, latency percentiles,
query-volume shifts (a new product launch, a seasonal spike),
index freshness (how long until a price or stock change is searchable) ⚠️,
per-segment quality (language, region, device)
```

---

## Recall questions

1. Draw the four-stage search pipeline.
2. Why hybrid retrieval, and how do you fuse the two rankings?
3. Pointwise, pairwise, listwise LTR — what does each optimise?
4. What is LambdaMART's weighting insight?
5. Name three biases in click logs and one correction each.
6. What is interleaving and why is it more sensitive than an A/B test?
7. How do you fix a high zero-result rate?
8. Size the ANN index for 100M products at 256 dimensions.
9. Why is caching search results risky, and how do you do it safely?
10. Which guardrail metrics would you set?
