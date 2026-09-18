
# Monitoring, Drift and Reliability ⭐⭐

> **Core idea in 3 lines**
> 1. Models degrade silently — nothing throws an exception when the predictions become wrong.
> 2. Labels arrive late or never, so you must monitor *inputs* and *outputs* as proxies for
>    quality.
> 3. Distinguishing data drift from concept drift, and knowing what each implies, is the core
>    question in this area.

---

## 1. What to monitor ⭐⭐⭐

```
 LAYER 1 — SYSTEM          latency (p50/p95/p99), throughput, error rate, CPU/GPU,
                            memory, queue depth, cost per 1k requests
 LAYER 2 — DATA            schema violations, null rate, range violations, cardinality,
                            volume, freshness/staleness, feature drift  ⭐
 LAYER 3 — MODEL           prediction distribution, confidence distribution,
                            output drift, fraction of fallbacks/abstentions
 LAYER 4 — QUALITY         accuracy/AUC/RMSE once labels arrive, per segment
 LAYER 5 — BUSINESS        conversion, revenue, complaint rate, manual-override rate  ⭐
```

⭐ Layers 1–3 are available **immediately**; layer 4 is delayed by the label lag; layer 5 is what
actually matters. Structure your answer this way and you will sound like someone who has run a
system.

⚠️ Always monitor **per segment** (region, device, customer tier, new vs returning). A stable
aggregate routinely hides a broken segment.

---

## 2. Types of drift ⭐⭐⭐

```
Notation: P(X) inputs, P(Y|X) the relationship, P(Y) the label distribution.

DATA / COVARIATE DRIFT   P(X) changes, P(Y|X) unchanged
    a new user demographic, a new camera, a new marketing channel
    → the model may still be correct, but it is extrapolating; retraining usually helps

CONCEPT DRIFT            P(Y|X) changes  ⚠️ the dangerous one
    fraud tactics evolve; COVID changes what "normal" demand means;
    a competitor's price change alters purchase behaviour
    → the learned relationship is now WRONG; retraining on fresh labels is required

LABEL / PRIOR SHIFT      P(Y) changes
    the fraud rate rises from 0.1% to 1% → recalibrate thresholds

UPSTREAM DATA CHANGE     a pipeline bug, a renamed column, a unit change (cents→dollars),
    a third-party API silently altering its schema  ⚠️ by far the most common in practice
```

⭐ The distinction to state: **covariate drift changes the inputs; concept drift changes the rules.**
Only the latter guarantees your model is now wrong.

⚠️ In real systems, most "drift" alerts are upstream data bugs, not genuine distribution change.
Investigate the pipeline before retraining.

### Detecting drift

| Method | Data type | Note |
|---|---|---|
| **PSI** (population stability index) | any binned | `Σ (a − e)·ln(a/e)`; `< 0.1` stable, `0.1–0.25` moderate, `> 0.25` significant ⭐ |
| **KS test** | continuous | distribution-free; sensitive to large `n` ⚠️ |
| Chi-square | categorical | |
| KL / JS divergence | distributions | JS is symmetric and bounded |
| Wasserstein distance | continuous | robust, interpretable in the feature's units |
| Domain classifier | any | train a model to tell train from live data; AUC ≈ 0.5 means no drift ⭐ |

⚠️ With millions of rows, statistical tests flag drift that is real but irrelevant. Use effect
sizes (PSI, Wasserstein) and alert on *sustained* change, not a single window.

---

## 3. When labels are delayed or absent ⭐⭐

Common: loan default (months), churn (weeks), fraud chargebacks (30–90 days), medical outcomes
(years).

Proxies to monitor in the meantime:

```
prediction distribution shift    (score histogram vs the training distribution)
confidence/entropy trend         (rising uncertainty precedes quality loss)
input drift (PSI per feature)
rate of fallbacks, abstentions, human overrides   ⭐ the strongest early signal
user behaviour: click-through, dismissal, complaint rate
a small, continuously labelled sample ("golden set") for direct measurement ⭐
```

---

## 4. Alerting that people do not ignore ⭐

```
page   : the service is down, error rate > X%, p99 latency breached, cost spike
ticket : drift over threshold for N consecutive windows; quality metric down;
         schema change detected
dashboard only : everything else
```

⚠️ Alert fatigue is a real failure mode. Alert on sustained, actionable changes with clear
runbooks, not on every statistical blip.

---

## 5. Reliability engineering for ML ⭐⭐

```
Timeouts        every model call has one; never let a slow model hang a request
Retries         with exponential backoff and jitter; idempotent calls only
Circuit breaker trip after repeated failures; fail fast instead of cascading
Fallbacks       a smaller model → a cached prediction → a heuristic rule → a default value ⭐
Graceful degradation  a slightly worse answer beats an error page
Load shedding   drop low-priority traffic under pressure
Bulkheads       isolate the model service so its failure cannot take down the app
Health checks   liveness and readiness; readiness must fail while a model is loading
Canary + rollback  automatic rollback when guardrails trip
```

✅ The sentence to say: *"every model call is a dependency that can fail or be slow, so it needs a
timeout, a fallback and a circuit breaker like any other network call."*

---

## 6. Testing ML systems ⭐⭐

| Test | Checks |
|---|---|
| Data schema tests | types, ranges, nullability, cardinality (Great Expectations, Pandera) |
| Unit tests on transformations | feature functions are deterministic and correct |
| **Skew test** | the same row through the training and serving paths gives identical features ⭐ |
| Model quality gate | the new model beats the incumbent on a fixed evaluation set |
| **Behavioural tests** (CheckList) | invariance (a paraphrase should not change the answer), directional expectations (adding a positive word should not lower the sentiment score), minimum-functionality cases ⭐ |
| Slice tests | per-segment thresholds, not just the aggregate |
| Load/latency tests | p99 under expected and peak traffic |
| Integration tests | end-to-end from request to logged prediction |

---

## 7. Responsible ML ⭐⭐

```
Fairness   : measure per protected group. Metrics conflict: demographic parity
             (equal positive rates), equal opportunity (equal TPR), equalised odds
             (equal TPR and FPR), calibration within groups.
             ⚠️ It is mathematically IMPOSSIBLE to satisfy calibration and equalised
             odds simultaneously when base rates differ (the impossibility result) ⭐⭐
Explainability: global (permutation importance, PDP) and local (SHAP, LIME,
             counterfactuals); regulated domains may require reason codes
Privacy    : minimisation, anonymisation, differential privacy, federated learning;
             note that models can memorise training data (extraction attacks) ⚠️
Security   : adversarial examples, data poisoning, model stealing, prompt injection
Governance : model cards, datasheets for datasets, audit logs, human review for
             high-stakes decisions, documented recourse
```

⭐ Good framing: fairness is a **product and policy decision** informed by measurement, not a metric
you optimise blindly — the right criterion depends on the harm you are preventing.

---

## Recall questions

1. List the five monitoring layers and say which are available immediately.
2. Distinguish covariate drift, concept drift and label shift, with an example each.
3. What is the most common real cause of a drift alert?
4. Define PSI with its thresholds; name two alternatives.
5. What do you monitor when labels take 90 days to arrive?
6. Describe the domain-classifier drift test.
7. Give the reliability toolkit for a model call and the fallback chain.
8. What is a skew test and where does it live?
9. Give three behavioural tests for an NLP model.
10. State the fairness impossibility result and its practical implication.
