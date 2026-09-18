
# Conceptual Q&A Bank ⭐⭐⭐

The questions that come up again and again, with the answer you should actually give — short,
correct, and with the one extra sentence that signals depth.

---

## Fundamentals

**Q. Explain the bias–variance trade-off.**
Expected squared error decomposes into irreducible noise, bias² (error from a model class too rigid
to represent the truth) and variance (sensitivity to the particular training sample). Increasing
capacity reduces bias and raises variance. *Extra:* the decomposition is exact for squared loss;
for 0–1 loss there is no clean additive form, and in the over-parameterised regime the classical
U-curve gives way to double descent.

**Q. How do you know whether you are underfitting or overfitting?**
Compare training and validation error. Both high and close → underfitting; low training error with
a large persistent gap → overfitting. *Extra:* the learning curve also tells you whether more data
will help — it will for variance, not for bias.

**Q. What is regularisation and why does it work?**
A penalty on model complexity that trades a little bias for a larger reduction in variance;
equivalently a prior saying large weights are implausible. *Extra:* L2 is a Gaussian prior, L1 a
Laplace prior, and early stopping bounds how far the weights travel from initialisation.

**Q. L1 vs L2?**
L1 produces exact zeros (feature selection) because its subgradient has constant magnitude and the
L1 ball has corners on the axes; L2 shrinks smoothly and handles correlated features as a group.
*Extra:* always standardise first and never penalise the intercept.

**Q. Generative vs discriminative models?**
Generative models `P(x, y)` (Naive Bayes, GMM, LDA, diffusion); discriminative models `P(y|x)`
(logistic regression, SVM, most neural nets). *Extra:* generative models need less data and can
generate, but make stronger assumptions; discriminative models usually win asymptotically on
classification accuracy.

**Q. Parametric vs non-parametric?**
Parametric models have a fixed number of parameters independent of `n` (linear regression);
non-parametric models grow with the data (k-NN, kernel SVM, decision trees, Gaussian processes).
*Extra:* non-parametric models have higher capacity but worse scaling and often need more data.

**Q. What is the curse of dimensionality?**
As dimension grows, data becomes sparse, all pairwise distances concentrate towards the same value,
and the volume needed to cover the space grows exponentially. *Extra:* it is why k-NN and
distance-based clustering degrade in high dimensions, and why dimensionality reduction or learned
embeddings help.

---

## Model-specific

**Q. Why is logistic regression called regression?**
It performs a linear regression on the **log-odds**; the sigmoid maps that to a probability. It is
used as a classifier via a threshold.

**Q. Why cross-entropy rather than MSE for classification?**
Cross-entropy is the negative log-likelihood of a Bernoulli/categorical model, it is convex for
linear models, and its gradient `p − y` does not vanish when a unit is saturated — MSE's gradient
carries a `σ'(z)` factor that goes to zero exactly when the model is confidently wrong.

**Q. Bagging vs boosting?**
Bagging trains models in parallel on bootstrap samples and averages them, attacking variance;
boosting trains sequentially, each model fitting the residual of the ensemble so far, attacking
bias. *Extra:* adding trees never hurts a random forest but does overfit a boosted model, so
boosting needs early stopping.

**Q. Why do random forests subsample features at each split?**
To decorrelate the trees. The variance of an average is `ρσ² + (1−ρ)σ²/B`, which floors at `ρσ²`,
so reducing `ρ` is the only way to keep improving.

**Q. What are support vectors?**
The training points lying on or inside the margin. KKT complementary slackness forces the dual
variable to zero for all other points, and `w = Σαᵢyᵢxᵢ`, so the solution depends on them alone.

**Q. Explain the kernel trick.**
The SVM dual depends on the data only through inner products, so replacing `xᵀz` with `K(x,z) =
φ(x)ᵀφ(z)` gives a non-linear boundary without ever computing `φ` — which may be
infinite-dimensional, as for the RBF kernel.

**Q. How do you choose `k` in k-means?**
Silhouette score, the gap statistic, BIC for a Gaussian mixture, or — best — tune it on the
downstream task. The elbow plot is a heuristic and is often ambiguous.

**Q. PCA: what does it optimise?**
The directions of maximum variance, which are the top eigenvectors of the covariance matrix;
equivalently it minimises reconstruction error. *Extra:* it is unsupervised, so it can discard the
discriminative direction — use LDA if class separation is the goal.

---

## Deep learning

**Q. Why do we need activation functions?**
Without them a stack of linear layers collapses to a single linear map, so depth adds nothing.

**Q. Why did ReLU replace sigmoid?**
Its derivative is exactly 1 on the positive side, so gradients survive depth; sigmoid's derivative
is at most 0.25, which decays exponentially with layers. It is also cheaper and induces sparsity.

**Q. What causes vanishing gradients, and what fixes them?**
Backprop multiplies `L − l` Jacobians, so the magnitude behaves like `γ^{L−l}`. Fixes: ReLU-family
activations, He/Xavier initialisation, normalisation layers, residual connections and gradient
clipping (for explosion).

**Q. Why do residual connections help?**
`∂(x + F(x))/∂x = I + ∂F/∂x`, so there is always a gradient path multiplied by 1; a block can also
represent the identity by driving `F → 0`, so extra depth cannot hurt.

**Q. BatchNorm vs LayerNorm?**
BN normalises each channel across the batch (batch-size dependent, different at inference);
LN normalises each sample across its features (batch-independent, identical at inference), which is
why transformers use it. *Extra:* the "internal covariate shift" explanation of BN has been largely
discredited; the benefit is a smoother loss landscape permitting higher learning rates.

**Q. Dropout at inference?**
Off. With inverted dropout, activations are scaled by `1/(1−p)` during training and nothing changes
at test time.

**Q. Why does Adam use bias correction?**
`m` and `v` start at zero, so `E[m_t] = (1−β₁ᵗ)E[g]` — biased towards zero, most severely for `v`
with `β₂ = 0.999`. Dividing by `(1−βᵗ)` makes the early estimates unbiased.

---

## NLP and LLMs

**Q. Explain attention.**
Each position emits a query; every position offers a key and a value; the softmax of scaled
query–key dot products gives weights, and the output is the weighted average of the values — a
soft, differentiable dictionary lookup.

**Q. Why divide by `√d_k`?**
With unit-variance components, `Var(q·k) = d_k`, so the logits scale as `√d_k`; without rescaling
the softmax saturates and its gradient vanishes.

**Q. Why do transformers need positional encodings?**
Attention is permutation-equivariant, so without them word order carries no information.

**Q. BERT vs GPT?**
BERT is an encoder with bidirectional attention trained on masked-token prediction — good for
understanding tasks; GPT is a decoder with causal attention trained on next-token prediction —
natively generative and adaptable by prompting. *Extra:* BERT-size encoders are still the right
choice for high-volume classification and retrieval.

**Q. RAG or finetuning?**
RAG for knowledge (facts, documents, anything that changes); finetuning for behaviour (format,
style, domain tone, task structure). They compose.

**Q. Why does an LLM hallucinate?**
It is trained to produce likely continuations, not true ones; it has no internal separation between
knowledge and plausibility. Mitigate with grounding and citations, an explicit "I don't know"
option, low temperature, tools for anything computable, and verification.

**Q. What is LoRA and why does it work?**
`W₀ + BA` with `r ≪ d`, training only `A` and `B` — the finetuning update is approximately
low-rank. It cuts trainable parameters by two to three orders of magnitude, adds no inference
latency once merged, and lets many adapters share a base model.

---

## Practice and production

**Q. Your model has 99% accuracy. Are you happy?**
Not until I know the class balance — with 1% positives, always predicting the majority achieves
99%. I would look at precision, recall and PR-AUC, compare with the trivial baseline, and choose the
threshold by expected cost.

**Q. Offline metrics improved, online did not. Why?**
Training–serving skew, an offline metric that does not track the business objective, distribution
shift, the prediction not changing any action, or an underpowered experiment. Leakage is the first
thing I would check if the gap is large and immediate.

**Q. How do you detect data leakage?**
Ask of every feature whether it would exist, with that value, before the label; check for a single
dominant feature and implausibly high performance; verify the split is time-based and grouped; and
confirm every learned transform is fitted inside the CV fold.

**Q. How would you handle a 1:1000 imbalance?**
Change the metric first (PR-AUC, recall at a precision floor), then the decision threshold by
expected cost, then class weights or focal loss, and only then resampling — applied inside the CV
fold. Below ~0.1% positives I would also consider framing it as anomaly detection.

**Q. When would you not use machine learning?**
When a rule is sufficient and auditable, when there are no labels and no way to get them, when the
cost of an error is catastrophic and unexplainable, when the data is too small or too biased, or
when the problem is really a product or process problem. Saying this is a strength, not a weakness.

---

## Recall drill

Cover the answers and give each question a 60-second spoken response. If you cannot answer in one
clear paragraph plus one "extra" sentence, revisit the topic file.
