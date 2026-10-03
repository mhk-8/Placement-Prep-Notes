
# ML Algorithms — Comparison Table

> **Use:** ML-round recall. The column interviewers actually press on is **assumptions** —
> "when does this fail?" is the question that separates candidates. ⭐

---

## 1. Master table ⭐⭐⭐

| Algorithm | Type | Key assumption | Train | Predict | Scaling needed | Interpretable | Fails when |
|---|---|---|---|---|---|---|---|
| **Linear regression** | Reg | Linearity, independence, homoscedasticity, normal residuals | O(nd²+d³) | O(d) | helpful | ✅ high | Non-linear, multicollinear, outliers ⚠️ |
| **Ridge (L2)** | Reg | + coefficient shrinkage | same | O(d) | ✅ **required** | ✅ | Needs feature selection (keeps all) |
| **Lasso (L1)** | Reg | + sparsity | iterative | O(d) | ✅ **required** | ✅ | Correlated features — picks one arbitrarily ⚠️ |
| Elastic net | Reg | L1+L2 blend | iterative | O(d) | ✅ | ✅ | Two hyperparameters to tune |
| **Logistic regression** | Clf | Linear decision boundary in log-odds | O(nd·iters) | O(d) | ✅ | ✅ high | Non-linear boundary, perfect separation ⚠️ |
| **k-NN** | Both | Locality; a meaningful distance | O(1) (lazy) | **O(nd)** ⚠️ | ✅ **critical** | ⚠️ local only | High dimension (curse), large n, imbalance |
| **Naive Bayes** | Clf | **Conditional independence** of features ⭐ | O(nd) | O(d) | ❌ | ✅ | Correlated features; still often works anyway |
| **Decision tree** | Both | Axis-aligned splits | O(nd log n) | O(depth) | ❌ **not needed** ⭐ | ✅ high | Overfits without pruning; unstable ⚠️ |
| **Random forest** | Both | Bagging + feature subsampling decorrelates trees | O(T·nd log n) | O(T·depth) | ❌ | ⚠️ medium | Extrapolation; very high-dimensional sparse text |
| **Gradient boosting / XGBoost / LightGBM** | Both | Sequential residual fitting | slow, sequential | fast | ❌ | ⚠️ medium | ⚠️ overfits with too many rounds; needs tuning. **Best on tabular data** ⭐ |
| AdaBoost | Both | Reweight misclassified samples | O(T·nd) | O(T) | ❌ | ⚠️ | Noisy labels and outliers ⚠️ |
| **SVM (linear)** | Both | Max-margin separation | O(nd) – O(n²d) | O(d) | ✅ **required** | ⚠️ low | n > 10⁵ gets slow |
| **SVM (RBF kernel)** | Both | Smoothness in kernel space | **O(n²)–O(n³)** ⚠️ | O(n_sv·d) | ✅ **required** | ❌ | Large n; no probability output natively |
| **k-means** | Clu | Spherical, equal-variance, equal-size clusters ⚠️ | O(n·k·d·iters) | O(kd) | ✅ **required** | ✅ centroids | Non-convex shapes, outliers, unknown k ⭐ |
| **DBSCAN** | Clu | Density-connected regions | O(n log n) with index | — | ✅ | ⚠️ | Varying densities; ε is hard to pick |
| Hierarchical | Clu | A meaningful linkage | **O(n³)** / O(n² log n) | — | ✅ | ✅ dendrogram | Large n |
| GMM | Clu | Gaussian mixture components | O(n·k·d·iters) | O(kd) | ✅ | ⚠️ | Needs k; may converge to a local optimum |
| **PCA** | DR | Variance = information; linear subspace | O(nd²+d³) | O(d·k) | ✅ **required** ⭐ | ⚠️ | Non-linear structure; components are not features |
| t-SNE / UMAP | DR | Local neighbourhood preservation | O(n log n) | — | ✅ | ❌ | ⚠️ visualisation only — **do not** feed to a classifier; distances between clusters are not meaningful ⭐ |
| LDA (discriminant) | Both | Class-conditional Gaussians, shared covariance | O(nd²) | O(d) | ✅ | ✅ | Assumption violated; needs n > d |
| **MLP** | Both | Universal approximation; needs data | O(n·params·epochs) | O(params) | ✅ **required** | ❌ | Small data; tabular (trees win) ⚠️ |
| **CNN** | Both | **Translation invariance**, local spatial structure ⭐ | heavy | fast | ✅ | ❌ | Non-grid data; small data without transfer learning |
| **RNN / LSTM** | Seq | Sequential dependence, Markov-ish horizon | sequential ⚠️ | O(T) | ✅ | ❌ | Long dependencies (vanishing gradient), no parallelism |
| **Transformer** | Seq | Attention over all positions; needs positional encoding ⭐ | **O(T²·d)** ⚠️ | O(T²·d) | ✅ | ❌ | Long sequences (quadratic), small data, high compute |
| Isolation forest | Anom | Anomalies are easier to isolate | O(n log n) | O(log n) | ❌ | ⚠️ | Contamination rate must be guessed |

---

## 2. Bias-variance ⭐⭐⭐

📐 **Decomposition for squared error:**
`E[(y − f̂(x))²] = Bias[f̂]² + Var[f̂] + σ²`  (irreducible noise σ²)

| | High bias (underfit) | High variance (overfit) |
|---|---|---|
| Symptom | Train error high, val ≈ train | Train error low, val ≫ train ⭐ |
| Model | Too simple | Too complex |
| Fix | More features, more capacity, less regularisation, train longer | More data, regularisation, simpler model, early stopping, bagging, dropout, augmentation ⭐ |

```
Bagging  → reduces VARIANCE (parallel, independent models, average)      Random forest
Boosting → reduces BIAS      (sequential, each fits the residual)        XGBoost ⭐
Stacking → a meta-learner on out-of-fold predictions
```
> ⭐ **The one-line answer to "bagging vs boosting":** bagging averages independently-trained
> high-variance models to cut variance; boosting fits models sequentially to each other's
> residuals to cut bias — which is also why boosting can overfit and bagging rarely does.

---

## 3. Regularisation ⭐⭐

| Method | Mechanism | Effect |
|---|---|---|
| **L1 (Lasso)** | +λΣ\|w\| | **Sparse** — drives weights to exactly 0 → feature selection ⭐ |
| **L2 (Ridge)** | +λΣw² | Shrinks all weights smoothly, never to 0; handles multicollinearity |
| Elastic net | both | Sparsity + stability with correlated features |
| **Dropout** | Zero units with prob p at train | Approximate ensembling; scale at inference ⭐ |
| **Early stopping** | Halt at best val | Implicit capacity control |
| **Batch norm** | Normalise activations per mini-batch | Faster training, mild regularisation ⚠️ differs train vs eval |
| Layer norm | Normalise per sample across features | ⭐ what Transformers use (batch-independent) |
| Weight decay | L2 in the optimiser | ⚠️ not identical to L2 under Adam → AdamW |
| Data augmentation | Label-preserving input transforms | Often the single highest-value fix ⭐ |
| Label smoothing | Soften one-hot targets | Better calibration, less over-confidence |

> ⭐ **Why L1 gives sparsity, geometrically:** the L1 constraint region is a diamond with vertices
> on the axes; the loss contours touch it at a corner, where coordinates are exactly zero. The L2
> region is a sphere with no corners, so the contact point is generically off-axis.

---

## 4. Gradient descent and optimisers ⭐⭐

| Optimiser | Update idea | Notes |
|---|---|---|
| SGD | w ← w − η∇L | Noisy; the noise itself helps generalisation ⭐ |
| SGD + momentum | velocity accumulates | Dampens oscillation across ravines |
| Nesterov | look-ahead gradient | Slightly better convergence |
| Adagrad | per-parameter η / √Σg² | ⚠️ learning rate decays to zero |
| RMSProp | exponential moving average of g² | Fixes Adagrad's decay |
| **Adam** | momentum + RMSProp + bias correction | Default choice; β₁=0.9, β₂=0.999, ε=1e-8 ⭐ |
| **AdamW** | decoupled weight decay | ⭐ the correct Adam for regularised training |

```
BATCH vs MINI-BATCH vs SGD : full gradient (stable, slow) · typical 32-512 (⭐ default) · n=1 (noisy)
Learning rate too high → divergence/oscillation; too low → slow, stuck in a plateau
Schedules: step · cosine annealing ⭐ · warmup (essential for Transformers) · one-cycle
Vanishing gradient : sigmoid/tanh saturate → use ReLU, residual connections, proper init
Exploding gradient : gradient clipping by global norm ⭐ (RNNs)
Init: Xavier/Glorot for tanh, He/Kaiming for ReLU. ⚠️ all-zeros kills symmetry breaking
Dead ReLU: negative pre-activations stop learning → LeakyReLU, ELU, GELU
```

---

## 5. Activations and losses

| Activation | Range | Note |
|---|---|---|
| Sigmoid | (0,1) | Saturates ⚠️; binary output layer |
| Tanh | (−1,1) | Zero-centred, still saturates |
| **ReLU** | [0,∞) | Default hidden; cheap, sparse ⚠️ dead units |
| LeakyReLU / ELU | ℝ | Fixes dead units |
| **GELU** / SiLU | ℝ | ⭐ Transformers |
| Softmax | simplex | Multi-class output; ⚠️ use logits + log-softmax for stability |

| Loss | Task |
|---|---|
| MSE / MAE / Huber | Regression |
| Binary cross-entropy | Binary / multi-label |
| Categorical cross-entropy | Multi-class (mutually exclusive) |
| Focal loss | ⭐ Extreme class imbalance in detection — down-weights easy examples |
| Hinge | SVM |
| KL divergence | Distribution matching, distillation |
| Contrastive / triplet / InfoNCE | Metric and self-supervised learning |
| CTC | Unaligned sequences (speech, OCR) |

---

## 6. Deep learning architectures ⭐⭐ (your ML-resume ground)

```
CNN         : conv (local weight sharing) → nonlinearity → pool. Inductive bias = translation
              equivariance + locality. Receptive field grows with depth/dilation ⭐
              Output size = floor((W − K + 2P)/S) + 1   📐 be able to compute this on the spot
              Params in a conv layer = (K·K·C_in + 1) · C_out  📐
ResNet      : y = F(x) + x. The skip gives the gradient an identity path → trains 100+ layers ⭐
Batch/Layer norm, dropout, global average pooling instead of a big FC head
RNN/LSTM/GRU: LSTM gates = forget, input, output + cell state → mitigates vanishing gradient.
              GRU merges to update + reset, fewer parameters
TRANSFORMER : 📐 Attention(Q,K,V) = softmax(QKᵀ/√d_k)·V
              Why √d_k: without it the dot products grow with d_k, pushing softmax into a
              saturated regime with vanishing gradients ⭐ a very common question
              Multi-head: h independent projections → concat → linear. Different subspaces
              Positional encoding: sinusoidal or learned — attention is permutation-invariant ⭐
              Encoder-decoder · encoder-only (BERT, masked LM) · decoder-only (GPT, causal mask)
              Complexity O(T²·d) in sequence length ⚠️ the motivation for Flash/linear attention
              Pre-norm vs post-norm; residual + layer norm everywhere
FINE-TUNING : full · linear probe · LoRA (low-rank adapters) · prefix/prompt tuning ⭐
```

> ⭐ **Your multi-task-learning angle:** hard parameter sharing (shared trunk, per-task heads) vs
> soft sharing; task-weighting and gradient-conflict problems (GradNorm, PCGrad, uncertainty
> weighting). This is a differentiating thing to raise and you have actually built one.

---

## 7. Practical choices ⭐

```
TABULAR DATA              → gradient boosting (LightGBM/XGBoost) first. ⭐ NN rarely wins here
IMAGES                    → pretrained CNN or ViT + fine-tune. Augment aggressively
TEXT                      → pretrained Transformer; TF-IDF + linear as the baseline ⭐
TIME SERIES               → gradient boosting on lag features beats most deep models at small scale
SMALL DATA (< 1 k rows)   → simple model + strong regularisation + CV; not a deep net
IMBALANCE                 → class weights · threshold tuning ⭐ · focal loss · SMOTE (train fold only)
                            ⚠️ do NOT resample the validation set
MANY CATEGORIES           → target/ordinal encoding with CV folds, or embeddings; not one-hot
MISSING VALUES            → trees handle them natively; otherwise impute inside the pipeline
HIGH DIMENSION, FEW ROWS  → L1/elastic net, or PCA, or feature selection in the CV loop
NEEDS EXPLANATION         → linear/tree + SHAP; say "SHAP for local, permutation importance for
                            global" ⭐
```

### Always state the baseline ⭐
```
Classification : majority class, or a logistic regression on raw features
Regression     : predict the mean / last value
Ranking        : BM25 ⭐ you have this one from your IR project
→ "My model gets 0.84 F1; the TF-IDF + logistic baseline gets 0.79" is a complete answer.
  A number with no baseline is not.
```

---

## 8. Classical ML maths to be able to derive 📐

```
LINEAR REGRESSION (OLS)    : ŵ = (XᵀX)⁻¹Xᵀy, from ∇_w ‖y − Xw‖² = 0
RIDGE                      : ŵ = (XᵀX + λI)⁻¹Xᵀy — λI makes it invertible even when XᵀX is singular ⭐
LOGISTIC REGRESSION        : σ(z) = 1/(1+e^−z); ∂L/∂w = Xᵀ(σ(Xw) − y) ⭐ note it is the SAME
                             gradient form as linear regression — worth pointing out
                             No closed form → convex, solved by IRLS/gradient methods
NAIVE BAYES                : argmax_c P(c)∏P(xᵢ|c); Laplace smoothing (α) for unseen features
PCA                        : eigenvectors of the covariance XᵀX/n, or SVD of centred X.
                             Explained variance ratio = λᵢ/Σλ ⭐
k-MEANS                    : alternating minimisation of Σ‖x − μ_c‖²; guaranteed to converge to a
                             LOCAL optimum; k-means++ for initialisation
SVM                        : min ½‖w‖² s.t. yᵢ(wᵀxᵢ+b) ≥ 1; margin = 2/‖w‖ ⭐
                             Dual → kernel trick: replace ⟨x,x'⟩ with K(x,x')
ENTROPY / GINI             : H = −Σp log p ; Gini = 1 − Σp². Information gain = H(parent) −
                             Σ(nᵢ/n)H(childᵢ) ⭐
BACKPROP                   : chain rule over the computational graph; cost ≈ 2× forward pass
SOFTMAX + CE GRADIENT      : ∂L/∂z = p − y ⭐ strikingly clean — know this one
```

---

## Recall questions
1. Which algorithms require feature scaling and which are invariant to it?
2. Naive Bayes' assumption — state it, and say why the model works anyway.
3. Bagging vs boosting: which reduces bias, which reduces variance, and why?
4. Why does L1 produce exact zeros but L2 does not?
5. Why divide by √d_k in scaled dot-product attention?
6. Write the conv output-size formula and the parameter count of a conv layer.
7. Why can you not feed t-SNE output into a classifier?
8. k-means fails on which cluster shapes, and what do you use instead?
9. Give the softmax + cross-entropy gradient.
