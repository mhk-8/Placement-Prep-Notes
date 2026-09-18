
# Classical ML — Flashcards

---

## Questions

**Learning theory**
1. Bias–variance decomposition, and why each cross-term vanishes.
2. Which loss does it hold exactly for?
3. Learning-curve signature of high bias vs high variance.
4. Which actions reduce bias, which reduce variance?
5. Bagging vs boosting: which term does each attack, and why?
6. What is nested CV for?
7. When do you use GroupKFold? TimeSeriesSplit?
8. Six ways data leaks, and the symptom.
9. VC bound — statement and qualitative reading.
10. Double descent.
11. No Free Lunch and its practical meaning.

**Linear / logistic**
12. Normal equation and its geometric meaning.
13. OLS assumptions (LINE + one more) and a diagnostic for each.
14. Why report adjusted `R²` or a held-out score?
15. Two reasons squared loss is wrong for classification.
16. Log-odds interpretation of a logistic coefficient.
17. Logistic gradient and the proof of convexity.
18. Perfectly separable data — what happens?
19. Why is logistic regression well calibrated?
20. Softmax vs one-vs-rest vs independent sigmoids.
21. GLM: link function and loss for normal, Bernoulli, Poisson.

**Regularisation**
22. Ridge solution and its SVD shrinkage factor.
23. Soft thresholding, and why lasso gives exact zeros.
24. L1/L2 geometry picture.
25. Priors behind L1 and L2; what `λ` equals in the ridge MAP.
26. Elastic net — what problem does it solve?
27. Standardisation and the intercept — the two rules.
28. Five deep-learning regularisers and what each constrains.
29. Inverted dropout at train and test time.
30. The 1-SE rule.

**Trees and ensembles**
31. Gini vs entropy; why not misclassification rate.
32. Information gain formula.
33. Pre-pruning parameters and cost-complexity pruning.
34. `Var(average) = ρσ² + (1−ρ)σ²/B` — derive and interpret.
35. What `max_features` does in a random forest.
36. Out-of-bag error: why free, what fraction.
37. AdaBoost weight update and its loss function.
38. Gradient boosting as functional gradient descent.
39. Does adding estimators overfit? RF vs GBM.
40. XGBoost vs LightGBM vs CatBoost — one differentiator each.
41. Why impurity importance misleads; what to use instead.
42. Why trees need no scaling and cannot extrapolate.

**SVM**
43. Why is the margin `2/‖w‖`?
44. Soft-margin primal; direction of `C`.
45. The dual, and the two consequences of its form.
46. Complementary slackness ⇒ support vectors.
47. Kernel trick with the `(xᵀz)²` example.
48. Role of `γ` in an RBF kernel.
49. Mercer's condition.
50. Why SVMs don't scale, and the alternative.
51. Hinge vs logistic loss.

**Unsupervised**
52. k-means objective; why the M-step is the mean; what convergence guarantees.
53. k-means++.
54. Four k-means assumptions and a failure for each.
55. GMM E and M steps; how k-means is a special case.
56. DBSCAN: core/border/noise, and two weaknesses.
57. Linkage criteria and their tendencies.
58. Silhouette score.
59. Two things you must not read off a t-SNE plot.
60. Valid clustering evaluation metrics with and without labels.

**Metrics**
61. Precision, recall, specificity, F1, Fβ.
62. Why is F1 harmonic, and what does it ignore?
63. Probabilistic meaning of ROC-AUC.
64. When does ROC-AUC lie, and what replaces it?
65. Macro vs micro F1.
66. MSE / MAE / pinball — which conditional statistic does each target?
67. NDCG.
68. Calibration: definition, two badly-calibrated models, three fixes.
69. Imbalance remedies in priority order.
70. Why SMOTE must live inside the CV fold.

---

## Answers

1. `E[(y−ĥ)²] = σ² + Bias² + Var`. The cross-terms vanish because `E[ε] = 0`, `ε ⫫ D`, and
   `E_D[ĥ − E_D[ĥ]] = 0`.

2. Squared loss. For 0–1 loss there is no clean additive decomposition.

3. High bias: both curves plateau high and close. High variance: low train error, a large and
   persistent gap that narrows with more data.

4. Bias↓: bigger model, more features, less regularisation, boosting, longer training.
   Variance↓: more data, regularisation, bagging, early stopping, feature selection, simpler model.

5. Bagging reduces variance by averaging models fit on resamples; boosting reduces bias by
   sequentially fitting the residual of the current ensemble.

6. Getting an unbiased estimate of a *procedure* that includes hyperparameter tuning; tuning
   happens in the inner loop, scoring once per outer fold.

7. GroupKFold when rows share an entity (patient, user, session). TimeSeriesSplit whenever there
   is temporal ordering — always train on the past.

8. Post-outcome features; preprocessing fitted before the split; target encoding on all data;
   duplicates across the split; shuffled time series; collection-artefact IDs. Symptom:
   implausibly high validation performance or one dominant feature.

9. `R ≤ R̂ + O(√((VC log n + log(1/δ))/n))`. Gap grows with capacity, shrinks as `√(1/n)`.

10. Past the interpolation threshold, test error can fall again — over-parameterised models find
    low-norm interpolating solutions, so the classical U-curve is not the whole story.

11. Averaged over all problems no learner is better than another; in practice, match the inductive
    bias to the domain (GBMs for tabular, CNNs/transformers for perceptual data).

12. `XᵀXw = Xᵀy`; `Xw` is the orthogonal projection of `y` onto `col(X)` and the residual is
    orthogonal to every feature.

13. Linearity (residual-vs-fitted plot), Independence (Durbin–Watson), Normality of residuals
    (Q–Q plot), Equal variance (funnel in the residual plot), no multicollinearity (VIF).

14. `R²` never decreases when a feature is added, so it cannot detect overfitting.

15. Predictions escape `[0,1]` and are not probabilities; MSE with a sigmoid is non-convex and has
    vanishing gradients on saturated units. (Also: far-away correct points still drag the fit.)

16. `wⱼ` is the change in log-odds per unit; `e^{wⱼ}` is the odds ratio.

17. `∇ = Xᵀ(p − y)`; `H = XᵀSX` with `S = diag(pᵢ(1−pᵢ)) ⪰ 0` ⇒ PSD ⇒ convex.

18. `‖w‖ → ∞`; regularisation or early stopping is required.

19. It directly maximises the Bernoulli log-likelihood of the predicted probabilities, so the
    probabilities themselves are the fitted quantity.

20. Softmax for mutually exclusive classes; OvR as a simple `K`-model alternative; independent
    sigmoids for multi-label.

21. Normal/identity/MSE; Bernoulli/logit/cross-entropy; Poisson/log/Poisson deviance.

22. `w = (XᵀX + λI)⁻¹Xᵀy`; in the SVD basis each direction is scaled by `σⱼ²/(σⱼ²+λ)`, so
    low-variance (collinear) directions are shrunk hardest.

23. `wⱼ = sign(ρⱼ)(|ρⱼ| − λ/2)₊`; once `|ρⱼ| ≤ λ/2` the coefficient is exactly zero.

24. Ellipsoidal loss contours first touch a diamond at a corner (a coordinate is zero) but touch a
    sphere at a generic point.

25. Laplace ⇒ L1; Gaussian ⇒ L2 with `λ = σ²/τ²`.

26. Correlated-feature instability: lasso picks one arbitrarily, elastic net keeps the group.

27. Standardise first (the penalty is scale-dependent); never penalise the intercept.

28. Weight decay (weight norm), dropout (co-adaptation / ensemble), early stopping (distance
    travelled from init), data augmentation (effective dataset size / invariances), label
    smoothing (logit confidence), mixup, BN/LN (activation statistics).

29. Scale by `1/(1−p)` during training; at inference dropout is disabled and nothing is scaled.

30. Among hyperparameter settings, choose the simplest (most regularised) whose CV score is within
    one standard error of the best.

31. `G = 1 − Σp²`, `H = −Σp log p`; Gini is cheaper and behaves almost identically. Misclassification
    rate is not strictly concave, so it can report zero gain for a genuinely purifying split.

32. `H(parent) − Σ (n_child/n_parent) H(child)`.

33. `max_depth`, `min_samples_split`, `min_samples_leaf`, `max_leaf_nodes`,
    `min_impurity_decrease`; cost-complexity pruning minimises `R(T) + α|T|` with `α` from CV.

34. `Var(Σf) = Bσ² + B(B−1)ρσ²`, divide by `B²`. Independent models fall as `1/B`; correlated ones
    floor at `ρσ²`, so decorrelation is the lever.

35. Restricts the candidate features at each split (`√d`, or `d/3` for regression), decorrelating
    the trees so the variance floor drops.

36. Each bootstrap leaves out ~36.8% of rows; predicting those with the trees that never saw them
    gives a cross-validated estimate at no extra cost.

37. `wᵢ ← wᵢ exp(−α_m yᵢ h_m(xᵢ))` with `α_m = ½log((1−err)/err)`; it is forward stagewise additive
    modelling under **exponential** loss, which is why it is sensitive to outliers.

38. Fit each new learner to the negative gradient `−∂L/∂F` at the current ensemble, then take a
    shrunken step `F_m = F_{m−1} + ν γ_m h_m`. For squared loss the target is the residual.

39. RF: no. GBM: yes — early stopping is required.

40. XGBoost: second-order objective with explicit leaf regularisation. LightGBM: histogram binning
    with leaf-wise growth (fast, overfits small data). CatBoost: ordered target statistics for
    categorical features, avoiding target leakage.

41. It favours high-cardinality/continuous features and is computed on training data; use
    permutation importance on held-out data or SHAP.

42. Splits use only value order, so scale is irrelevant; leaves predict constants, so predictions
    are bounded by the training range.

43. Fixing the scale so the closest points satisfy `y(wᵀx+b) = 1`, the two margin planes are
    `2/‖w‖` apart, since the distance from a point to the plane is `(wᵀx+b)/‖w‖`.

44. `min ½‖w‖² + CΣξᵢ` s.t. `yᵢ(wᵀxᵢ+b) ≥ 1−ξᵢ`, `ξᵢ ≥ 0`. Large `C` = less tolerance = less
    regularisation.

45. `max Σαᵢ − ½ΣΣαᵢαⱼyᵢyⱼxᵢᵀxⱼ` s.t. `Σαᵢyᵢ = 0`, `0 ≤ αᵢ ≤ C`. (i) data appears only as inner
    products ⇒ kernels; (ii) most `αᵢ = 0` ⇒ sparse solution in the support vectors.

46. `αᵢ[yᵢ(wᵀxᵢ+b) − 1 + ξᵢ] = 0`, so a non-zero multiplier requires an active constraint — the
    point sits on (or inside) the margin.

47. `(xᵀz)² = φ(x)ᵀφ(z)` with `φ(x) = (x₁², √2x₁x₂, x₂²)` — the map is never computed.

48. `γ = 1/(2σ²)`, the inverse radius of influence. Small `γ` ⇒ near-linear/underfit; large `γ` ⇒
    islands around points/overfit.

49. `K` is valid iff every finite Gram matrix is symmetric PSD; then `K` is an inner product in some
    feature space.

50. Kernel training is `O(n²d)`–`O(n³)` with an `n×n` kernel matrix in memory. Use a linear SVM via
    SGD, or gradient boosting, for large `n`.

51. Hinge is exactly zero past the margin (so only support vectors matter) and is not
    differentiable at the kink; logistic loss is smooth and never exactly zero, so all points
    contribute and probabilities come out calibrated.

52. `J = ΣΣ‖x − μ_k‖²`; setting the derivative of the within-cluster sum to zero gives the mean;
    each step decreases `J` and assignments are finite, so it terminates — at a local optimum.

53. Seed the first centroid uniformly, then each next one with probability `∝ D(x)²`; gives an
    `O(log k)` expected approximation and far better practical results.

54. Spherical clusters (fails on elongated), similar sizes/densities (fails on unequal),
    meaningful Euclidean distance (fails on categorical/text), every point belongs (fails with
    outliers). Also requires a known `k` and standardised features.

55. E: `γ_ik ∝ π_k N(xᵢ|μ_k,Σ_k)`. M: `π_k = N_k/n`, `μ_k` and `Σ_k` are `γ`-weighted statistics.
    k-means = hard assignments with isotropic equal covariance as `σ² → 0`.

56. Core: ≥ `minPts` neighbours within `eps`; border: within `eps` of a core point; noise: neither.
    Weaknesses: varying density, and distance concentration in high dimensions.

57. Single → chaining/elongated; complete → compact equal-diameter; average → middle; Ward →
    spherical, similar sizes (default).

58. `s = (b − a)/max(a,b)` per point, `a` = mean intra-cluster distance, `b` = mean distance to the
    nearest other cluster; average it and maximise over `k`.

59. Cluster sizes and inter-cluster distances are not meaningful; and there is no valid
    out-of-sample transform.

60. Without labels: silhouette, Davies–Bouldin, Calinski–Harabasz, stability under resampling.
    With labels: ARI, NMI, V-measure — never plain accuracy, since cluster labels are arbitrary.

61. `P = TP/(TP+FP)`, `R = TP/(TP+FN)`, `Spec = TN/(TN+FP)`, `F1 = 2PR/(P+R)`,
    `Fβ = (1+β²)PR/(β²P+R)`.

62. The harmonic mean is dominated by the smaller term, so a degenerate model scores 0. F1 ignores
    TN.

63. The probability that a random positive is scored above a random negative.

64. Under heavy imbalance, because FPR's denominator is enormous; use PR-AUC / average precision,
    whose baseline is the positive rate.

65. Macro treats every class equally (good when rare classes matter); micro pools counts and is
    dominated by frequent classes (equals accuracy in single-label multiclass).

66. MSE → conditional mean; MAE → conditional median; pinball at `q` → conditional `q`-quantile.

67. `DCG@k = Σ rel_i/log₂(i+1)` normalised by the ideal ordering; handles graded relevance and
    position discounting.

68. Predicted probabilities match observed frequencies. Badly calibrated: neural nets
    (over-confident) and SVMs/naive Bayes. Fixes: temperature scaling (nets), Platt scaling,
    isotonic regression.

69. Metric → threshold → class weights / focal loss → resampling → reframe as anomaly detection →
    collect more positives.

70. Applied before splitting, synthetic minority points derived from test rows end up in training
    (and vice versa), leaking the test set and inflating the score.
