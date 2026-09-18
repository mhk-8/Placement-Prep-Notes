
# Data Leakage — The P1 Topic ⭐⭐⭐

> **Core idea in 3 lines**
> 1. Leakage is any information in the training data that will not be available — or would not have
>    existed — at prediction time.
> 2. It is the single most common cause of a model that looks excellent offline and fails in
>    production, and interviewers test it constantly.
> 3. The defence is a habit, not a tool: for every feature and every split, ask *"would I know this,
>    with this value, at the moment of prediction?"*

---

## 1. The taxonomy ⭐⭐⭐

### (a) Target leakage — a feature contains the answer

```
churn model with `cancellation_reason`            ← only exists AFTER churning
loan default with `total_amount_recovered`        ← only exists after default
disease prediction with `prescribed_treatment`    ← the treatment follows the diagnosis
fraud with `chargeback_filed`                     ← that IS the label
conversion model with `order_id`                  ← non-null only for converters ⚠️
```

**The tell:** one feature has overwhelming importance and the metric is suspiciously high
(AUC 0.99). Investigate rather than celebrate. ⭐

### (b) Train–test contamination — test information reaches training

```
scaler/imputer/PCA/encoder fitted on the FULL dataset before splitting ⚠️
SMOTE or any resampling applied before splitting ⚠️
feature selection on the full dataset, then CV on the model only ⚠️
hyperparameters tuned on the test set (or on the same folds you report)
duplicate or near-duplicate rows straddling the split ⚠️
```

### (c) Temporal leakage — using the future ⭐⭐

```
random shuffling of time-ordered data
computing an aggregate over a window that includes or follows the prediction timestamp
backward-fill of missing values
a "30-day average" that includes the row being scored
normalising by statistics computed over the whole time range
```

### (d) Group leakage ⭐

```
the same patient / user / device / document appears in both train and test
  → the model memorises the entity, not the pattern
augmented copies of the same image split across train and test
multiple rows from one session split randomly
```

### (e) Sneaky metadata leakage

```
row ORDER correlates with the label (data was sorted by class before export)
an id that encodes time or source (customer_id ascending = signup order)
file names, image resolutions or scanner artefacts that differ by class
  ⭐ the classic: a medical model that learned to detect the portable X-ray machine
  used in the ICU rather than the disease
```

---

## 2. The audit checklist ⭐⭐⭐

Run this on every project — and say it in interviews.

```
□ For EVERY feature: could it exist, with this value, before the label was determined?
□ Is the split time-based if there is any temporal structure?
□ Is the split grouped if entities repeat?
□ Are ALL learned transformations inside a Pipeline, fitted per fold?
□ Any duplicates or near-duplicates across the split?
□ Is any single feature dominating importance? Drop it and see what happens. ⭐
□ Is the metric implausibly good relative to the baseline and to domain expectations?
□ Do the window aggregates strictly end before the prediction timestamp?
□ Were hyperparameters tuned on the same data used to report the score?
□ Does the serving pipeline compute features identically? (skew test)
```

---

## 3. Point-in-time correctness 📐⭐⭐

The formal version of the temporal rule. To build a training row for entity `e` with prediction
time `t`:

```
features(e, t) = f( all events for e with timestamp < t )
label(e, t)    = outcome observed in (t, t + horizon]
```

```
 timeline ──────────────────────────────────────────────────►
        │◄──── feature window ────►│   │◄── label window ──►│
        t−30d                      t   t                  t+30d
                                   ▲
                          prediction time: NOTHING at or after t
                          may enter the features ⚠️
```

⚠️ A subtle case: a feature table updated in place (a customer's "current segment") has no history,
so joining it to an old training row gives that row **today's** value — future information. The fix
is slowly-changing-dimension tables with validity ranges, or an as-of join against an event log.
This is precisely what feature stores exist to provide. ⭐⭐

---

## 4. Worked examples ⭐⭐

**Example 1 — the 0.99 AUC churn model.**
Feature list includes `days_since_last_login`, computed *as of today* rather than as of the
prediction date. Churned users stopped logging in, so the feature encodes the label. Fix: compute
every feature as of the prediction date `t` from an event log.

**Example 2 — the house-price model that fell apart.**
Prices were normalised by the mean price *of the whole dataset*, which included test-period sales
during a market boom. Fix: compute the normalisation from the training period only, and treat the
market index as a lagged feature.

**Example 3 — the medical imaging model.**
Images from the diseased cohort came from one hospital and the controls from another; the model
learned the scanner's characteristic noise. Fix: stratify or group by site, and test on a held-out
site.

**Example 4 — the recommender with future interactions.**
Training rows for day `d` used the user's full interaction history including days after `d`. Fix:
strict temporal slicing per training row.

---

## 5. How leakage shows up in production ⭐

```
offline AUC 0.95 → online lift ≈ 0
the model degrades immediately at launch rather than slowly over months
the most important feature turns out to be missing or constant at serving time ⚠️
predictions are strangely confident and bimodal
```

⭐ A good closing line in an interview: *"If offline performance is far better than the online
result and the drop is immediate rather than gradual, my first hypothesis is leakage or
training–serving skew, not drift."*

---

## 6. Prevention as engineering ⭐⭐

```
1. Build features from an EVENT LOG with explicit timestamps, never from mutable
   current-state tables.
2. Use as-of / point-in-time joins (a feature store, or window functions with an
   explicit cutoff).
3. Put every learned transformation in a Pipeline.
4. Choose the split deliberately: time-based, grouped, stratified — and write down why.
5. Deduplicate before splitting.
6. Hold out a final test set that is used ONCE.
7. Add a CI test that pushes one row through the training and serving feature paths and
   asserts equality (the skew test). ⭐
8. Review the top-20 feature importances with a domain expert before shipping.
```

---

## Recall questions

1. Define leakage in one sentence.
2. Name the five categories and give an example of each.
3. What is the tell-tale symptom, and what is the first diagnostic?
4. Draw the point-in-time diagram and state the rule.
5. Why are mutable current-state tables dangerous for training data?
6. Give three transformations that must be fitted inside the CV fold.
7. When must you use a grouped split, and what happens if you do not?
8. Explain the medical-imaging scanner example.
9. How does leakage manifest after deployment, and how do you distinguish it from drift?
10. List the eight engineering practices that prevent it.
