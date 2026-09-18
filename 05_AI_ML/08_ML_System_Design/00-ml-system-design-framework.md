
# ML System Design — The Framework ⭐⭐⭐

> **Core idea in 3 lines**
> 1. An ML design round tests whether you can turn a vague product ask into a measurable ML
>    problem, a data plan, a model plan and a serving plan — with trade-offs stated.
> 2. There is no single right answer; there is a right *structure*, and candidates fail by jumping
>    to the model.
> 3. Spend the first five minutes on requirements and metrics. Interviewers are explicitly grading
>    that.

---

## 1. The eight-step framework ⭐⭐⭐

```
 1. CLARIFY        scope, users, scale, latency, what "good" means
 2. METRICS        business metric → ML metric → guardrails
 3. DATA           sources, labels, volume, splits, leakage, privacy
 4. FEATURES       what signals exist; how they are computed and served
 5. MODEL          baseline → simple → advanced; loss; training strategy
 6. EVALUATION     offline (with slices) and online (A/B)
 7. SERVING        architecture, latency budget, scale, cost
 8. MONITOR/ITERATE  drift, feedback loops, retraining, failure modes
```

Say the structure out loud at the start: *"I'll clarify requirements, define metrics, then go
through data, features, model, evaluation, serving and monitoring — and I'll flag trade-offs as I
go."* ⭐

---

## 2. Step 1 — Clarify (5 minutes, always) ⭐⭐

Questions to actually ask:

```
Users and scale:   how many users/items/requests per second? peak vs average?
Latency:           real-time (<100 ms), near-real-time, or batch?
Freshness:         must the model see events from seconds ago?
Objective:         what does the business want to increase? what is the cost of a mistake?
Existing system:   what is there now (a rule? a model?) and what is its performance?
Data:              what is logged today? are labels available, and with what delay?
Constraints:       privacy/regulation, on-device, multilingual, cold start, budget
```

⚠️ Never skip this. Designing a real-time system when a nightly batch job suffices — or vice versa —
is the most common way to fail the round.

---

## 3. Step 2 — Metrics ⭐⭐⭐

```
BUSINESS metric   what the company cares about: revenue, retention, watch time,
                  fraud loss, support deflection
      │  (must be connected by an argument, not assumed)
      ▼
ML metric         the offline proxy you can optimise: AUC, NDCG@10, recall@precision-0.9,
                  RMSE, groundedness
      │
      ▼
GUARDRAILS        must not get worse: latency p99, cost/request, diversity, fairness
                  across segments, complaint rate, safety violations
```

State the cost asymmetry explicitly. "A missed fraud costs ₹5 000; a false alarm costs 2 minutes of
support time and some customer annoyance — so I will optimise recall at a precision floor and set
the threshold by expected cost." That sentence alone lifts a candidate. ⭐⭐⭐

---

## 4. Step 3–4 — Data and features ⭐⭐

```
Sources        : logs, transactions, user profiles, content metadata, third-party
Labels         : explicit (ratings, reports) vs implicit (clicks, dwell time, purchases)
                 ⚠️ implicit labels are biased — position bias, presentation bias,
                 and you never observe the counterfactual
Volume/skew    : how many positives? is it 0.1% or 30%?
Splits         : time-based for anything temporal; grouped for repeated entities
Leakage audit  : any feature computed after the label? point-in-time correctness?
Privacy        : PII minimisation, consent, retention limits, regional residency
```

**Feature families to enumerate** (this is a checklist worth memorising):

```
user      : demographics, tenure, historical aggregates (7/30/90-day), embeddings
item      : category, price, age, quality signals, embeddings
context   : time of day, day of week, device, location, session position
interaction: user×item history, co-occurrence, similarity scores
graph     : social/network features, neighbourhood aggregates
text/image: embeddings from a pretrained encoder
counters  : real-time aggregates (last 5 min) via a streaming store
```

⚠️ Always mention: cold start (new users/items), the online/offline computation split, and how each
feature is available at serving time.

---

## 5. Step 5 — Model ⭐⭐

**Always start with a baseline.** Popularity ranking, a hand-written rule, logistic regression on a
few features. It sets the bar, ships fast, and reveals data problems.

```
 baseline (rule / popularity / logistic regression)
      ▼
 strong tabular model (gradient boosting) — usually the best accuracy/effort ratio
      ▼
 deep model (two-tower, sequence model, transformer) when embeddings, text, images
 or very large interaction data justify it
```

**The two-stage pattern** — use it whenever the candidate set is large (recommendation, search, ads,
retrieval):

```
 millions of items
        │
        ▼
 ┌────────────────┐   CANDIDATE GENERATION / RETRIEVAL
 │ cheap, high    │   two-tower embeddings + ANN, co-visitation, popularity, filters
 │ recall, ~10 ms │   → a few hundred candidates
 └───────┬────────┘
         ▼
 ┌────────────────┐   RANKING
 │ expensive, high│   gradient boosting or a deep model with rich cross features
 │ precision      │   → ordered list
 └───────┬────────┘
         ▼
 ┌────────────────┐   RE-RANKING / BUSINESS LOGIC
 │ diversity, freshness, dedup, policy filters, ads blending
 └────────────────┘
```

⭐⭐⭐ This diagram answers half of all ML system design questions. Know why the split exists:
scoring millions of items with an expensive model is impossible in 100 ms, and the retrieval stage
only needs recall, not precise ordering.

---

## 6. Step 6 — Evaluation

```
offline : the ML metric, per slice (new users, rare classes, regions, devices)
          plus a counterfactual/replay evaluation for ranking systems
online  : A/B test on the business metric with guardrails, powered and pre-registered
          (see 02_Probability_and_Statistics/04-hypothesis-testing-and-ab.md)
staged  : shadow → 1% → 5% → 50% → 100%
```

⚠️ Expect the follow-up "offline improved, online did not — why?" Have the four reasons ready:
skew, metric misalignment, distribution shift, and the prediction not actually changing behaviour.

---

## 7. Step 7 — Serving

```
latency budget breakdown; batch vs online; feature store; model format;
horizontal scaling; caching; fallbacks; cost per 1k requests
```

Do a **back-of-envelope estimate**. It is expected, and it is easy marks:

```
1M DAU × 20 requests/day = 20M requests/day ≈ 230 rps average, ~1000 rps peak
at 20 ms/request per core → ~20 cores at peak, plus headroom → ~40 cores
feature store: 20M × 200 features × 4 B ≈ 16 GB/day of reads
storage: 20M predictions/day × 200 B ≈ 4 GB/day of logs → 1.5 TB/year
```

---

## 8. Step 8 — Monitoring and iteration

Drift, feedback loops, retraining cadence, failure modes and the rollback plan (see the MLOps
folder). Close with what you would do next and what you would measure to know it worked.

---

## 9. Scoring rubric — what interviewers grade ⭐⭐

| Signal | Weak | Strong |
|---|---|---|
| Requirements | jumps straight to the model | asks about scale, latency, cost of errors |
| Metrics | "accuracy" | business → ML → guardrails, with cost asymmetry |
| Data | assumes clean labels | discusses label bias, leakage, delay, splits |
| Model | names the trendiest architecture | baseline first, justifies each step up |
| Trade-offs | one solution | states alternatives and why this one |
| Production | stops at the model | serving, monitoring, retraining, failure modes |
| Communication | monologue | checks in, draws, adapts to hints ⭐ |

⚠️ **Take the interviewer's hints.** If they ask "what if the item catalogue is 100M?", they are
telling you to introduce approximate nearest-neighbour retrieval.

---

## 10. Reusable phrases ⭐

- *"Let me start with a baseline so we have something to beat."*
- *"I'd split this into retrieval and ranking because scoring 100M items in 100 ms is impossible."*
- *"The cost of a false negative here is much higher than a false positive, so I'd optimise recall
  at a precision floor."*
- *"This must be a time-based split; a random split would leak the future."*
- *"I'd ship it in shadow mode first to validate the pipeline with zero user risk."*
- *"The risk here is a feedback loop, so I'd hold out a slice of traffic served by a different
  policy."*

---

## Recall questions

1. Recite the eight steps.
2. Which questions do you ask in the clarification phase?
3. Explain the business → ML → guardrail metric chain with an example.
4. Draw the retrieval/ranking/re-ranking architecture and justify the split.
5. List the seven feature families.
6. Why start with a baseline?
7. Give four reasons offline gains do not appear online.
8. Do a back-of-envelope sizing for 1M DAU and 20 requests/user/day.
9. What separates a weak answer from a strong one on metrics and on data?
10. Name three phrases that signal production experience.
