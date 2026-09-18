
# MLOps — Flashcards

---

## Questions

1. The ML lifecycle in six stages; what fraction is modelling.
2. Batch vs online vs streaming vs edge — a use case each.
3. Shadow mode, canary, blue-green, A/B — what each gives you.
4. Where does an online latency budget actually go?
5. Five causes of training–serving skew.
6. How do you test for skew in CI?
7. Three problems a feature store solves; when it is overkill.
8. Point-in-time correctness — why it matters.
9. Four retraining triggers; the champion–challenger gate.
10. Feedback loops: example and two mitigations.
11. Four things to pin for reproducibility.
12. The five monitoring layers.
13. Covariate vs concept vs label drift.
14. The most common real cause of a drift alert.
15. PSI formula and thresholds; two alternative drift tests.
16. The domain-classifier drift test.
17. What to monitor when labels are delayed 90 days.
18. The reliability toolkit for a model call.
19. A sensible fallback chain.
20. Behavioural tests (CheckList) — the three types.
21. Fairness metrics and the impossibility result.
22. What belongs in a model card.
23. Global vs local explainability methods.
24. Where ML cost goes, and the biggest lever.
25. Cold start in serving — cause and mitigation.

---

## Answers

1. Problem framing → data → modelling → deployment → monitoring → retrain. Modelling is roughly
   10% of the work.

2. Batch: nightly churn scores. Online: fraud at checkout. Streaming: telemetry anomaly detection.
   Edge: on-device camera or keyboard prediction.

3. Shadow: zero-risk validation on live traffic. Canary: gradual exposure with guardrails. Blue-green:
   instant switch and rollback. A/B: a statistically valid measurement of the business effect.

4. Network + feature fetch + inference + serialisation/overhead; feature retrieval is frequently
   the largest share, not the model.

5. Duplicated feature logic in two codebases; batch-vs-realtime aggregation differences; time
   travel in training features; preprocessing not packaged with the model; different missing-value
   handling.

6. Push the same canonical row through the training and serving feature paths and assert the
   vectors are identical; re-score logged serving features offline and compare predictions.

7. Feature reuse, training–serving consistency, point-in-time-correct joins. Overkill for a small
   team with one or two models.

8. Training features must be computed from data that existed *at the prediction timestamp*;
   otherwise the model trains on future information it will never have in production.

9. Scheduled, performance-based, drift-based, data-volume-based. Promote the challenger only if it
   beats the champion on a fixed evaluation set.

10. The model's predictions shape the data it is later trained on — e.g. a recommender that never
    shows unpopular items. Mitigate with exploration/bandits, a differently-served holdout, and
    propensity weighting.

11. Code SHA, data version, configuration, environment image and library/CUDA versions.

12. System, data, model outputs, quality (once labels arrive), business.

13. Covariate: `P(X)` changes, relationship intact. Concept: `P(Y|X)` changes — the model is now
    wrong. Label/prior shift: `P(Y)` changes, so thresholds and calibration need updating.

14. An upstream data or pipeline bug — a renamed column, a unit change, a client release — not
    genuine population change.

15. `PSI = Σ (a − e) ln(a/e)`; `<0.1` stable, `0.1–0.25` moderate, `>0.25` significant.
    Alternatives: KS test, chi-square, JS divergence, Wasserstein distance.

16. Train a classifier to distinguish training data from live data; AUC near 0.5 means the
    distributions are indistinguishable, and high AUC localises drift via feature importance.

17. Input drift, prediction-score distribution, confidence/entropy, override and fallback rates,
    early proxy outcomes, and a small continuously labelled golden set.

18. Timeouts, bounded retries with backoff and jitter, circuit breakers, bulkheads, health checks,
    load shedding, canary with automatic rollback.

19. Smaller/faster model → cached prediction → heuristic rule → safe default; never an error page.

20. Invariance (irrelevant changes must not alter the output), directional expectation (a known
    change must move the output the right way), minimum functionality (unambiguous cases must be
    right).

21. Demographic parity, equal opportunity (equal TPR), equalised odds (equal TPR and FPR),
    calibration within groups. With unequal base rates, calibration and equalised odds cannot both
    hold, so the criterion must be chosen to match the harm.

22. Intended and out-of-scope use, training and evaluation data, sliced results, limitations and
    failure modes, ethical and fairness analysis, licence, version and contact.

23. Global: permutation importance, partial dependence, global SHAP. Local: SHAP values, LIME,
    counterfactual explanations, attention/saliency maps.

24. Training GPU-hours, serving (usually dominant long-run), storage/egress, and labelling — often
    the single biggest line item, reduced by active learning, weak supervision and model-assisted
    pre-labelling.

25. A new replica must load a large model before serving; mitigate with pre-warmed pools, readiness
    probes that fail during loading, smaller artefacts, lazy layers, and keeping a minimum replica
    count.
