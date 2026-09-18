
# Trees, Bagging, Random Forests and Boosting ⭐⭐⭐

> **Core idea in 3 lines**
> 1. A decision tree greedily partitions feature space to make the labels in each region as pure
>    as possible; it has low bias and very high variance.
> 2. Bagging averages many high-variance trees to kill variance; random forests go further by
>    decorrelating them.
> 3. Boosting builds trees sequentially, each fitting the current residual, which attacks bias —
>    and gradient boosting is still the best default for tabular data.

---

## 1. Decision trees

### The greedy algorithm

```
build(node, data):
    if stopping criterion:  make a leaf (majority class / mean value)
    for each feature j, for each candidate threshold t:
        compute impurity of the split (left, right)
    choose (j*, t*) minimising the weighted child impurity
    recurse on the two children
```

Finding the globally optimal tree is NP-hard, so the algorithm is greedy — worth saying. ⭐

### Impurity measures 📐

For a node with class proportions `p₁ … p_K`:

```
Gini     G = 1 − Σ p_k²          = Σ p_k(1 − p_k)
Entropy  H = −Σ p_k log₂ p_k
Misclassification  1 − max p_k
```

For regression, the impurity is the variance / MSE within the node.

```
Information gain = H(parent) − Σ_children (n_child/n_parent) · H(child)
Gini gain        = same with G
```

| | Gini | Entropy |
|---|---|---|
| Range (binary) | `[0, 0.5]` | `[0, 1]` |
| Cost | no logarithms — faster | logarithm per class |
| Behaviour | nearly identical trees in practice | slightly more sensitive to rare classes |

⚠️ Misclassification error is **not** used for growing, because it is not strictly concave: it can
report zero gain for a split that clearly improves purity. Gini and entropy are strictly concave,
so any split that changes the distribution has positive gain. This is a sharp interview point. ⭐

*Worked example.* Node with 8 positives and 2 negatives; a split gives `(5+, 0−)` and `(3+, 2−)`.

```
parent Gini = 1 − (0.8² + 0.2²) = 0.32
left  Gini  = 0
right Gini  = 1 − (0.6² + 0.4²) = 0.48
weighted    = (5/10)(0) + (5/10)(0.48) = 0.24
gain        = 0.32 − 0.24 = 0.08
```

### Stopping and pruning

```
pre-pruning:  max_depth, min_samples_split, min_samples_leaf,
              min_impurity_decrease, max_leaf_nodes
post-pruning: cost-complexity pruning — minimise  R(T) + α|T|
              (|T| = number of leaves; α chosen by CV)
```

An unconstrained tree grows until every leaf is pure — training error 0, test error poor. That is
the purest illustration of variance in the whole of ML.

### Strengths / weaknesses ⭐

| ✅ | ⚠️ |
|---|---|
| no scaling needed (split points are order-based) | very high variance; a small data change reshapes the tree |
| handles mixed types, missing values (surrogate splits) | axis-aligned splits only — a diagonal boundary needs a staircase |
| captures interactions automatically | greedy, so not globally optimal |
| interpretable when shallow | cannot extrapolate beyond the training range (regression) ⚠️ |
| fast inference `O(depth)` | biased towards high-cardinality features |

⚠️ **Trees do not need feature scaling** — a favourite true/false question. Splits depend only on
the ordering of values.

⚠️ **Impurity-based feature importance is biased** towards high-cardinality and continuous
features. Prefer **permutation importance** or **SHAP** values. ⭐⭐

---

## 2. Bagging (bootstrap aggregating)

```
for b = 1 … B:
    D_b = bootstrap sample of size n (with replacement)
    train model f_b on D_b
prediction: average (regression) / majority vote (classification)
```

### 📐 Why it reduces variance

If the `B` models each have variance `σ²` and pairwise correlation `ρ`:

```
Var( (1/B) Σ f_b ) = ρσ² + (1 − ρ)σ²/B
```

*Derivation.* `Var(Σf_b) = Σ Var(f_b) + Σ_{i≠j} Cov(f_i, f_j) = Bσ² + B(B−1)ρσ²`; divide by `B²`. ∎

Two readings, both worth saying: with independent models (`ρ = 0`) the variance falls as `1/B`;
with correlated models it **floors at `ρσ²`** no matter how many you add. **Therefore the way to
improve bagging is to reduce `ρ`** — which is exactly what random forests do. ⭐⭐⭐

**Out-of-bag estimate:** each model misses ~36.8% of the rows (derivation in the probability
folder), so averaging each row's predictions from the trees that did not see it gives a free
cross-validated score.

Bagging leaves bias roughly unchanged (each model is fit on a sample of the same distribution), so
it needs **low-bias, high-variance** base learners — deep trees. Bagging a linear model is nearly
pointless. ⭐

---

## 3. Random forests ⭐⭐⭐

Bagging **plus** feature subsampling at every split:

```
at each split, consider only a random subset of m features
    classification:  m ≈ √d
    regression:      m ≈ d/3
```

That extra randomness is the entire point: if one feature is hugely predictive, every bagged tree
splits on it first and the trees become highly correlated (`ρ` large ⇒ the variance floor `ρσ²`
stays high). Restricting the candidate features forces trees to be different. ⭐⭐⭐

```
                 training data
                       │
      ┌────────┬───────┼───────┬────────┐
      ▼        ▼       ▼       ▼        ▼
   boot 1   boot 2  boot 3   ...     boot B      ← row sampling (bagging)
      │        │       │       │        │
    tree 1   tree 2  tree 3   ...     tree B     ← at each split, random m of d
      │        │       │       │        │           features (decorrelation)
      └────────┴───────┼───────┴────────┘
                       ▼
             average / majority vote
```

**Hyperparameters that matter:** `n_estimators` (more is never worse, just slower — no overfitting
from adding trees ⭐), `max_features` (the key knob), `max_depth`/`min_samples_leaf` (usually left
unconstrained), `class_weight` for imbalance.

**Extremely Randomised Trees (ExtraTrees):** also picks the split *threshold* at random, trading a
little more bias for still lower variance and faster training.

---

## 4. Boosting ⭐⭐⭐

Sequential: each new weak learner focuses on what the ensemble currently gets wrong.

### AdaBoost

```
init weights wᵢ = 1/n
for m = 1 … M:
    fit a weak learner h_m on the weighted data
    err_m = Σ wᵢ·1[h_m(xᵢ) ≠ yᵢ] / Σ wᵢ
    α_m   = ½ log((1 − err_m)/err_m)
    wᵢ ← wᵢ · exp(−α_m yᵢ h_m(xᵢ))      (misclassified points gain weight)
    normalise w
final: sign( Σ α_m h_m(x) )
```

Note `α_m` is large when the learner is accurate, and the weight update up-weights exactly the
points that were wrong. AdaBoost is provably equivalent to forward stagewise additive modelling
under **exponential loss** — which is also why it is sensitive to label noise and outliers (the
exponential loss punishes them enormously). ⚠️⭐

### Gradient boosting 📐 — the derivation to know

Build an additive model `F_M(x) = Σ_m γ_m h_m(x)`. At stage `m` we want to reduce `L(y, F(x))`.
Treat `F` as a parameter and take a functional gradient-descent step:

```
pseudo-residual:   rᵢ = − ∂L(yᵢ, F(xᵢ)) / ∂F(xᵢ)   evaluated at F = F_{m−1}
fit h_m to {(xᵢ, rᵢ)}   (a regression tree, by least squares)
F_m = F_{m−1} + ν · γ_m h_m          ν = learning rate (shrinkage)
```

For squared loss `L = ½(y − F)²` the pseudo-residual is simply `y − F`, so **gradient boosting is
literally "fit the next tree to the residuals"** — say the general version, then this special case.
For log-loss the residual is `y − p`, the same clean form we keep seeing. ⭐⭐⭐

```
 F₀ = mean(y)
   │
   ▼
 residual r₁ = y − F₀ ──► tree h₁ ──► F₁ = F₀ + ν h₁
                                        │
                        residual r₂ = y − F₁ ──► tree h₂ ──► F₂ = F₁ + ν h₂
                                                              │
                                                             ...  → F_M
```

**Key hyperparameters:** `n_estimators` × `learning_rate` trade off against each other (low `ν`
with many trees generalises better, the standard advice being `ν ≈ 0.05–0.1`); `max_depth` 3–8
(shallow trees are the weak learners); `subsample < 1` gives stochastic gradient boosting;
`colsample_bytree`; `min_child_weight`; `reg_lambda`/`reg_alpha`.

⚠️ **Unlike random forests, boosting DOES overfit as you add trees.** Use early stopping on a
validation set. This contrast is a very common question. ⭐⭐⭐

### XGBoost / LightGBM / CatBoost

| | What it adds |
|---|---|
| **XGBoost** | second-order (Newton) objective using gradient *and* Hessian; explicit `γT + ½λ‖w‖²` regularisation on leaves; sparsity-aware default split direction for missing values; approximate quantile split finding; parallel feature-wise histogram building |
| **LightGBM** | histogram binning; **leaf-wise** growth (splits the leaf with max gain, not level-wise) ⇒ much faster and usually more accurate, but overfits small datasets — control with `num_leaves`; GOSS sampling; native categorical handling |
| **CatBoost** | ordered target statistics for categorical features to avoid target leakage; ordered boosting to remove prediction shift; symmetric (oblivious) trees ⇒ very fast inference |

📐 **XGBoost's leaf value** (worth knowing): with `gᵢ`, `hᵢ` the first and second derivatives of the
loss, the optimal weight of a leaf `j` and its gain are

```
w*_j = − (Σ_{i∈j} gᵢ) / (Σ_{i∈j} hᵢ + λ)
gain = ½ [ G_L²/(H_L+λ) + G_R²/(H_R+λ) − (G_L+G_R)²/(H_L+H_R+λ) ] − γ
```

The `−γ` term means a split must beat a threshold to be taken — pruning built into the objective.

---

## 5. Bagging vs boosting ⭐⭐⭐ (asked almost every time)

| | Bagging / RF | Boosting |
|---|---|---|
| Training | parallel, independent | sequential, each depends on the last |
| Attacks | **variance** | **bias** |
| Base learner | deep (low-bias, high-variance) trees | shallow (high-bias) stumps/trees |
| Sampling | bootstrap rows (+ random features) | reweight / fit residuals |
| Overfits with more estimators? | no | **yes** — use early stopping |
| Noise/outlier sensitivity | robust | sensitive (especially AdaBoost) |
| Tuning effort | low — works out of the box | higher, but higher ceiling |
| Typical accuracy on tabular data | good | usually best ⭐ |

**Stacking** is the third family: train diverse base models, then a meta-learner on their
out-of-fold predictions. ⚠️ The meta-features must be out-of-fold, or the meta-learner sees leaked
predictions.

---

## 6. When to use trees at all ⭐

Use gradient boosting when: tabular/heterogeneous features, mixed types, non-linear interactions,
moderate data size, and you need strong accuracy quickly. Prefer linear models when you need
interpretable coefficients and extrapolation; prefer neural networks for perceptual data (images,
audio, text) and for very large datasets with learned representations.

⚠️ **Trees cannot extrapolate.** A regression tree predicts a constant outside the training range —
so for a trending time series, model the trend separately (or use a linear model on top).

---

## Recall questions

1. Write Gini and entropy, and explain why misclassification rate is not used for growing.
2. Compute the Gini gain for a stated split.
3. Derive `Var = ρσ² + (1−ρ)σ²/B` and draw the conclusion for random forests.
4. What exactly does `max_features` do, and why does it help?
5. Why is out-of-bag error free, and what fraction of rows is OOB?
6. Derive gradient boosting as functional gradient descent, then specialise to squared loss.
7. Which ensemble overfits when you add estimators, and which does not? Why?
8. Three things XGBoost adds over plain gradient boosting.
9. Why is impurity-based feature importance misleading, and what do you use instead?
10. Why do trees need no feature scaling, and why can't they extrapolate?
