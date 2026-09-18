
# Data Handling & Feature Engineering — Flashcards

---

## Questions

1. The EDA checklist.
2. MCAR / MAR / MNAR and what each implies.
3. When does a missing indicator help?
4. Three outlier detection methods; the judgement that decides treatment.
5. Which models need feature scaling?
6. Log transform: when and why.
7. One-hot vs ordinal vs target vs hashing encoding.
8. Smoothed target encoding formula; why out-of-fold.
9. Handling unseen categories at inference.
10. The dummy-variable trap.
11. The five tests a feature must pass.
12. Why ratios and personal-baseline features are strong.
13. Cyclical encoding of hour-of-day.
14. Why never feed a raw timestamp.
15. Window aggregate features and the point-in-time guard.
16. Filter / wrapper / embedded / permutation selection.
17. Why feature selection belongs inside CV.
18. What correlated features do to importance.
19. Define leakage; the five categories.
20. The tell-tale symptom of leakage and the first diagnostic.
21. Point-in-time correctness — the rule.
22. Why mutable current-state tables are dangerous.
23. Group leakage and when it applies.
24. The medical-imaging scanner example.
25. How leakage looks after deployment vs drift.
26. The eight engineering practices that prevent leakage.
27. Rolling window SQL that excludes the current row.
28. As-of join.
29. The three-CTE training-table pattern.
30. Three join traps.
31. Idempotency and backfill.
32. Batch vs streaming; the lambda skew problem.
33. Event time, processing time, watermark.
34. Parquet vs CSV; partitioning.
35. What to do when data does not fit in memory.

---

## Answers

1. Shape/dtypes; target distribution; missingness and its pattern; per-feature distributions,
   skew, outliers, cardinality; duplicates; correlations; temporal structure; repeated entities;
   sanity checks; segment sizes.

2. MCAR: missingness independent of everything — dropping is unbiased. MAR: depends on observed
   variables — imputation from other features is valid. MNAR: depends on the unobserved value —
   no imputation fixes it; add an indicator.

3. Almost always, and especially under MNAR, because the fact of being missing carries signal.

4. Z-score (assumes normality), IQR rule (robust), modified z-score with MAD, Isolation
   Forest/LOF for multivariate. Treat as error → fix/remove; rare-but-real → keep; otherwise clip,
   transform, or use a robust model.

5. Linear/logistic regression, SVM, k-NN, k-means, PCA, neural networks. Trees, random forests and
   gradient boosting do not.

6. For right-skewed positive variables (income, prices, counts) — it compresses the tail, makes the
   relationship more linear and stabilises variance. Use `log1p` for zeros.

7. One-hot for low cardinality; ordinal only for genuine order or tree models; target encoding for
   high cardinality with trees (out-of-fold); hashing for very high cardinality or streaming.

8. `(n_c·ȳ_c + m·ȳ_global)/(n_c + m)`. Out-of-fold, because a row contributing to its own encoding
   leaks the label directly — the most common leakage bug.

9. Define it explicitly: an "other" bucket, the global mean, or a hash. `handle_unknown="ignore"`
   in sklearn.

10. Including all `k` one-hot columns together with an intercept makes the design matrix singular;
    drop one level.

11. Availability at prediction time, point-in-time correctness, distributional stability, a
    plausible mechanism, and computability within the latency budget.

12. Because absolute values mean different things for different entities; "8× this user's median"
    normalises away the between-entity variation and isolates the anomaly.

13. `sin(2πh/24)`, `cos(2πh/24)` — places hours on a circle so 23:00 and 00:00 are adjacent.

14. It increases monotonically, so at serving time it takes values outside the training range and
    the model extrapolates. Use components and elapsed times instead.

15. Counts/sums/means over 1h, 24h, 7d, 30d per entity, plus ratios between windows and trends.
    The window must end strictly before the label's timestamp.

16. Filter: statistics vs the target, fast, ignores interactions. Wrapper: search with the model,
    expensive. Embedded: lasso or tree importances during training. Permutation: shuffle a column
    and measure the held-out drop — honest but slow and confused by correlation.

17. Because selecting on all the data uses test-set information, inflating the reported score.

18. They split importance between themselves, so a genuinely strong signal can appear weak;
    cluster correlated features and evaluate the group.

19. Information in training that will not be available at prediction time. Categories: target
    leakage, train–test contamination, temporal leakage, group leakage, metadata/artefact leakage.

20. Implausibly high performance, usually with one dominant feature. First diagnostic: drop that
    feature and re-evaluate, then audit its availability at prediction time.

21. Features are computed only from events strictly before the prediction timestamp; the label
    comes from a window after it.

22. They hold only the current value with no history, so joining them to an old training row
    injects today's (future) information. Use event logs or slowly-changing-dimension tables.

23. When rows repeat per entity (patient, user, device, document) — a random split lets the model
    memorise the entity, inflating the score. Use GroupKFold.

24. Diseased and control images came from different sites, so the model learned scanner artefacts
    rather than pathology; it failed on any new hospital. Fix: group/stratify by site and test on a
    held-out site.

25. Leakage produces an immediate, large gap between offline and online performance; drift produces
    a gradual decay over weeks or months.

26. Build from event logs; use as-of joins; pipeline all learned transforms; choose the split
    deliberately; deduplicate before splitting; hold out a one-use test set; add a CI skew test;
    review top feature importances with a domain expert.

27. `AVG(x) OVER (PARTITION BY id ORDER BY ts RANGE BETWEEN INTERVAL '30 days' PRECEDING AND
    INTERVAL '1 second' PRECEDING)` — the guard excludes the current row, which the default frame
    would include.

28. For each label event, a lateral subquery taking the latest feature row with
    `valid_from < label_ts`, ordered descending, limit 1.

29. `cohort` (eligible entities as of `t`) → `feat` (aggregates with `event_ts < t`) → `lab`
    (outcome in `(t, t+horizon]`), joined on the entity key.

30. Inner joins silently dropping rows and changing the cohort; one-to-many joins duplicating rows
    and inflating aggregates; NULL keys never matching. Check `COUNT(*)` before and after.

31. Idempotent: re-running the same partition produces the same result rather than duplicating
    data. Backfillable: history can be recomputed when logic changes — both require partitioned,
    deterministic jobs.

32. Batch for training data and batch scoring; streaming for real-time counters. Implementing the
    same feature twice in two engines is a leading cause of training–serving skew.

33. Event time is when the event happened; processing time is when it was handled. A watermark is
    the heuristic bound on lateness after which a window is finalised.

34. Parquet is columnar, typed and compressed with predicate pushdown, so reading a few columns is
    dramatically faster; partitioning by date lets queries skip whole directories.

35. Optimise dtypes and read only needed columns; use DuckDB/Polars over Parquet; push the
    aggregation into the warehouse and bring back only the feature table; only then reach for
    Spark/Dask.
