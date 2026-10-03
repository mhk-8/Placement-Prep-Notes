
# ML Metrics — One Pager

> **Use:** every ML interview, and the moment you are asked "how did you evaluate it?"
> Picking the wrong metric is the most common ML-interview failure, and it is entirely avoidable. ⭐

---

## 1. The confusion matrix ⭐⭐⭐

```
                    PREDICTED
                 Positive   Negative
ACTUAL Positive     TP         FN      ← Type II error (miss)
       Negative     FP         TN      ← Type I error (false alarm)
```

| Metric | Formula | Reads as | Use when |
|---|---|---|---|
| **Accuracy** | (TP+TN)/(TP+TN+FP+FN) | Overall correctness | Balanced classes ⚠️ useless at 99:1 |
| **Precision** | TP/(TP+FP) | Of those flagged, how many were right | FP is expensive (spam, fraud alerts) ⭐ |
| **Recall / TPR / Sensitivity** | TP/(TP+FN) | Of the real positives, how many did we catch | FN is expensive (cancer, defects) ⭐ |
| **Specificity / TNR** | TN/(TN+FP) | Of the real negatives, how many did we clear | |
| **F1** | 2PR/(P+R) | Harmonic mean | Need a single number with imbalance |
| **Fβ** | (1+β²)PR/(β²P+R) | β>1 favours recall | Asymmetric costs, cost known |
| FPR | FP/(FP+TN) = 1−specificity | | ROC x-axis |
| FNR | FN/(TP+FN) = 1−recall | | |
| Balanced accuracy | (TPR+TNR)/2 | | Imbalanced classes |
| **MCC** | see below | Correlation coefficient for a 2×2 table | Best single metric under imbalance ⭐ |
| Cohen's κ | (p₀−pₑ)/(1−pₑ) | Agreement above chance | Inter-annotator agreement |

📐 **MCC** = (TP·TN − FP·FN) / √((TP+FP)(TP+FN)(TN+FP)(TN+FN)), range [−1, 1].

> ⭐ **The accuracy paradox, with numbers.** 1% of transactions are fraud. A model that predicts
> "not fraud" always scores **99% accuracy**, with recall 0 and precision undefined. This is the
> answer to "why not just use accuracy" — always give it as a number, not as a principle.

> **Precision/recall trade-off:** lowering the decision threshold raises recall and lowers
> precision. The threshold is a *product decision*, not a model property. ⭐

---

## 2. Threshold-free metrics ⭐⭐

| Metric | Axes | Interpretation | Caveat |
|---|---|---|---|
| **ROC-AUC** | TPR vs FPR | P(a random positive scores above a random negative) ⭐ | ⚠️ **optimistic under heavy imbalance** — FPR has a huge TN denominator |
| **PR-AUC / average precision** | Precision vs Recall | Area under the PR curve | ⭐ **the right choice when positives are rare** |
| Log loss / cross-entropy | — | −(1/N)Σ[y log p + (1−y) log(1−p)] | Punishes confident mistakes hard; needs calibration |
| Brier score | — | mean (p − y)² | Proper scoring rule for calibration |
| Lift / gain | — | Improvement over random at the top k% | Marketing, ranking |

```
AUC = 0.5 → random.   AUC = 1.0 → perfect.   AUC < 0.5 → invert your predictions.
Baseline PR-AUC equals the POSITIVE CLASS RATE, not 0.5 ⭐ — so 0.3 PR-AUC on a 2%-positive
problem is good, and saying this shows you understand the metric.
```

**Calibration:** a model is calibrated if, among samples predicted 0.7, about 70% are positive.
Check with a reliability diagram; fix with **Platt scaling** (logistic) or **isotonic
regression**. ⚠️ Neural networks are typically over-confident; tree ensembles under-confident. ⭐

---

## 3. Regression metrics ⭐⭐

| Metric | Formula | Units | Property |
|---|---|---|---|
| **MAE** | (1/n)Σ\|y−ŷ\| | target units | Robust to outliers; optimises the **median** ⭐ |
| **MSE** | (1/n)Σ(y−ŷ)² | squared | Penalises large errors; optimises the **mean**; differentiable |
| **RMSE** | √MSE | target units | Same optimum as MSE, interpretable scale |
| **R²** | 1 − SS_res/SS_tot | unitless | Fraction of variance explained. ⚠️ can be negative |
| Adjusted R² | 1−(1−R²)(n−1)/(n−p−1) | unitless | Penalises extra predictors |
| MAPE | (100/n)Σ\|(y−ŷ)/y\| | % | ⚠️ undefined/explodes near y = 0, asymmetric |
| SMAPE | symmetric variant | % | Bounded |
| Huber | quadratic then linear | target units | Robust *and* differentiable ⭐ |
| RMSLE | √mean(log(1+y)−log(1+ŷ))² | — | For skewed targets; penalises under-prediction more |
| Quantile / pinball loss | asymmetric | — | Predicting a quantile, not a mean |

> ⭐ **MAE vs MSE in one sentence:** MSE's gradient grows with the error so it chases outliers and
> its optimum is the conditional mean; MAE weights all errors equally and its optimum is the
> conditional median. Choose by whether outliers are signal or noise.

---

## 4. Ranking and retrieval ⭐ (your IR project)

| Metric | Formula / idea |
|---|---|
| Precision@k / Recall@k | Among the top k |
| **MAP** | Mean over queries of average precision |
| **MRR** | mean of 1/rank of the first relevant result ⭐ |
| **NDCG@k** | DCG@k / IDCG@k, where DCG = Σ relᵢ / log₂(i+1) ⭐ graded relevance, position-discounted |
| Hit rate / coverage / diversity / novelty | Recommender-system health beyond accuracy |

> ⭐ **Say this if ranking comes up:** NDCG is the right metric when relevance is graded and
> position matters; MRR when there is essentially one right answer. Both are what your TF-IDF /
> BM25 / LSA comparison used, and you tested the differences with a **Wilcoxon signed-rank test**
> — mentioning the significance test, not just the point estimates, is unusual and lands well.

---

## 5. Clustering and unsupervised

| Metric | Needs labels? | Range / goal |
|---|---|---|
| Inertia / WCSS | No | Lower; use the elbow method ⚠️ monotone in k |
| **Silhouette** | No | [−1,1], higher better; (b−a)/max(a,b) ⭐ |
| Davies-Bouldin | No | Lower better |
| Calinski-Harabasz | No | Higher better |
| **ARI** | Yes | Chance-corrected agreement |
| NMI / AMI | Yes | Normalised mutual information |
| Homogeneity / completeness / V-measure | Yes | Precision/recall analogues |

---

## 6. NLP and generative

```
BLEU        : n-gram precision + brevity penalty (translation) ⚠️ poor for single sentences
ROUGE-N/L   : n-gram / longest-common-subsequence recall (summarisation)
METEOR      : synonym- and stem-aware
chrF        : character n-gram F-score
Perplexity  : exp(cross-entropy) — lower is better; comparable only on the same tokenisation ⭐
BERTScore   : embedding cosine similarity, token-aligned
Exact match / token F1 : extractive QA (SQuAD)
WER         : (S+D+I)/N for speech recognition
Human eval / pairwise preference / LLM-as-judge ⚠️ note the biases if you mention it
```

---

## 7. Computer vision

```
IoU = area(∩)/area(∪)                             — the threshold for a "correct" box
mAP@0.5 · mAP@[0.5:0.95]                          — COCO detection standard ⭐
Pixel accuracy · mean IoU · Dice = 2|A∩B|/(|A|+|B|)  — segmentation (Dice = F1 on pixels)
Top-1 / Top-5 accuracy                            — classification
FID / IS                                          — generative image quality
PSNR · SSIM                                       — reconstruction / super-resolution ⭐ your image
                                                    preprocessing project
```

---

## 8. Validation and the traps ⚠️⭐⭐

| Scheme | Use |
|---|---|
| Hold-out (train/val/test) | Large data; fast |
| **k-fold CV** | Standard; k = 5 or 10 |
| **Stratified k-fold** | ⭐ mandatory for imbalanced classification |
| Group k-fold | Correlated samples (same patient, same user) ⚠️ |
| **Time-series split** | ⭐ expanding/rolling window; never shuffle time |
| Leave-one-out | Tiny datasets; high variance, expensive |
| Nested CV | Honest estimate when you also tune hyperparameters ⭐ |

### The leakage checklist ⚠️ (this is what interviewers hunt for)
```
□ Fit the scaler/encoder/imputer on TRAIN ONLY, then transform val/test ⭐ the #1 leak
□ Feature selection inside the CV loop, not before it
□ Oversampling (SMOTE) AFTER the split, on the training fold only ⚠️
□ Time-series: no shuffling, no future information in features, no target leakage from lags
□ Duplicate or near-duplicate rows spanning the split
□ Group leakage: the same user/patient/session in both train and test
□ Target encoding computed on the full dataset
□ Tuning on the test set — the test set is touched once ⭐
```

> ⭐ **Say this unprompted:** "I fit the preprocessing inside the pipeline so it is refit on each
> training fold." It is a one-line sentence that distinguishes someone who has actually shipped a
> model from someone who has read about it.

---

## 9. Choosing the metric ⭐⭐

```
Balanced classification               → accuracy, F1
Rare positives (fraud, defect, churn) → PR-AUC, recall at a fixed precision ⭐ not accuracy
FN expensive (medical screening)      → recall / Fβ with β > 1
FP expensive (auto-blocking content)  → precision / Fβ with β < 1
Need probabilities (bidding, pricing) → log loss + CALIBRATION ⭐
Ranking                               → NDCG, MRR, MAP
Regression with outliers              → MAE or Huber
Regression where big errors are worse  → RMSE
Business framing                      → always also state the business metric and the operating
                                        threshold. "Recall 0.92 at precision 0.80, threshold 0.37" ⭐
```

> **Interview reflex:** never answer "which metric" without first asking **what is the class
> balance and which error is more expensive?** The question is usually a test of whether you ask.

---

## Recall questions
1. 1% positives, model predicts all-negative. Give accuracy, precision, recall.
2. When is ROC-AUC misleading, and what replaces it?
3. What is the baseline value of PR-AUC?
4. MAE vs MSE — which optimises the median, and which the mean?
5. Name four distinct forms of data leakage.
6. What does it mean for a model to be calibrated, and how do you fix miscalibration?
7. Define NDCG in one sentence. When would you prefer MRR?
8. Write the MCC formula, or at least say what it measures and its range.
