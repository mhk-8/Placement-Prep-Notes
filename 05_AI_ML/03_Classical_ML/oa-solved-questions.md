
# Classical ML — Solved OA Questions

35 questions covering bias–variance, linear models, regularisation, trees, SVMs, clustering and
metrics. These mirror the ML sections of MNC online assessments.

---

## Set A — Bias, variance, validation

**Q1.** Train accuracy 99%, validation accuracy 71%. Diagnosis and two fixes?

<details><summary>Answer</summary>

High variance (overfitting). Fixes: more data or augmentation, stronger regularisation
(L1/L2/dropout), a simpler model, early stopping, bagging, or removing leaky/noisy features.
</details>

**Q2.** Train accuracy 68%, validation accuracy 67%. Will more data help?

<details><summary>Answer</summary>

**No.** Both errors are high and close ⇒ high bias. More data does not reduce bias. Increase
capacity, add features/interactions, reduce regularisation, train longer, check the learning rate.
</details>

**Q3.** Write the bias–variance decomposition of expected squared error.

<details><summary>Answer</summary>

`E[(y − ĥ(x))²] = σ² + (f(x) − E[ĥ(x)])² + E[(ĥ(x) − E[ĥ(x)])²]` = noise + bias² + variance.
Holds exactly for squared loss; no clean additive form for 0–1 loss.
</details>

**Q4.** Which of these reduces variance? (a) deeper tree (b) more features (c) bagging (d) lower λ

<details><summary>Answer</summary>

**(c) bagging.** The others all increase variance.
</details>

**Q5.** You have 50 patients with 10 visits each — 500 rows. What CV scheme?

<details><summary>Answer</summary>

**GroupKFold by patient.** Plain KFold puts the same patient in both train and test, so the model
memorises the patient rather than the pattern, inflating the score. ⚠️
</details>

**Q6.** Why is nested CV needed?

<details><summary>Answer</summary>

If you tune hyperparameters on the same folds you report, the score is optimistically biased.
Nested CV tunes in an inner loop and scores once per outer fold, giving an unbiased estimate of the
whole *procedure*.
</details>

**Q7.** Test AUC is 0.99 on a churn model. What do you check first?

<details><summary>Answer</summary>

Leakage. Look for post-outcome features (cancellation date, refund flag), target encoding fit on
all data, duplicate rows across the split, random shuffling of time-ordered data, and any single
feature with overwhelming importance.
</details>

---

## Set B — Linear and logistic regression

**Q8.** `XᵀX` is singular. Two causes and two fixes?

<details><summary>Answer</summary>

Causes: `d > n`, or exactly/near collinear features (including a dummy-variable trap — all `k`
one-hot columns plus an intercept). Fixes: ridge (`XᵀX + λI` is always PD), drop or combine
features, PCA, or use the pseudoinverse.
</details>

**Q9.** Adding a random noise feature to a linear regression: what happens to train `R²` and to
adjusted `R²`?

<details><summary>Answer</summary>

Train `R²` never decreases (it weakly increases). Adjusted `R²` typically decreases because of the
`(n−1)/(n−d−1)` penalty. Held-out performance is what you should report.
</details>

**Q10.** Logistic regression coefficient for `x = 0.693`. Interpretation?

<details><summary>Answer</summary>

`e^{0.693} ≈ 2`: a one-unit increase in `x` **doubles the odds** of the positive class, holding
other features fixed. It does not double the probability. ⚠️
</details>

**Q11.** `∇J` for logistic regression?

<details><summary>Answer</summary>

`Xᵀ(p − y)` with `p = σ(Xw)`. The sigmoid derivative cancels against the log in the cross-entropy.
</details>

**Q12.** Is the logistic loss convex? Prove it.

<details><summary>Answer</summary>

Yes. `H = XᵀSX` with `S = diag(pᵢ(1−pᵢ)) ⪰ 0`, so `vᵀHv = ‖S^{1/2}Xv‖² ≥ 0`. PSD Hessian ⇒ convex
⇒ every local optimum is global.
</details>

**Q13.** Classes are perfectly separable. What happens to the logistic weights?

<details><summary>Answer</summary>

They diverge — the likelihood keeps increasing as `‖w‖ → ∞`. L2 regularisation (sklearn's default)
or early stopping bounds them.
</details>

**Q14.** Residual plot shows a funnel widening with fitted value. Which assumption fails and what
do you do?

<details><summary>Answer</summary>

Homoscedasticity. Standard errors and CIs become wrong (coefficients stay unbiased). Fix: log- or
Box-Cox-transform `y`, use weighted least squares, or use robust (HC) standard errors.
</details>

---

## Set C — Regularisation

**Q15.** Which gives sparsity, L1 or L2, and why?

<details><summary>Answer</summary>

L1. Its subgradient is a constant `λ·sign(w)`, so it keeps pushing with full force until the
coefficient hits exactly zero; L2's gradient `2λw` weakens as `w → 0`. Geometrically the L1 ball's
corners lie on the axes.
</details>

**Q16.** In the orthonormal case, write the ridge and lasso solutions in terms of the OLS solution.

<details><summary>Answer</summary>

Ridge: `w_ols/(1+λ)`. Lasso: `sign(w_ols)(|w_ols| − λ/2)₊` — soft thresholding, exactly zero once
`|w_ols| ≤ λ/2`.
</details>

**Q17.** Two features are 0.99 correlated. Lasso vs ridge behaviour?

<details><summary>Answer</summary>

Lasso arbitrarily keeps one and zeroes the other (unstable under resampling); ridge shrinks both
together, splitting the coefficient. Elastic net keeps them together with sparsity.
</details>

**Q18.** Why must features be standardised before regularising, and should the intercept be
penalised?

<details><summary>Answer</summary>

The penalty acts on raw coefficient magnitudes, so the same feature in different units is
penalised differently. The intercept must **not** be penalised — shrinking it pulls predictions
toward zero rather than toward the mean.
</details>

**Q19.** `λ → ∞` in ridge: what is `w`, and what are bias and variance?

<details><summary>Answer</summary>

`w → 0` (predictions become the intercept/mean). Bias → maximal, variance → 0.
</details>

**Q20.** Is dropout applied at test time?

<details><summary>Answer</summary>

No. With inverted dropout, activations are divided by `(1−p)` during training and inference is
left unchanged.
</details>

---

## Set D — Trees and ensembles

**Q21.** Node with 8 positives, 2 negatives. Gini?

<details><summary>Answer</summary>

`1 − (0.8² + 0.2²) = 1 − 0.68 = 0.32`.
</details>

**Q22.** Why is misclassification rate not used to grow trees?

<details><summary>Answer</summary>

It is not strictly concave, so a split that clearly improves purity can score zero gain. Gini and
entropy are strictly concave and always reward a purity-changing split.
</details>

**Q23.** Random forest: what does `max_features` do, and why does it help?

<details><summary>Answer</summary>

It restricts the candidate features considered at each split (`√d` for classification). It
decorrelates the trees; since `Var(avg) = ρσ² + (1−ρ)σ²/B`, lowering `ρ` lowers the floor of the
averaged variance. ⭐
</details>

**Q24.** Does adding trees to a random forest cause overfitting? To a gradient-boosted model?

<details><summary>Answer</summary>

RF: no — more trees only reduce variance; the curve flattens. GBM: **yes** — each tree fits the
current residual, so too many rounds fit noise. Use early stopping.
</details>

**Q25.** In gradient boosting with squared loss, what does each new tree fit?

<details><summary>Answer</summary>

The negative gradient of the loss with respect to the current prediction, which for squared loss is
exactly the residual `y − F_{m−1}(x)`. For log-loss it is `y − p`.
</details>

**Q26.** Lower the learning rate in a GBM. What else must change?

<details><summary>Answer</summary>

Increase `n_estimators` roughly proportionally — they trade off. Small `ν` with many trees usually
generalises better.
</details>

**Q27.** Why is impurity-based feature importance misleading?

<details><summary>Answer</summary>

It favours high-cardinality and continuous features because they offer more split points, and it
is computed on training data. Use permutation importance on held-out data, or SHAP.
</details>

**Q28.** Do decision trees require feature scaling?

<details><summary>Answer</summary>

**No** — splits depend only on the order of values, not their scale. (SVMs, k-NN, k-means, PCA and
gradient-descent-trained linear models do.)
</details>

---

## Set E — SVM, clustering, metrics

**Q29.** What does `C` control in a soft-margin SVM, and in which direction?

<details><summary>Answer</summary>

The penalty on margin violations. **Large `C`** ⇒ few violations tolerated ⇒ narrow margin, low
bias, high variance. `C` behaves like `1/λ`.
</details>

**Q30.** Which points determine the SVM solution, and why?

<details><summary>Answer</summary>

The support vectors. KKT complementary slackness forces `αᵢ = 0` for every point strictly outside
the margin, and `w = Σαᵢyᵢxᵢ` therefore depends only on points with `αᵢ > 0`.
</details>

**Q31.** RBF SVM with a very large `γ`: what happens?

<details><summary>Answer</summary>

Each training point influences only its immediate neighbourhood, so the boundary forms islands
around training points — severe overfitting, essentially 1-NN.
</details>

**Q32.** k-means on data with two concentric rings. Result?

<details><summary>Answer</summary>

It fails — k-means produces convex, roughly spherical partitions and will cut both rings in half.
Use DBSCAN or spectral clustering.
</details>

**Q33.** Choosing `k`: name two principled methods.

<details><summary>Answer</summary>

Silhouette score (maximise the mean), the gap statistic, BIC for a GMM, or tuning `k` on a
downstream task metric. The elbow method is a heuristic and is often ambiguous.
</details>

**Q34.** 1% positive class; your model has ROC-AUC 0.95 but is useless in production. Explain.

<details><summary>Answer</summary>

FPR has a huge denominator (the 99% negatives), so thousands of false positives barely move it.
Report **PR-AUC/average precision**, whose baseline is the 1% positive rate, and pick the threshold
by expected cost. ⭐⭐⭐
</details>

**Q35.** TP=80, FP=120, FN=20, TN=9780. Compute precision, recall, F1 and accuracy, and comment.

<details><summary>Answer</summary>

Precision `0.40`, recall `0.80`, F1 `0.533`, accuracy `0.986`. Accuracy is worse than the
always-negative baseline (0.99) despite the model being useful — the standard demonstration that
accuracy is the wrong metric under imbalance.
</details>

---

## Scoring

| Correct | Read as |
|---|---|
| 31–35 | Classical ML is interview-ready |
| 24–30 | Revisit the two weakest topic files, then retest |
| 16–23 | Work through `01`–`07` of this folder with the derivations |
| < 16 | This folder is your highest-ROI study target; three focused days |
