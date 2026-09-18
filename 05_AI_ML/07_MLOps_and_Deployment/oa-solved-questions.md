
# MLOps — Solved Questions

20 scenario questions of the kind asked in applied-ML and MLE screens.

---

**Q1.** Your model's offline AUC is 0.85 but the online business metric does not move. Give four
possible reasons.

<details><summary>Answer</summary>

(i) Training–serving skew — different features online; (ii) the offline metric is not aligned with
the business objective (ranking quality vs conversion); (iii) distribution shift between the
training period and live traffic; (iv) the model's output is not acted on (threshold, UI placement,
downstream rule overrides it); (v) leakage inflated the offline number; (vi) the A/B test is
underpowered.
</details>

**Q2.** How would you detect training-serving skew?

<details><summary>Answer</summary>

Log the exact feature vector used at serving time, then re-compute features for the same rows with
the training pipeline and compare them value by value; any mismatch is skew. Add this as an
automated test in CI that pushes one canonical row through both paths. Complementary signals:
compare the distribution of serving features with the training distribution (PSI), and re-score
logged serving features offline to check the predictions match what was served.
</details>

**Q3.** PSI is 0.31 for one feature. What does that mean and what do you do?

<details><summary>Answer</summary>

`> 0.25` indicates significant population shift. Before retraining, check for an upstream pipeline
change (renamed column, unit change, a new data source, a client release). If the shift is genuine,
assess whether model quality has degraded on that segment and retrain with fresh data.
</details>

**Q4.** Labels for loan default arrive 6 months later. How do you monitor the model?

<details><summary>Answer</summary>

Monitor proxies: input drift (PSI per feature), prediction-score distribution, confidence/entropy,
approval rate, manual-override rate, and early indicators (first-payment default at 30 days). Keep a
continuously labelled golden set. Backfill true quality metrics as labels mature and compare with
the proxy signals.
</details>

**Q5.** Covariate drift or concept drift: fraudsters invent a new attack pattern.

<details><summary>Answer</summary>

**Concept drift** — `P(Y|X)` has changed, so the learned relationship is now wrong. Retraining on
fresh labels is required; input monitoring alone may not even show a change.
</details>

**Q6.** Recommend a deployment pattern: nightly product recommendations for 10M users.

<details><summary>Answer</summary>

**Batch.** Precompute the top-N per user overnight, write to a key-value store, serve by lookup.
Cheap, trivially scalable, easy to debug. Add a lightweight online re-ranker only if session
context matters.
</details>

**Q7.** Recommend a deployment pattern: fraud scoring at checkout.

<details><summary>Answer</summary>

**Online**, with a hard latency budget (say 50 ms p99), an online feature store for aggregates,
timeouts with a rule-based fallback, and a shadow-mode rollout before taking traffic.
</details>

**Q8.** What is shadow mode, and why start there?

<details><summary>Answer</summary>

The new model scores live traffic and its predictions are logged, but the old model's output is
served. It validates the pipeline, feature availability and latency under real traffic with zero
user risk, and it lets you compare predictions before any exposure.
</details>

**Q9.** Your recommender only ever recommends popular items. What is happening?

<details><summary>Answer</summary>

A feedback loop: the model is trained on interactions it caused, so unshown items never gather
data. Mitigations: exploration (ε-greedy, Thompson sampling), a holdout slice served by a different
policy, inverse-propensity weighting of the training data, and explicit diversity/novelty objectives.
</details>

**Q10.** What must be versioned for a reproducible training run?

<details><summary>Answer</summary>

Code (git SHA), data (snapshot or DVC/Delta version), configuration/hyperparameters, and the
environment (container digest, library and CUDA versions). Seeds alone are insufficient.
</details>

**Q11.** A feature's null rate jumps from 0.1% to 30% overnight. What do you do?

<details><summary>Answer</summary>

Treat it as an incident: check the upstream pipeline and any recent client or schema release; assess
the model's sensitivity to that feature; if it is important, either roll back to a model that does
not use it, serve the fallback path, or apply the documented imputation while the source is fixed.
Do not silently impute and carry on.
</details>

**Q12.** What is the champion–challenger pattern?

<details><summary>Answer</summary>

The current production model (champion) keeps serving while a new model (challenger) is evaluated
in shadow or on a small traffic slice. The challenger is promoted only if it beats the champion on
the agreed metrics, giving an automatic quality gate for retraining.
</details>

**Q13.** Your p99 latency is 400 ms against a 200 ms budget. Where do you look?

<details><summary>Answer</summary>

Break the budget down: network, feature fetch, model inference, serialisation, downstream calls.
Feature retrieval is often the culprit. Fixes: cache or precompute features, batch requests, use a
smaller/quantised model or ONNX/TensorRT, add replicas, and check for cold starts and GC pauses.
</details>

**Q14.** Give three behavioural tests for a sentiment classifier.

<details><summary>Answer</summary>

Invariance: replacing a person's name should not change the prediction. Directional: adding "and I
loved it" should not decrease the positive score. Minimum functionality: unambiguous cases
("this is terrible") must be classified correctly. (CheckList.)
</details>

**Q15.** Why can't you satisfy both calibration and equalised odds when base rates differ?

<details><summary>Answer</summary>

It is a proved impossibility (Kleinberg et al.; Chouldechova): with unequal base rates, a
classifier that is calibrated within each group cannot also equalise both true- and false-positive
rates, except in degenerate cases. Practically, you must decide which fairness criterion matches
the harm you are preventing.
</details>

**Q16.** Spiky traffic: 10 requests/minute most of the time, 5 000/minute for one hour a day. How
do you serve?

<details><summary>Answer</summary>

Autoscaling with a scale-to-zero or minimal baseline, queue-depth-based scaling (not CPU),
pre-warmed replicas ahead of the known peak, request batching, caching, and load shedding of
low-priority traffic. For very spiky patterns a serverless endpoint may be cheaper despite cold
starts.
</details>

**Q17.** A model trained 8 months ago still scores well offline on old data but users complain.
What do you suspect?

<details><summary>Answer</summary>

Staleness plus drift: the offline set is from the old distribution, so it cannot reveal the
problem. Re-evaluate on recent labelled data, compare feature distributions (PSI) between the
training window and now, and check for concept drift in the target relationship. Also check
whether an upstream pipeline change quietly altered a feature's meaning.
</details>

**Q18.** What goes in a model card?

<details><summary>Answer</summary>

Intended use and out-of-scope uses, training data and its provenance, evaluation data and results
**broken down by relevant slices**, known limitations and failure modes, ethical considerations,
fairness measurements, licence, contact and version.
</details>

**Q19.** How do you decide the retraining cadence?

<details><summary>Answer</summary>

Measure how fast quality decays: hold out models trained at earlier dates and score them on recent
data to get a degradation curve. Set the cadence where the expected loss from staleness exceeds
retraining cost, and add drift-based triggers between scheduled runs.
</details>

**Q20.** Your training pipeline takes 14 hours and fails at hour 12. What do you change?

<details><summary>Answer</summary>

Checkpoint regularly and make the pipeline resumable; split it into cacheable stages (data prep,
features, training, evaluation) so only the failed stage reruns; validate data up front so schema
errors fail in minutes, not hours; use spot instances with checkpointing; add retries and
alerting; run a small smoke-test configuration in CI on every change.
</details>

---

## Scoring

| Correct | Read as |
|---|---|
| 17–20 | Strong production judgement — this is what MLE interviews test |
| 12–16 | Reread `01` and `02` of this folder |
| < 12 | This folder is quick to learn and heavily rewarded; one focused day |
