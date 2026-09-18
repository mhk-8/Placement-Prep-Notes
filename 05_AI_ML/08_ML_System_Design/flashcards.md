
# ML System Design — Flashcards

---

## Questions

1. The eight steps of the framework.
2. Seven clarifying questions to ask in the first five minutes.
3. The business → ML → guardrail metric chain.
4. Why state cost asymmetry, and how does it set the threshold?
5. The seven feature families.
6. Why start with a baseline?
7. Draw retrieval → ranking → re-ranking and justify the split.
8. Two-tower retrieval: structure, training, and why it is cheap.
9. Why can't the retrieval towers use cross features?
10. Why is CTR alone a bad recommender objective?
11. Position bias: what it is and three corrections.
12. Cold start for new users and new items.
13. Four reasons offline gains do not appear online.
14. Interleaving and why it beats an A/B test for search.
15. Hybrid retrieval and reciprocal rank fusion.
16. Pointwise / pairwise / listwise LTR.
17. Zero-result rate and its fixes.
18. Fraud: why PR-AUC, and the expected-cost threshold rule.
19. Why three outcomes in fraud, not two?
20. Velocity features and how they leak.
21. Why rules alongside a model in fraud?
22. Adversarial drift and how you respond to it.
23. RAG: where permissions must be enforced.
24. The single biggest quality lever in RAG.
25. Small-to-big and contextual chunking.
26. When to route away from RAG.
27. Prompt injection defences.
28. Churn: why uplift modelling?
29. CTR: why calibration matters more than AUC.
30. Wide & Deep: what each half does.
31. Moderation cascade.
32. Why quantile loss for ETA.
33. Edge compression techniques in order.
34. The cross-cutting design checklist.
35. Back-of-envelope sizing for 1M DAU × 20 requests.

---

## Answers

1. Clarify → metrics → data → features → model → evaluation → serving → monitoring.

2. Scale (users, items, qps), latency and freshness requirements, the business objective and cost
   of each error, the existing baseline, what data and labels exist and with what delay,
   constraints (privacy, regulation, device, budget), and what the prediction will actually be used
   for.

3. Business metric (revenue, retention, fraud loss) → an offline ML proxy (NDCG, PR-AUC, RMSE) →
   guardrails that must not regress (latency, cost, diversity, fairness, complaints).

4. Because the optimal decision threshold minimises `C_FP·FP + C_FN·FN`, not the error count;
   stating the costs turns metric choice into a derivation rather than a preference.

5. User, item, context, interaction, graph, text/image embeddings, real-time counters.

6. It sets the bar, ships quickly, exposes data problems, and prevents you from attributing gains
   to the model that a rule would also have delivered.

7. Millions of items → cheap high-recall retrieval (~hundreds of candidates) → expensive
   high-precision ranking → business re-ranking. The split exists because an expensive model cannot
   score millions of items inside the latency budget.

8. Separate user and item MLPs producing embeddings scored by a dot product; trained with
   sampled/in-batch softmax negatives. Item embeddings are precomputed and ANN-indexed, so serving
   costs one user-tower forward pass plus a lookup.

9. Because the item side must be precomputable independently of the query; any user×item
   interaction feature would force per-pair computation and destroy the ANN index.

10. It rewards clickbait; the strong answer is a multi-objective score combining predicted click,
    watch-time, positive engagement and negative signals, with weights tuned by A/B tests against
    retention.

11. Items in higher positions are clicked more regardless of quality. Corrections: include position
    as a training feature and fix it at inference; inverse-propensity weighting; randomised
    exploration slots.

12. New users: onboarding preferences, demographic and regional priors, popularity, fast online
    adaptation, contextual bandits. New items: content-based embeddings plus a guaranteed
    exploration impression budget.

13. Training–serving skew; the offline metric not aligned with the business metric; distribution
    shift; the prediction not changing any action; plus leakage or an underpowered test.

14. It mixes two rankers' results within the same session, removing between-user variance, so it
    detects smaller differences with far less traffic.

15. BM25 for exact and rare terms plus dense embeddings for semantics, combined with
    `RRF = Σ 1/(k + rank_i)`.

16. Pointwise predicts a per-item score; pairwise learns relative order; listwise optimises the
    list metric directly (LambdaMART weights pairs by the NDCG change of swapping them).

17. The fraction of queries returning nothing; fixed by spelling correction, synonym expansion,
    progressive filter relaxation and semantic retrieval — not by better ranking.

18. Positives are ~0.1%, so FPR barely moves and ROC-AUC flatters the model; PR-AUC reflects the
    flagged set. Threshold: minimise `C_FN·(fraud value) + C_FP·(declined good)`, which makes the
    threshold value-dependent.

19. A step-up (OTP/3-DS) converts would-be false declines into mild friction, which is far cheaper
    than losing a good customer.

20. Counts and sums over recent windows per card/device/IP/merchant. They leak if computed from a
    table that already includes the current transaction — use point-in-time-correct streaming
    aggregates shared by training and serving.

21. Rules give instant, auditable response to a new attack pattern and hard compliance guarantees;
    the model generalises to unseen patterns. Neither alone is sufficient.

22. Attackers deliberately cause concept drift and probe the boundary. Respond with frequent
    retraining, cohort-level score monitoring, secret/randomised thresholds, ensembles, and analysts
    who can push a rule in minutes.

23. At retrieval time, as a metadata filter in both the vector and lexical queries, re-checked at
    render time. The prompt is not a security boundary.

24. Adding a cross-encoder reranker over the top ~100 candidates.

25. Embed small precise chunks but pass the enclosing parent section to the model; prepend a short
    document-level summary to each chunk so it is interpretable out of context.

26. When the question needs aggregation or computation over structured data — route to text-to-SQL
    over the warehouse instead; a question-type router is the mature design.

27. Treat retrieved text as untrusted data, keep tool permissions and ACLs outside the model,
    sandbox and validate tool inputs, require human approval for destructive actions, never place
    secrets in prompts.

28. Because some customers would stay regardless and some are provoked by contact; the value comes
    from the treatment effect, validated against a randomised holdout.

29. The predicted probability is multiplied by the bid to price the auction, so miscalibration
    directly misprices; ranking quality alone does not guarantee correct absolute values.

30. Wide memorises frequent explicit feature crosses; deep generalises to unseen combinations via
    embeddings.

31. Hash-match known content → a cheap classifier → an expensive multimodal model on uncertain
    cases → human reviewers, routed by expected value and policy severity.

32. Being late is far more costly than being early, so predict an upper quantile (70th–80th) using
    pinball loss rather than the mean.

33. Quantisation (int8 PTQ, then QAT if needed) → structured pruning → distillation from a larger
    teacher, always measured on the real device.

34. What action follows; cost asymmetry; time-based and grouped splits; feature availability at
    prediction time; the baseline; feedback loops; the fallback when the model is down; how you
    detect breakage; and who is harmed, measured per segment.

35. 20M requests/day ≈ 230 rps average and ~1 000 rps peak; at 20 ms per request that is ~20 cores
    at peak plus headroom, with roughly 4 GB/day of prediction logs.
