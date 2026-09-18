
# Case Study: Recommendation System ⭐⭐⭐

*"Design the recommendation feed for a video app with 100M users and 50M videos."*

---

## 1. Clarify

```
Scale      : 100M MAU, ~20M DAU, 50M videos, ~10k new videos/hour
Surface    : the home feed — an ordered list of ~20 videos, refreshed per session
Latency    : < 200 ms end-to-end for the feed
Objective  : long-term engagement, not just clicks  ⭐
Constraints: cold start for new users and new videos; regional content rules;
             must not be a filter bubble
```

---

## 2. Metrics ⭐⭐

```
BUSINESS : daily/monthly retention, total watch time per user, sessions per week
ML       : NDCG@20 / recall@20 offline; watch-time-weighted ranking loss
GUARDRAILS: p99 latency, diversity (categories per session), freshness (share of
            content < 24h old), creator-side fairness, reported-content rate
```

⚠️ **Optimising click-through alone produces clickbait.** The standard strong answer is a
**multi-objective** target — a weighted combination of predicted click, predicted watch-time
fraction, predicted like/share, and negative signals (skip, "not interested", report) — with the
weights tuned by A/B tests against retention. Mentioning this unprompted is a big signal. ⭐⭐⭐

---

## 3. Data and labels

```
Events    : impression, click, watch duration, completion %, like, share, comment,
            skip, hide, follow  → a clickstream in Kafka
Positives : watched > 30 s or > 60% of duration (define it explicitly)
Negatives : impressed but not clicked (in-batch negatives for retrieval training) ⚠️
Metadata  : video topic, language, duration, creator, upload time, audio/video embeddings
```

⚠️ **Position bias:** items shown at the top get clicked more regardless of quality. Corrections:
add position as a feature at training and fix it to a constant at inference; inverse-propensity
weighting; randomised exploration slots to collect unbiased data. Raising this is a strong signal.
⭐⭐

⚠️ **Split by time, not randomly** — the feed is inherently temporal.

---

## 4. Architecture ⭐⭐⭐

```
  50M videos
      │
      ▼
┌───────────────────────────────────────────────────────────┐
│ CANDIDATE GENERATION  (~10 ms, recall-oriented)            │
│  • two-tower model: user tower / item tower → embeddings   │
│    trained with in-batch softmax (sampled) negatives       │
│  • ANN index (HNSW/ScaNN) over item embeddings             │
│  • plus: recent-watch collaborative signals, trending,     │
│    followed creators, fresh-content pool                   │
│  → ~500–1000 candidates from several sources, then dedup   │
└──────────────────────────┬────────────────────────────────┘
                           ▼
┌───────────────────────────────────────────────────────────┐
│ RANKING  (~50 ms, precision-oriented)                      │
│  multi-task model → p(click), E[watch time], p(like),      │
│  p(skip) with shared bottom layers (MMoE-style)            │
│  features: user × item cross features, sequence of the     │
│  last 50 watches, context, counters                        │
│  score = Σ wᵢ · headᵢ                                      │
└──────────────────────────┬────────────────────────────────┘
                           ▼
┌───────────────────────────────────────────────────────────┐
│ RE-RANKING                                                 │
│  diversity (MMR / per-creator and per-topic caps),         │
│  freshness injection, dedup of near-duplicates,            │
│  policy and regional filters, exploration slots (ε≈5%) ⭐  │
└───────────────────────────────────────────────────────────┘
```

**Two-tower retrieval ⭐⭐** — the key design to be able to explain:

```
 user features ──► user tower (MLP) ──► u ∈ R^d ┐
                                                 ├─ score = uᵀv  (dot product)
 item features ──► item tower (MLP) ──► v ∈ R^d ┘
```

Item embeddings are precomputed and indexed offline; at request time only the user tower runs, then
an ANN lookup. That is why it is cheap. The towers cannot use user×item cross features — which is
exactly the job of the ranking stage. Trained with sampled softmax / in-batch negatives, usually
with a log-Q correction for popularity bias. ⭐⭐⭐

---

## 5. Cold start ⭐⭐

```
New user : onboarding topic picks, demographic/geo priors, popularity by region,
           rapid online adaptation from the first few interactions; contextual bandit
New item : content-based embeddings (title, audio, video, creator history) so it can be
           retrieved before any interactions; an exploration budget that guarantees
           every new video gets some impressions ⭐
```

---

## 6. Serving and scale

```
1M concurrent users, ~20M feed requests/day ≈ 230 rps average, 1–2k peak
item embedding index: 50M × 128 dims × 4 B ≈ 25 GB → shard across nodes, or use PQ
user embeddings: computed per request (fresh) or cached for 10 minutes
ranking: ~1000 candidates × a small deep model; batch them in one forward pass
caching: feed cached per user for the session; invalidate on new interactions
```

---

## 7. Evaluation

```
offline : NDCG@20, recall@100 for retrieval; AUC and watch-time RMSE per head;
          replay/counterfactual evaluation with propensity weights
online  : A/B on retention and watch time, with diversity and report-rate guardrails;
          run at least two full weeks for novelty effects ⭐
```

---

## 8. Risks and failure modes ⭐⭐

| Risk | Mitigation |
|---|---|
| **Feedback loop / filter bubble** | exploration slots, diversity constraints, a holdout served by a different policy |
| Popularity bias | log-Q correction in sampled softmax, popularity-debiased sampling |
| Clickbait | multi-objective with negative feedback and watch-completion terms |
| Position bias | position feature + IPW + randomised slots |
| Stale embeddings | re-embed items hourly; refresh the ANN index incrementally |
| Creator fairness | impression floors, exposure monitoring per creator cohort |
| Harmful content | policy classifiers as a hard filter before ranking ⚠️ |

---

## Recall questions

1. Why two stages, and what is each stage optimised for?
2. Explain the two-tower model and why it makes retrieval cheap.
3. Why is CTR alone a bad objective, and what replaces it?
4. What is position bias and how do you correct for it?
5. How do you handle a brand-new video with zero interactions?
6. Size the ANN index for 50M items at 128 dimensions.
7. What is the filter-bubble failure mode, and how do you detect it?
8. Which guardrail metrics would you set before launching?
