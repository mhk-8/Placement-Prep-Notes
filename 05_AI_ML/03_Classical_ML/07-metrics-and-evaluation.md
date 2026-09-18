
# Metrics, Imbalance and Evaluation ⭐⭐⭐

> **Core idea in 3 lines**
> 1. Accuracy is almost always the wrong metric; the right one follows from the cost of each kind
>    of error.
> 2. Precision/recall, ROC-AUC and PR-AUC answer different questions, and knowing when each one
>    lies is the point of the topic.
> 3. Class imbalance is a metric-and-threshold problem far more often than a resampling problem.

---

## 1. The confusion matrix

```
                     PREDICTED
                  Positive   Negative
        Positive     TP         FN        ← actual positives  (FN = "miss")
ACTUAL
        Negative     FP         TN        ← actual negatives  (FP = "false alarm")
```

```
Accuracy    = (TP + TN) / (TP + TN + FP + FN)
Precision   = TP / (TP + FP)      "of those I flagged, how many were right?"
Recall(TPR) = TP / (TP + FN)      "of all the real positives, how many did I catch?"
Specificity = TN / (TN + FP)      TNR
FPR         = FP / (FP + TN) = 1 − specificity
F1          = 2PR/(P + R)         harmonic mean
Fβ          = (1+β²)PR/(β²P + R)  β>1 weights recall (F2), β<1 weights precision (F0.5)
```

⚠️ **Why the harmonic mean?** It is dominated by the smaller value: precision 1.0 and recall 0.0
give F1 = 0, whereas the arithmetic mean would give 0.5. F1 refuses to reward a degenerate model.

⚠️ **F1 ignores TN entirely**, so it is the right summary for rare-positive problems and the wrong
one when true negatives matter.

**Precision–recall trade-off:** both are functions of the threshold. Lowering the threshold catches
more positives (recall ↑) but flags more junk (precision ↓). You cannot improve both by moving the
threshold — only a better *model* moves the whole curve. ⭐

### Which one does the business want? ⭐⭐⭐

| Problem | Costly error | Optimise |
|---|---|---|
| Cancer screening | missing a case (FN) | **recall** (then confirm with a second test) |
| Spam filter | a real email in spam (FP) | **precision** |
| Fraud detection | both; FN loses money, FP annoys customers | **PR-AUC**, then pick the threshold by expected cost |
| Search / recommendations | ranking quality at the top | **precision@k, NDCG, MAP** |
| Credit default | asymmetric monetary costs | **expected cost**, calibrated probabilities |

✅ The strongest answer in an interview is always: *"write down the cost of a false positive and a
false negative, then choose the threshold that minimises expected cost"*:

```
choose threshold t minimising   C_FP · FP(t)  +  C_FN · FN(t)
```

---

## 2. ROC and PR curves ⭐⭐⭐

```
ROC: TPR vs FPR, sweeping the threshold        PR: precision vs recall
 1 ┤        ____                                1 ┤‾‾‾╲
   │      ╱                                       │    ╲
TPR│    ╱   ← good                          Prec  │     ╲___  ← good
   │  ╱                                           │         ╲
   │╱  ...... random (AUC 0.5)                    │ ........  ← baseline = positive rate
 0 └──────────► FPR 1                           0 └──────────► Recall 1
```

**ROC-AUC** has a probabilistic meaning worth quoting: it is the probability that a randomly
chosen positive is scored higher than a randomly chosen negative — equivalently the normalised
Mann–Whitney U statistic. It is **threshold-independent** and **invariant to the class balance**.

⚠️⭐⭐⭐ **That invariance is exactly the trap.** With 0.1% positives, FPR has a denominator of
~999 000, so thousands of false positives barely move it: ROC-AUC can look excellent (0.95) while
precision at any usable threshold is 2%. **For heavy imbalance, use PR-AUC / average precision,**
whose baseline is the positive rate (0.001 here) rather than 0.5, and which is sensitive to exactly
the errors you care about.

| | ROC-AUC | PR-AUC |
|---|---|---|
| Axes | TPR vs FPR | precision vs recall |
| Baseline | 0.5 | the positive rate |
| Uses TN | yes | no |
| Good when | balanced classes, both errors matter | rare positives, you care about the flagged set |

---

## 3. Multiclass and multilabel

```
macro-F1  : unweighted mean of per-class F1  → treats every class equally, good for imbalance ⭐
micro-F1  : pool all TP/FP/FN then compute   → dominated by frequent classes; equals accuracy
                                               in single-label multiclass
weighted-F1: mean weighted by class support
```

Pick macro when rare classes matter as much as common ones; say *which* you are reporting — vague
"F1" is a weak answer. For multilabel, also report Hamming loss and subset accuracy (exact match).

---

## 4. Regression metrics

| Metric | Formula | Property |
|---|---|---|
| MSE | `Σ(y−ŷ)²/n` | penalises large errors quadratically; differentiable |
| RMSE | `√MSE` | in the units of `y` |
| MAE | `Σ\|y−ŷ\|/n` | robust to outliers; the MLE under Laplace noise |
| MAPE | `Σ\|y−ŷ\|/\|y\|/n` | scale-free; explodes near `y = 0` and is asymmetric ⚠️ |
| SMAPE / WAPE | — | fixes some MAPE problems |
| R² | `1 − SS_res/SS_tot` | fraction of variance explained; can be negative on test data |
| Huber | quadratic then linear | outlier-robust and differentiable |
| Quantile/pinball | asymmetric | when over- and under-prediction cost differently ⭐ |

⭐ MSE optimises the conditional **mean**, MAE optimises the conditional **median**, pinball loss
at quantile `q` optimises that quantile. That one sentence answers "when would you use MAE over
MSE?" properly.

---

## 5. Ranking metrics (recommenders and search) ⭐

```
Precision@k, Recall@k
MAP    = mean over queries of the average precision
MRR    = mean of 1/(rank of the first relevant item)
NDCG@k = DCG@k / IDCG@k,   DCG = Σ rel_i / log₂(i+1)
Hit rate / coverage / diversity / novelty  — business-side metrics
```

NDCG is the standard because it handles graded relevance and discounts lower positions.

---

## 6. Calibration ⭐⭐

A model is calibrated if, among the cases it gives probability 0.7, about 70% are positive.

```
 1 ┤              ╱ perfect
   │            ╱
obs│         ╱ ·  ← over-confident model (sagging below the diagonal)
freq│      ╱  ·
   │   ╱ ·
 0 └──────────────► predicted probability
```

Measured by a **reliability diagram**, **Brier score** (`Σ(p − y)²/n`) or **expected calibration
error**.

| Model | Calibration |
|---|---|
| Logistic regression | good by construction |
| Neural nets | modern deep nets are **over-confident** ⚠️ (fixed by temperature scaling) |
| SVM | uncalibrated scores — needs Platt scaling |
| Naive Bayes | pushed towards 0/1 by the independence assumption |
| Boosted trees / random forests | RF under-confident at the extremes; boosting over-confident |

Fixes: **Platt scaling** (fit a logistic regression on the scores), **isotonic regression**
(non-parametric, needs more data), **temperature scaling** (divide the logits by a single learned
`T` — the standard fix for neural nets, and it does not change the argmax, so accuracy is
unaffected). ⭐

Calibration matters whenever the probability is used as a number: expected-value decisions,
thresholding by cost, blending with other scores, or reporting risk.

---

## 7. Class imbalance ⭐⭐⭐

**Step 0 — do not panic.** If the metric and threshold are right, many models handle imbalance
fine. The ordering of remedies matters:

```
1. Change the METRIC        → PR-AUC, F-beta, recall@fixed-precision, expected cost
2. Change the THRESHOLD     → tune on validation for the business objective  ⭐ cheapest, most effective
3. Change the LOSS          → class weights (weight ∝ 1/frequency), focal loss for extreme cases
4. Change the DATA          → resampling (below)
5. Change the FRAMING       → treat it as anomaly detection when positives are <0.1%
6. Get more positives       → targeted labelling, or a related auxiliary task
```

| Resampling method | What it does | Risk |
|---|---|---|
| Random oversampling | duplicates minority rows | overfits the duplicates |
| Random undersampling | drops majority rows | throws away information |
| **SMOTE** | synthesises minority points by interpolating between a point and its `k` nearest minority neighbours | can create points inside the majority region; poor in high dimensions; ⚠️ **must be applied inside the CV fold, on training data only** — SMOTE-before-split is a classic leakage bug that inflates the score |
| ADASYN | SMOTE concentrated on hard regions | same caveats |
| Tomek links / ENN | cleans the boundary | mild effect |
| Class weights | reweights the loss, no data change | usually the first thing to try ⭐ |

⚠️ **Resampling changes the base rate, so the output probabilities are no longer calibrated for
the real world.** If you need real probabilities, either use class weights instead, or recalibrate
afterwards with the true prior.

---

## 8. Building a trustworthy evaluation ⭐⭐⭐

```
1. Fix the split BEFORE looking at anything.  Stratified; grouped if entities repeat;
   time-based if there is any temporal order.
2. Everything learned from data lives inside the Pipeline — scaler, imputer, encoder,
   PCA, SMOTE, feature selection.  Refit per fold.
3. Report the mean AND the std across folds; a 0.3% difference with 2% fold-std is noise.
4. Compare against a real baseline: majority class, a simple rule, or the current system.
5. Bootstrap the test set for a confidence interval on the metric.
6. Slice the metric: by segment, by time, by data source.  A flat average hides the failure.
7. Touch the test set once.
```

⚠️ **Leakage checklist** — the fastest way to a fake 0.99 AUC:
- a feature computed after the label was known (e.g. `account_closed_date` for churn),
- target/mean encoding fitted on the whole dataset,
- duplicated rows split across train and test,
- time travel: random shuffling of a time series,
- an ID that correlates with the label because of how the data was collected,
- normalisation statistics computed before the split.

**The tell:** validation performance that is implausibly high, or one feature with overwhelming
importance. Investigate rather than celebrate. ⭐⭐⭐

---

## 9. Worked numerical example (OA favourite)

10 000 transactions, 100 fraudulent. A model flags 200 transactions, of which 80 are fraud.

```
TP = 80, FP = 120, FN = 20, TN = 9780
Precision = 80/200  = 0.40
Recall    = 80/100  = 0.80
F1        = 2(0.4)(0.8)/(1.2) = 0.533
Accuracy  = (80+9780)/10000 = 0.986     ← but ALWAYS-NEGATIVE also scores 0.99 ⚠️
Specificity = 9780/9900 = 0.988
FPR = 0.0121
```

The punchline: accuracy 98.6% is *worse than the trivial model*, while recall 0.8 at precision 0.4
is genuinely useful if manual review of 200 items per 10 000 is affordable.

---

## Recall questions

1. Define precision and recall, and give a problem where each dominates.
2. Why is F1 a harmonic mean, and what does it ignore?
3. What does ROC-AUC mean probabilistically?
4. Why does ROC-AUC mislead under heavy imbalance, and what replaces it?
5. Macro vs micro F1 — when does each matter?
6. Which loss targets the conditional mean, median and `q`-quantile?
7. Define calibration, name two models that are badly calibrated, and give the fix for each.
8. Order the remedies for class imbalance, cheapest first.
9. Why must SMOTE go inside the CV fold?
10. Name five ways data leakage creeps in, and the symptom that gives it away.
