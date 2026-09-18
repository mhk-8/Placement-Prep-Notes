
# Case Study: Real-Time Fraud Detection ⭐⭐⭐

*"Design a system to detect fraudulent card transactions for a payments company."*

---

## 1. Clarify

```
Scale      : 10k transactions/second at peak, ~500M/day
Latency    : the decision must return in < 100 ms (it blocks the checkout) ⚠️
Base rate  : ~0.1% fraudulent — extreme imbalance ⭐
Actions    : approve / decline / step-up (OTP or 3-D Secure) — three outcomes, not two ⭐
Labels     : chargebacks arrive 30–90 days later; manual review labels arrive in hours
Constraints: regulatory explainability (reason codes), PII handling, adversarial attackers
```

---

## 2. Metrics ⭐⭐⭐

```
BUSINESS  : fraud loss (₹) prevented, false-decline rate (a declined good customer is
            expensive and churns), review-team cost
ML        : PR-AUC / average precision; recall at a fixed precision; recall at a fixed
            review budget  ⚠️ NOT accuracy, NOT ROC-AUC alone (0.1% positives)
GUARDRAILS: p99 latency, decline rate by segment, customer complaints, fairness across
            regions and card types
```

**Expected-cost framing** — the strongest answer:

```
cost(threshold) = C_FN · (missed fraud value) + C_FP · (declined good transactions)
choose the threshold that minimises it; note that C_FN scales with TRANSACTION VALUE,
so the threshold should be value-dependent — decline a ₹200 000 transaction at a lower
score than a ₹200 one. ⭐⭐⭐
```

The three-outcome design matters: instead of a single cut, use **two thresholds** — approve below
`t₁`, step-up between `t₁` and `t₂`, decline above `t₂`. Step-up converts many potential false
declines into a small amount of friction.

---

## 3. Data and features ⭐⭐

```
Transaction : amount, currency, merchant, MCC category, channel, timestamp
Card/user   : tenure, historical spend profile, average/σ of amount, usual categories
Device/net  : device fingerprint, IP, geolocation, is-proxy, browser entropy
Velocity ⭐ : counts and sums over the last 1 min / 5 min / 1 h / 24 h / 7 d for
              (card, device, IP, merchant, billing address)
Deviation   : amount vs the user's 30-day mean/σ; distance from usual location;
              time since last transaction; impossible-travel flag
Graph ⭐⭐  : shared device/IP/address links between accounts — fraud rings show up as
              dense subgraphs; features = component size, neighbour fraud rate
Merchant    : historical fraud rate, risk tier
```

⚠️ **Velocity features must be point-in-time correct.** Computing "transactions in the last hour"
from a table that already contains the current transaction is leakage. Use a streaming aggregate
store (Kafka + Flink → Redis) so the same computation runs in training and serving.

⚠️ **Label delay:** train on transactions old enough for chargebacks to have settled, and use
manual-review labels to get a faster (biased) signal. State the bias: reviewed transactions are not
a random sample.

---

## 4. Architecture ⭐⭐

```
 transaction
      │
      ▼
┌────────────────────┐  < 5 ms
│ 1. RULES (hard)    │  sanctions list, blocked BINs, obvious velocity limits
│    deterministic,  │  auditable, instantly updatable when an attack starts ⭐
│    fail-closed     │
└─────────┬──────────┘
          ▼
┌────────────────────┐  ~10 ms
│ 2. FEATURE FETCH   │  Redis online store: velocity counters, user profile,
│                    │  device reputation, graph features
└─────────┬──────────┘
          ▼
┌────────────────────┐  ~20 ms
│ 3. MODEL           │  gradient-boosted trees (primary) + an anomaly score
│                    │  (isolation forest / autoencoder) for novel attacks ⭐
└─────────┬──────────┘
          ▼
┌────────────────────┐
│ 4. DECISION        │  value-aware thresholds → approve / step-up / decline
│                    │  + reason codes (SHAP top features) for compliance ⭐
└─────────┬──────────┘
          ▼
┌────────────────────┐
│ 5. FEEDBACK        │  manual review queue → labels; chargebacks → labels;
│                    │  everything logged for retraining and audit
└────────────────────┘
```

**Why gradient boosting and not a deep network:** tabular heterogeneous features, strong
non-linear interactions, fast CPU inference, robust to missing values, and far easier to explain —
plus it usually wins on accuracy for this data type. A deep model earns its place only for sequence
modelling of a card's transaction history or graph neural networks over the entity graph. ⭐⭐

**Hybrid rules + ML** is the right production answer: rules give instant response to a new attack
and hard compliance guarantees; the model generalises. ⭐

---

## 5. Handling imbalance ⭐⭐

Order of remedies (see `03_Classical_ML/07-metrics-and-evaluation.md`):

```
1. metric: PR-AUC, recall@precision
2. threshold: tuned by expected cost, value-dependent
3. loss: class weights / scale_pos_weight in XGBoost
4. data: undersample the negatives (keeping all positives) for training speed —
   ⚠️ then RECALIBRATE, because the base rate has changed
5. framing: add an unsupervised anomaly score for attacks with no labelled examples
```

---

## 6. The adversarial dimension ⭐⭐⭐ (the thing that makes fraud special)

```
Attackers ADAPT.  This is concept drift caused deliberately and continuously.
  • retrain frequently (daily/weekly), with fast-path model updates
  • monitor for sudden score-distribution changes per merchant/BIN/device cohort
  • keep some features and thresholds secret/randomised so they cannot be probed
  • watch for "card testing": many small transactions to validate stolen cards ⭐
  • expect probing — attackers submit transactions to learn the boundary
  • ensemble diverse models so a single exploited weakness is not fatal
  • human analysts in the loop, with tooling to push a rule within minutes
```

---

## 7. Evaluation and rollout

```
offline : PR-AUC and recall@precision on a strictly time-ordered split (train on the past,
          test on the future) ⚠️; evaluate by transaction-value-weighted loss, not row counts
shadow  : score live traffic without acting; compare with the incumbent
canary  : act on 1% of traffic; watch decline rate and complaint rate hourly
A/B     : measure fraud loss and false declines together; never one alone
```

---

## 8. Monitoring

```
score distribution per hour, per merchant, per BIN
decline rate and step-up rate by segment  ⚠️ a silent decline spike is a customer disaster
feature freshness and null rates (a stale velocity counter is worse than none)
review-queue volume and analyst agreement rate
fraud loss vs the counterfactual holdout   ⭐ keep a small always-approve holdout
   (where policy allows) to measure true fraud rates without the model's interference
```

---

## Recall questions

1. Why is accuracy — and even ROC-AUC — the wrong metric here?
2. Write the expected-cost threshold rule and explain why it should depend on transaction value.
3. Why three outcomes rather than two?
4. What are velocity features and how do you avoid leaking through them?
5. Why gradient boosting rather than a deep network?
6. Why keep a rules engine alongside the model?
7. What does the 30–90 day label delay mean for training and monitoring?
8. What is card testing, and how would you detect it?
9. Why is a time-ordered split mandatory?
10. What does a counterfactual holdout buy you?
