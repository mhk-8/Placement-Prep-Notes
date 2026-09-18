
# Unsupervised Learning: Clustering and Dimensionality Reduction ⭐⭐

> **Core idea in 3 lines**
> 1. Clustering has no ground truth, so the hard parts are choosing `k`, choosing the distance,
>    and validating the result.
> 2. k-means optimises within-cluster variance with a hard-assignment EM loop; GMMs do the soft
>    version with full covariances; DBSCAN uses density and needs no `k`.
> 3. Dimensionality reduction splits into linear-and-invertible (PCA) and
>    non-linear-for-visualisation-only (t-SNE, UMAP).

---

## 1. k-means ⭐⭐⭐

**Objective (within-cluster sum of squares):**

```
J = Σ_{k=1}^{K} Σ_{x ∈ C_k} ‖x − μ_k‖²
```

**Lloyd's algorithm:**

```
initialise centroids μ₁ … μ_K
repeat:
    ASSIGN:  C_k = { x : k = argmin_j ‖x − μ_j‖² }      (hold μ fixed, minimise over assignment)
    UPDATE:  μ_k = mean of the points in C_k             (hold assignment fixed, minimise over μ)
until assignments stop changing
```

📐 **Why the update is the mean.** With the assignment fixed,
`∂/∂μ_k Σ_{x∈C_k}‖x − μ_k‖² = −2Σ(x − μ_k) = 0 ⇒ μ_k = (1/|C_k|)Σx`. ∎

📐 **Why it converges.** Each step is a coordinate descent on `J` that cannot increase it, and
there are finitely many possible assignments, so the algorithm terminates. ⚠️ It converges to a
**local** optimum — the global problem is NP-hard. Hence multiple restarts (`n_init`).

**k-means++ initialisation ⭐**: pick the first centroid uniformly at random; pick each subsequent
centroid with probability proportional to `D(x)²`, the squared distance to the nearest chosen
centroid. This spreads the seeds out and gives an `O(log k)`-competitive expected objective.

**Complexity:** `O(n·k·d·iterations)`.

### Assumptions and failure modes ⚠️⭐⭐

| Assumption | Fails when |
|---|---|
| clusters are spherical | elongated/anisotropic clusters → use GMM with full covariance |
| clusters have similar size and density | one big and one small cluster → the big one gets split |
| Euclidean distance is meaningful | categorical data → use k-modes/k-prototypes; text → cosine (spherical k-means) |
| every point belongs to a cluster | noise/outliers distort centroids → DBSCAN |
| `k` is known | → elbow / silhouette / gap statistic |

⚠️ **Must standardise first** — k-means minimises Euclidean distance, so a large-scale feature
dominates.
⚠️ Two concentric rings cannot be separated by k-means at all (spectral clustering or DBSCAN can).

### Choosing `k`

```
Elbow method:  plot J vs k, look for the knee.  J always decreases, so the knee is a
               judgement call and is often ambiguous. ⚠️
Silhouette:    s(i) = (b − a)/max(a, b),  a = mean intra-cluster distance,
               b = mean distance to the nearest OTHER cluster.  s ∈ [−1,1];
               pick k maximising the mean silhouette. ⭐
Gap statistic: compare log J to its expectation under a uniform reference distribution.
Davies–Bouldin / Calinski–Harabasz: ratio-based indices.
Downstream:    if the clusters feed a task, tune k on that task's metric — the best answer.
```

---

## 2. Gaussian mixture models ⭐

```
p(x) = Σ_k π_k N(x | μ_k, Σ_k) ,   Σπ_k = 1
```

Fitted by EM (see the probability folder):

```
E-step:  γ_ik = π_k N(xᵢ|μ_k,Σ_k) / Σ_j π_j N(xᵢ|μ_j,Σ_j)      ← responsibilities (soft)
M-step:  N_k = Σᵢ γ_ik
         π_k = N_k/n ,  μ_k = (1/N_k)Σᵢ γ_ik xᵢ ,
         Σ_k = (1/N_k)Σᵢ γ_ik (xᵢ−μ_k)(xᵢ−μ_k)ᵀ
```

⭐ **k-means is GMM-EM with `Σ_k = σ²I`, `σ² → 0` and hard assignments.** Say this; it is the
expected connection.

Advantages: soft membership, elliptical clusters, a proper likelihood (so BIC/AIC can select `k`).
⚠️ A single point can drive a component's covariance to zero and the likelihood to infinity —
regularise the covariance (`reg_covar`).

---

## 3. DBSCAN ⭐⭐

Density-based; parameters `eps` (radius) and `minPts`.

```
core point      : ≥ minPts points within eps
border point    : within eps of a core point but not itself core
noise           : neither
clusters        = connected components of core points, plus their borders
```

| ✅ | ⚠️ |
|---|---|
| no `k` needed | two parameters that are hard to set |
| arbitrary cluster shapes | struggles with varying density (use **HDBSCAN**) |
| labels outliers explicitly as noise | curse of dimensionality: distances concentrate, so `eps` stops discriminating |
| deterministic given the parameters | `O(n log n)` with an index, `O(n²)` without |

Heuristic for `eps`: plot the sorted distance to the `minPts`-th nearest neighbour and take the
knee. `minPts ≈ 2·d` is a reasonable starting point.

---

## 4. Hierarchical clustering

```
agglomerative: start with n singletons, repeatedly merge the closest pair → dendrogram
divisive:      start with one cluster and split
cut the dendrogram at a height to get any k, without refitting ⭐
```

**Linkage** determines the result:

| Linkage | Distance between clusters | Tendency |
|---|---|---|
| single | min pairwise | chaining, finds elongated shapes |
| complete | max pairwise | compact, equal-diameter clusters |
| average | mean pairwise | middle ground |
| **Ward** | increase in within-cluster variance | spherical, similar-size clusters (the usual default) |

Cost `O(n³)` naive, `O(n² log n)` with heaps — so it does not scale past tens of thousands of rows.

---

## 5. Dimensionality reduction ⭐⭐

**PCA** — derived fully in `01_Math_Foundations/04-pca-derivation.md`. Linear, invertible,
deterministic, preserves global variance structure. Use it for compression, decorrelation, noise
reduction and as a preprocessing step.

| Method | Linear? | Preserves | Use for | Watch out |
|---|---|---|---|---|
| PCA | yes | global variance | preprocessing, compression | misses non-linear structure |
| Kernel PCA | no | structure in feature space | non-linear compression | kernel choice, `O(n²)` |
| **t-SNE** | no | **local** neighbourhoods | 2-D visualisation only | cluster **sizes and distances are meaningless** ⚠️⚠️; `perplexity` changes everything; no reusable `transform` |
| **UMAP** | no | local + some global | visualisation, sometimes features | stochastic; still not a faithful metric |
| LDA | yes | class separation | supervised reduction | at most `C−1` components |
| Autoencoder | no | whatever the loss encodes | learned non-linear compression | needs data and tuning |
| Truncated SVD / LSA | yes | variance, works on sparse | text (TF-IDF matrices) | no centring (keeps sparsity) |
| Random projection | yes | pairwise distances (Johnson–Lindenstrauss) | very high `d`, cheap | approximate |

⚠️ **The single most-repeated t-SNE trap:** in a t-SNE plot you may **not** conclude that a big
cluster contains more variance, or that two far-apart clusters are more different than two close
ones. The algorithm optimises a KL divergence between neighbourhood distributions; global geometry
is not preserved. Also never fit t-SNE on train and "apply" to test — there is no out-of-sample
mapping.

---

## 6. Evaluating clustering ⭐

**Internal (no labels):** silhouette, Davies–Bouldin, Calinski–Harabasz, inertia.
**External (labels available):** Adjusted Rand Index, Normalised Mutual Information, homogeneity /
completeness / V-measure. ⚠️ Plain accuracy is meaningless — cluster labels are arbitrary
permutations; ARI and NMI are permutation-invariant, and ARI is corrected for chance.
**Stability:** re-cluster on bootstrap samples and measure agreement — a practical check that the
structure is real rather than an artefact of the sample.

---

## 7. Other unsupervised tasks worth naming

- **Anomaly detection:** Isolation Forest (anomalies are isolated in few random splits — short
  path length), One-Class SVM, Local Outlier Factor, autoencoder reconstruction error. ⭐
- **Association rules:** Apriori / FP-growth with support, confidence and lift; lift > 1 means the
  items co-occur more than independence predicts.
- **Topic modelling:** LDA (Dirichlet priors over topic and word mixtures), NMF on TF-IDF.
- **Self-supervised learning:** the modern engine of representation learning — contrastive
  (SimCLR, CLIP) or masked-prediction (BERT, MAE) objectives create labels from the data itself.

---

## Recall questions

1. State the k-means objective and prove that the M-step is the mean.
2. Why does k-means converge, and what does it converge to?
3. Explain k-means++ and why it helps.
4. List four assumptions of k-means and a failure case for each.
5. Write the E and M steps of a GMM and say exactly how k-means is a special case.
6. Define core, border and noise points in DBSCAN, and name its two weaknesses.
7. Which linkage would you use for compact clusters, and which chains?
8. Silhouette score: define it and say how you use it to pick `k`.
9. Give two things you must never infer from a t-SNE plot.
10. Why is accuracy invalid for clustering, and what do you use instead?
