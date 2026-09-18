
# Feature Engineering ⭐⭐

> **Core idea in 3 lines**
> 1. Feature engineering is where domain knowledge enters the model; for tabular problems it
>    usually beats model choice.
> 2. Good features are available at prediction time, stable over time, and encode a hypothesis
>    about the mechanism.
> 3. Every engineered feature is a leakage opportunity — the discipline is asking "would I know
>    this at prediction time?" every single time.

---

## 1. The test every feature must pass ⭐⭐⭐

```
1. AVAILABILITY : will this value exist, with this meaning, at prediction time?
2. POINT-IN-TIME: is it computed only from data that existed BEFORE the label?
3. STABILITY    : will its distribution hold next month? (avoid ids, raw timestamps)
4. SIGNAL       : is there a plausible mechanism, not just a correlation?
5. COST         : can it be computed within the latency budget, online?
```

A feature that fails (1) or (2) is leakage, no matter how predictive it looks.

---

## 2. Numeric features

```
transformations : log1p (skew), sqrt, reciprocal, power, Box-Cox/Yeo-Johnson
binning         : equal-width, equal-frequency (quantile), or domain cut-points
                  — captures non-linearity for linear models; loses information ⚠️
interactions    : products, ratios ⭐ (ratios are often the real signal:
                  debt/income, price/sqft, clicks/impressions, spend/tenure)
polynomials     : degree 2–3; explodes dimensionality, prefer trees instead
differences     : value − group mean, value − personal baseline ⭐
clipping        : cap at a learned percentile to bound outlier influence
```

⭐ **Ratios and deviations from a personal baseline are the highest-value engineered numeric
features** in most applied problems — "this transaction is 8× this user's median" carries far more
signal than the raw amount.

---

## 3. Categorical features

Encoding is covered in `01-data-cleaning-and-eda.md`. Beyond encoding:

```
grouping rare levels into "other" (min_frequency)
hierarchical features: city → state → country, product → subcategory → category
                       ⭐ lets the model back off to a coarser level for rare values
combinations: category × time-of-day, device × region (explicit crosses)
count/frequency of the level; target statistics (out-of-fold)
```

---

## 4. Datetime features ⭐⭐

```
components : year, month, day, hour, minute, day-of-week, week-of-year, quarter
flags      : is_weekend, is_holiday (per country ⭐), is_month_end, is_business_hour
cyclical ⭐: hour 23 and hour 0 are adjacent, but 23 and 0 look maximally distant.
             Encode as   sin(2π·h/24),  cos(2π·h/24)   — likewise for month and weekday.
elapsed    : time since signup, since last purchase, since last login, until expiry
relative   : position within the session, order of the event
```

⚠️ **Never feed a raw timestamp to a model that will run in the future** — it is monotonically
increasing, so the model extrapolates into a range it has never seen. Use components and elapsed
times instead.

📐 The cyclical encoding matters because a model must be able to learn that 23:00 and 00:00 are
close; with a raw integer they differ by 23. Two features `(sin, cos)` place the hours on a circle
where that distance is correct.

---

## 5. Aggregation and window features ⭐⭐⭐

The workhorse of tabular ML on event data.

```
per entity (user, card, device, store):
    count, sum, mean, median, std, min, max, nunique
    over windows: last 1h, 24h, 7d, 30d, lifetime
    ratios between windows ⭐: last-7d spend / last-90d spend  → detects a change in behaviour
    time since first / last event; inter-event gaps
    trend: slope of a linear fit over the window
    entropy/diversity: how spread out are the categories used?
```

```
 events ──► group by entity ──► window ──► aggregate ──► join back to the prediction row
                                    ▲
                          ⚠️ the window must END strictly BEFORE the label's timestamp
```

⚠️⚠️ This is where point-in-time correctness lives. If the 30-day aggregate includes the
transaction being scored, or events after it, you have leaked. Build training features from an
as-of join at the prediction timestamp, using the same code that computes them online.

---

## 6. Text, image and graph features

```
text  : length, word/sentence counts, punctuation and capitalisation ratios,
        TF-IDF, sentiment score, language, named-entity counts,
        pretrained sentence embeddings ⭐ (usually the strongest single feature)
image : pretrained CNN/CLIP embeddings; simple stats (size, aspect ratio, blur,
        brightness) are surprisingly predictive for quality problems
graph : degree, PageRank, clustering coefficient, community id, neighbour label rate ⭐
        (fraud rings, referral abuse, co-purchase structure)
geo   : distance to points of interest, geohash cells, density, region aggregates
```

---

## 7. Feature selection ⭐⭐

| Family | Methods | Note |
|---|---|---|
| **Filter** | correlation with target, mutual information, chi-square, variance threshold | fast, model-agnostic; ignores interactions ⚠️ |
| **Wrapper** | recursive feature elimination, forward/backward selection | expensive; risks overfitting the selection |
| **Embedded** | lasso, tree importances, regularisation paths | selection during training; cheap |
| **Permutation importance** ⭐ | shuffle a column, measure the drop on **held-out** data | model-agnostic, honest; slow; misleading with correlated features |
| **SHAP** | game-theoretic attributions | consistent, local + global; expensive but the current standard ⭐ |

⚠️ **Feature selection must be inside the cross-validation loop.** Selecting features on the full
dataset and then cross-validating the model is a classic way to report a score several points too
high.

⚠️ Correlated features split their importance, so a genuinely important signal can look weak when
duplicated across columns — cluster correlated features and evaluate the group.

---

## 8. Automated feature engineering

```
Featuretools (deep feature synthesis) : automatic aggregations across relational tables
tsfresh                               : hundreds of time-series features
AutoML (AutoGluon, H2O)               : search over pipelines
Deep learning                         : learns representations instead — the reason
                                        feature engineering matters far less for images,
                                        audio and text than for tabular data ⭐
```

⚠️ Automated generation produces many correlated, hard-to-explain features and multiplies the
leakage surface. Use it to generate candidates, then select and sanity-check by hand.

---

## 9. Worked example — a fraud feature set ⭐

```
raw            : amount, merchant_category, timestamp, country
engineered:
  amount_log                    log1p(amount)                 — skew
  amount_vs_user_median         amount / user's 90d median    — personal baseline ⭐
  amount_zscore_user            (amount − μ_user)/σ_user
  txn_count_1h / 24h / 7d       velocity per card             — point-in-time windows ⚠️
  distinct_merchants_24h        diversity
  seconds_since_last_txn        inter-event gap
  is_new_merchant_for_user      boolean
  hour_sin, hour_cos            cyclical time
  is_night_local                using the merchant's timezone
  country_mismatch              billing vs transaction country
  distance_from_last_txn_kmph   implied travel speed → impossible-travel flag ⭐
  device_shared_account_count   graph feature
  merchant_fraud_rate_30d       out-of-fold target statistic ⚠️ smoothed
```

Every one of these must be computable online within the latency budget — which is why they live in
a streaming aggregate store rather than being recomputed from raw history at request time.

---

## Recall questions

1. Give the five tests every feature must pass.
2. Why are ratios and personal-baseline deviations so effective?
3. Write the cyclical encoding for hour-of-day and explain why it is needed.
4. Why must a raw timestamp never be a model feature?
5. Where exactly does point-in-time correctness apply in window aggregates?
6. Compare filter, wrapper, embedded and permutation-based selection.
7. Why must feature selection be inside the CV loop?
8. What goes wrong with importance when features are correlated?
9. Name three graph features and a problem where they matter.
10. Design five features for detecting card fraud and justify each.
