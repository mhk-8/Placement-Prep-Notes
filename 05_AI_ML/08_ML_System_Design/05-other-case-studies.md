
# Case Studies in Brief ⭐⭐

Five more designs in compressed form. For each: the clarifying question that matters most, the
metric, the model choice, and the one trap.

---

## 1. Churn prediction ⭐⭐

*"Predict which subscribers will cancel next month."*

```
CLARIFY  : what counts as churn (cancellation, or 30 days inactive)? what ACTION follows
           a prediction? ⭐ — a churn score nobody acts on is worthless
METRICS  : business = retained revenue from the intervention;
           ML = recall at the top-k budget (you can only call 5 000 people),
           or lift@10%; ranking matters more than calibration ⚠️
DATA     : snapshot the features as of a prediction date T, label = churn in (T, T+30d].
           ⚠️ THE trap: a feature like `cancellation_reason` or `days_since_cancellation`
           leaks the label. Audit every feature for "could this exist before T?"
SPLIT    : by time — train on earlier cohorts, test on later ones
MODEL    : gradient boosting on aggregated behavioural features (usage trend, support
           tickets, payment failures, tenure, plan changes)
SERVING  : batch — score everyone nightly, write to a CRM table
TRAP ⭐  : predicting churn is not the goal; **uplift modelling** is — some customers
           would stay anyway, and contacting some actually triggers churn. Model the
           *treatment effect*, not the outcome, and validate with a randomised holdout.
```

---

## 2. Ad click-through-rate prediction ⭐⭐

```
CLARIFY  : auction type (second price), latency (< 50 ms), scale (100k qps)
METRICS  : business = revenue = Σ bid × pCTR; ML = log loss and CALIBRATION ⭐⭐
           — the absolute probability is multiplied by the bid, so a miscalibrated
           model directly misprices the auction; AUC alone is insufficient
DATA     : impressions and clicks; extreme imbalance (~0.1–2% CTR); massive
           high-cardinality categorical features (user id, ad id, publisher)
FEATURES : hashing trick / embeddings for high-cardinality ids; cross features
MODEL    : logistic regression with FTRL (classic, online-updatable), or
           Wide & Deep / DeepFM / DCN — "wide" memorises frequent feature crosses,
           "deep" generalises to unseen ones ⭐
SERVING  : sub-50 ms, huge qps; embedding tables sharded in a parameter server
TRAP     : position bias (top slots get clicks regardless) and the feedback loop —
           you only observe clicks on ads you chose to show; use exploration and IPW
```

---

## 3. Content moderation ⭐⭐

```
CLARIFY  : which policies? what volume? appeal process? human review capacity?
METRICS  : business = prevalence of violating content that users actually see;
           ML = recall at a fixed precision per policy (cost of a false negative varies
           enormously: spam vs child safety) ⭐; guardrails = false-removal rate,
           reviewer workload, latency
DATA     : user reports (biased), reviewer decisions (the labels), adversarial evasion
MODEL    : a cascade ⭐ — cheap hash matching for known bad content → a fast text/image
           classifier → an expensive multimodal model on the uncertain middle → humans
           on the top of the queue. Route by expected value of review.
SERVING  : real-time for posting; asynchronous re-scan for the back catalogue
TRAPS    : multilingual and code-switched content; adversarial evasion (leetspeak,
           image text, cropping); context dependence (the same words are abuse or
           quotation); severe class imbalance; reviewer wellbeing and label noise
```

---

## 4. ETA / delivery-time prediction ⭐

```
CLARIFY  : which leg (pickup, transit, total)? what does the user see? cost of being
           early vs late? ⭐
METRICS  : business = on-time rate and customer satisfaction; ML = MAE, plus
           QUANTILE loss ⭐⭐ — underestimating an ETA is far worse than overestimating,
           so predict the 70th–80th percentile, not the mean. Pinball loss expresses this
           directly.
DATA     : historical trips, GPS traces, road network, traffic, weather, courier profile,
           restaurant prep times
FEATURES : route distance and segment-level historical speeds, time of day, day of week,
           weather, real-time traffic, current courier load, historical store prep time
MODEL    : gradient boosting on engineered features (strong baseline), or a graph/sequence
           model over road segments; often a sum of separately-modelled legs
SERVING  : real-time at order placement; continuous updates during the trip
TRAP     : feedback loop — a quoted long ETA changes user behaviour (they cancel), so the
           observed data depends on the predictions. Also, the distribution is
           right-skewed: report percentiles, not just the mean.
```

---

## 5. Image classification at the edge ⭐

```
CLARIFY  : device (phone? microcontroller?), memory and power budget, offline use,
           latency, privacy
METRICS  : accuracy per class, plus latency, model size (MB), energy per inference ⭐
DATA     : collect from the ACTUAL deployment device — lab photos do not transfer ⚠️
MODEL    : MobileNetV3 / EfficientNet-Lite; then compress:
           quantisation (int8 PTQ, or QAT for a better accuracy/size trade-off) →
           pruning (structured, so it actually speeds up hardware) → distillation
           from a large teacher ⭐
DEPLOY   : ONNX Runtime / TFLite / Core ML / NPU delegates; measure on the real device,
           not on a server ⚠️
TRAP     : deployment shift — lighting, camera, motion blur, compression. Augment for the
           deployment conditions and monitor by shipping back a sampled, consented subset
           of inputs (or on-device metrics only, for privacy).
```

---

## Cross-cutting checklist for any case ⭐⭐⭐

```
□ What ACTION does a prediction trigger? (If none, it is not worth building.)
□ What is the cost asymmetry between error types?
□ Is the split time-based? Are entities grouped?
□ Is any feature unavailable — or different — at prediction time?
□ What is the baseline, and does the model beat it by enough to justify the complexity?
□ Is there a feedback loop? How do you keep collecting unbiased data?
□ What happens when the model is unavailable? (fallback)
□ How will you know it has broken? (monitoring, alert, runbook)
□ Who is harmed if it is wrong, and is that measured per segment?
```

---

## Recall questions

1. For churn, why is uplift modelling the right framing?
2. Why does CTR prediction require calibration and not just ranking quality?
3. What does the "wide" part of Wide & Deep do that the "deep" part does not?
4. Describe a moderation cascade and what decides the routing.
5. Why is quantile/pinball loss right for ETA prediction?
6. Name three compression techniques for edge deployment and their order.
7. Give the cross-cutting checklist from memory.
