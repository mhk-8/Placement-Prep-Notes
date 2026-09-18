
# Scenario Questions ⭐⭐⭐

Open-ended "what would you do" questions. There is no single right answer — the grader is looking
for a structured diagnosis, stated assumptions, and awareness of production reality. Each answer
below is a template you can adapt.

---

## Debugging scenarios

**S1. "Your model gets 99.8% accuracy on the test set. What do you do?"**

Be suspicious, not pleased. In order: (i) check the class balance — 99.8% may be below the majority
baseline; (ii) audit for leakage, starting with the most important feature and asking whether it
could exist before the label; (iii) check for duplicates or near-duplicates across the split;
(iv) check the split is time-based and grouped if it should be; (v) confirm preprocessing was fitted
inside the folds; (vi) evaluate on a genuinely fresh sample. Only then report it.

**S2. "Training loss decreases but validation loss increases from epoch 3."**

Overfitting from epoch 3. Immediate: early stopping at the minimum; then stronger regularisation
(weight decay, dropout, augmentation), a smaller model, and more data if obtainable. Also check that
validation is not somehow harder or differently distributed, and that `model.eval()` is set so
dropout and BN behave correctly during validation.

**S3. "Training loss does not decrease at all."**

This is a bug until proven otherwise. Try to overfit 10 examples to near-zero loss — if that fails,
the fault is in the data, the loss or the forward pass. Check the loss at initialisation (`ln K` for
`K` balanced classes), the learning rate (run a range test), label alignment, `zero_grad()`,
whether the last layer is frozen, input normalisation, and dead ReLUs via per-layer gradient norms.

**S4. "Validation accuracy is higher than training accuracy."**

Usually not a bug: training loss is measured *during* updates with dropout and augmentation active,
while validation runs clean. Other causes: an easier validation split, a very small validation set
(high variance), or leakage into validation. Check the split sizes and composition first.

**S5. "The model works in the notebook but fails in production."**

Training–serving skew. Compare the exact feature vector produced by each path for the same row;
check preprocessing packaged with the model, library and tokeniser versions, missing-value handling,
category encoding for unseen levels, and `model.eval()`/BN running statistics. Add a CI skew test so
it cannot recur.

---

## Data scenarios

**S6. "You have 500 labelled examples and need a classifier."**

Start with transfer learning — a pretrained backbone with a linear probe, or a finetuned small
model; classical models on good features are also competitive at this size. Use heavy augmentation,
cross-validation rather than a single split (the variance of a 100-row test set is enormous), and
report confidence intervals. In parallel, plan how to get more labels: active learning on the
model's uncertain cases, weak supervision, and model-assisted pre-labelling with human review.

**S7. "30% of a critical feature is missing."**

First determine the mechanism: is it MCAR, MAR or MNAR? Look at whether missingness correlates with
the target — if it does, the indicator itself is a feature. Then choose: add a missing indicator and
impute (median or model-based, fitted in-fold), or use a model that handles missingness natively
(LightGBM/XGBoost). Investigate the upstream cause — a 30% null rate is often a pipeline bug rather
than a data property.

**S8. "Your dataset has 0.1% positives."**

Metric first: PR-AUC or recall at a precision floor, never accuracy. Then the threshold, set by
expected cost. Then class weights or focal loss. Only then resampling, inside the CV fold, with
recalibration afterwards because resampling changes the base rate. Consider framing it as anomaly
detection, and consider whether more positives can be obtained by targeted labelling.

**S9. "Labels are noisy — annotators disagree 20% of the time."**

Measure it: inter-annotator agreement (Cohen's/Fleiss' kappa) sets the ceiling on achievable
accuracy — say so explicitly. Improve the guidelines and re-train annotators; use multiple
annotators with majority vote or a Dawid–Skene model on the hard cases; use label smoothing and
robust losses; and evaluate on a small, carefully adjudicated gold set rather than on noisy labels.

**S10. "The data does not fit in memory."**

Push the aggregation into the warehouse and bring back only the feature table; use DuckDB or Polars
over Parquet; downcast dtypes and read only the needed columns; process in chunks or stream; sample
for exploration and use the full data only for the final fit. Reach for Spark/Dask only when those
fail.

---

## Product and deployment scenarios

**S11. "The business wants a model; you think a rule would do."**

Say so, with evidence. Build the rule as the baseline, measure it, and show what the model would
need to beat. Frame the trade-off: a rule is auditable, instant, free to serve and easy to change;
a model needs data, monitoring and retraining. If the model wins by enough to justify that cost,
build it. This answer is a strength, not a dodge.

**S12. "How would you decide whether to ship this model?"**

Offline: does it beat the incumbent on the primary metric *and* on every important slice, with a
confidence interval rather than a point estimate? Then shadow mode to validate the pipeline and
latency at zero risk, then a canary at 1–5% watching guardrails, then a properly powered A/B test on
the business metric run for at least two full weeks. Ship if the business metric moves and no
guardrail regresses — and only with a rollback plan.

**S13. "Model performance has dropped 10% since launch. Diagnose."**

Order the hypotheses by likelihood: (i) an upstream data change — check schema, null rates and
feature distributions first, because this is the most common cause; (ii) training–serving skew
introduced by a recent release; (iii) covariate drift — measure PSI per feature; (iv) concept drift
— check whether the relationship changed, using recent labels; (v) a feedback loop from the model's
own actions; (vi) a seasonal effect. Then act: fix the pipeline, retrain, or roll back.

**S14. "Latency is 300 ms; the budget is 100 ms."**

Profile the breakdown first — network, feature fetch, inference, serialisation. Feature retrieval is
often the largest share. Levers: cache or precompute features, batch requests, quantise or distil
the model, export to ONNX/TensorRT, reduce the candidate set before the expensive stage, add
replicas, and consider an asynchronous or two-tier design where a fast model answers and a slower
one refines.

**S15. "Your recommender keeps showing the same popular items."**

A feedback loop: the model trains on interactions it caused. Mitigations: exploration slots
(ε-greedy or Thompson sampling), diversity constraints and per-creator caps in re-ranking,
inverse-propensity weighting of training data, content-based retrieval so new items are reachable,
and a holdout slice served by a different policy to collect unbiased data.

---

## Design-ish scenarios

**S16. "Your team has one week to improve a model that is at 82% accuracy."**

Do error analysis before touching the model: sample 100 errors and categorise them. That tells you
whether the win is in data (label noise, a missing feature, an under-represented segment), in the
model, or in the threshold. Usually the largest wins in one week are better features, fixing a
broken segment, and tuning the decision threshold — not a new architecture.

**S17. "You must reduce inference cost by 80%."**

Quantise (int8/int4), distil into a smaller student, cache aggressively (exact and semantic),
route easy inputs to a small model and only hard ones to the large one, batch requests, prune,
shorten prompts or contexts for LLMs, and re-examine whether every request needs a model call at
all. Measure quality at each step against a fixed evaluation set and stop when it drops below the
bar.

**S18. "A stakeholder asks why the model rejected a specific application."**

Produce the local explanation — SHAP values for that prediction expressed as reason codes — plus the
feature values and how they compare with the population. Be honest about the limits: SHAP explains
the model's behaviour, not causation. Mention the governance angle: in regulated domains you need
documented reason codes, an appeal path, and fairness monitoring per protected group.

---

## How to answer any scenario ⭐⭐⭐

```
1. Restate the problem and state your assumptions out loud.
2. Ask one or two clarifying questions.
3. Give the structured list of hypotheses, ORDERED by likelihood.
4. Say what you would measure to distinguish them.
5. State the action for the most likely cause, and the fallback.
6. Close with how you would prevent a recurrence.
```

That six-step shape works for every scenario question, and using it consistently is itself the
signal being measured.
