
# ML / DL Interview Round — Q&A

> **Scope.** For ML, Data Science, Applied Scientist and ML-Engineer roles. Full theory and
> derivations are in `../../05_AI_ML/`; this file is the **spoken-answer layer** plus the questions
> your specific resume invites.
>
> ⚠️ **The calibration point for you:** your ML resume leads with a *GPU compiler* M.Tech project.
> That is unusual for an ML applicant and it cuts both ways — see §7.

---

## 1. The shape of an ML interview ⭐⭐

```
1. RESUME / PROJECT DEEP DIVE     ~40%   ← your strongest ground; see ../02_Project_Deep_Dives/
2. ML FUNDAMENTALS (rapid-fire)   ~25%   ← this file, §2-§4
3. CODING                         ~20%   ← DSA, or implement a layer/metric from scratch
4. ML SYSTEM DESIGN               ~15%   ← ../../05_AI_ML/08_ML_System_Design/
```

For MLE roles the coding weight rises and system design becomes the differentiator. For Applied
Scientist / Research roles, the maths and the project depth dominate.

---

## 2. The fundamentals you must answer in 45 seconds ⭐⭐⭐

<details><summary>"Explain the bias-variance trade-off."</summary>

Expected squared error decomposes into irreducible noise, bias² (the model class is too rigid to
represent the truth) and variance (sensitivity to the particular training sample). Increasing
capacity lowers bias and raises variance.

**The extra sentence that signals depth:** the decomposition is exact for squared loss; for 0-1
loss there is no clean additive form, and in the over-parameterised regime the classical U-curve
gives way to **double descent**. ⭐
</details>

<details><summary>"How do you tell underfitting from overfitting?"</summary>

Compare training and validation error. Both high and close → underfitting; low training error with a
large persistent gap → overfitting. The learning curve also tells you whether **more data will
help** — it will for variance, not for bias.
</details>

<details><summary>"L1 vs L2 regularisation?"</summary>

L1 produces exact zeros (feature selection) because its subgradient has constant magnitude `λ·sign(w)`
and keeps pushing until the coefficient hits zero; L2's gradient `2λw` weakens as `w → 0` and never
crosses. Geometrically, the L1 ball has corners on the axes.

**The extra sentence:** both are MAP estimates — L2 under a Gaussian prior with `λ = σ²/τ²`, L1
under a Laplace prior. ⭐
</details>

<details><summary>"Why cross-entropy rather than MSE for classification?"</summary>

Cross-entropy is the negative log-likelihood of a Bernoulli/categorical model, it is convex for
linear models, and its gradient collapses to `p − y` — no vanishing factor. With MSE and a sigmoid,
the gradient carries a `σ'(z)` term that goes to zero exactly when the unit is saturated, i.e. when
the model is confidently wrong.
</details>

<details><summary>"Bagging vs boosting?"</summary>

Bagging trains models in parallel on bootstrap resamples and averages them — it attacks **variance**.
Boosting trains sequentially, each model fitting the residual of the ensemble so far — it attacks
**bias**.

**The extra sentence:** adding trees never hurts a random forest but does overfit a boosted model,
so boosting needs early stopping. And the reason random forests subsample features is that
`Var(average) = ρσ² + (1−ρ)σ²/B` floors at `ρσ²` — decorrelating the trees is the only way to keep
improving. ⭐
</details>

<details><summary>"Your model has 99% accuracy. Are you happy?"</summary>

Not until I know the class balance — with 1% positives, always predicting the majority achieves 99%.
I'd look at precision, recall and PR-AUC, compare against the trivial baseline, and choose the
threshold by expected cost: minimise `C_FP·FP + C_FN·FN`.
</details>

<details><summary>"When does ROC-AUC mislead?"</summary>

Under heavy class imbalance. FPR's denominator is the large negative class, so thousands of false
positives barely move it — ROC-AUC can read 0.95 while precision at any usable threshold is 2%.
Use **PR-AUC / average precision**, whose baseline is the positive rate.
</details>

<details><summary>"Explain precision and recall, and when each dominates."</summary>

Precision = of what I flagged, how much was right. Recall = of the real positives, how many I
caught. Cancer screening optimises recall (a miss is fatal, a false alarm gets a second test); a
spam filter optimises precision (a real email in the spam folder is worse than a spam in the inbox).
F1 is their harmonic mean, which is dominated by the smaller of the two and ignores true negatives.
</details>

<details><summary>"What is data leakage and how do you detect it?"</summary>

Any information in training that will not exist at prediction time. Detection: ask of every feature
whether it could have that value *before* the label; look for one dominant feature and implausibly
high validation performance; check the split is time-based and grouped where it needs to be; and
confirm every learned transform (scaler, imputer, encoder, PCA, SMOTE) is fitted **inside** the CV
fold.

⭐ The production symptom: offline is far better than online, and the gap appears **immediately**
rather than decaying over weeks — which distinguishes leakage from drift.
</details>

<details><summary>"How would you handle a 1:1000 class imbalance?"</summary>

In this order: change the **metric** (PR-AUC, recall at a precision floor), then the **threshold**
by expected cost, then the **loss** (class weights or focal loss), and only then **resampling** —
inside the CV fold, with recalibration afterwards because resampling changes the base rate. Below
about 0.1% positives I would also consider framing it as anomaly detection.
</details>

---

## 3. Deep learning ⭐⭐⭐

<details><summary>"Why do we need activation functions?"</summary>

Without them, a stack of linear layers collapses: `W₂(W₁x + b₁) + b₂ = W'x + b'`. All the
representational power comes from the non-linearity.
</details>

<details><summary>"Why did ReLU replace sigmoid?"</summary>

Its derivative is exactly 1 on the positive side, so the gradient survives depth. Sigmoid's
derivative is at most 0.25, so ten layers multiply by at most `0.25¹⁰ ≈ 10⁻⁶`. ReLU is also cheaper
(no exponential) and induces sparsity. The cost is dying ReLU — a unit driven permanently negative
has zero gradient forever.
</details>

<details><summary>"Explain vanishing gradients and how residual connections fix them."</summary>

Backprop multiplies `L − l` Jacobians, so the gradient magnitude behaves like `γ^(L−l)` —
exponential decay or growth in depth. Residual connections give `∂(x + F(x))/∂x = I + ∂F/∂x`, so
there is always a path multiplied by **1**. A block can also learn the identity by driving `F → 0`,
so adding depth cannot hurt in principle. ⭐ One line, and it is the whole answer.
</details>

<details><summary>"BatchNorm vs LayerNorm?"</summary>

BN normalises each channel across the **batch**, so it is batch-size dependent and behaves
differently at inference (running statistics). LN normalises each sample across its **features**, so
it is batch-independent and identical at train and test — which is why transformers use it.

**The extra sentence:** the original "internal covariate shift" explanation of BN has been largely
discredited; the accepted explanation is that it smooths the loss landscape, permitting higher
learning rates. ⭐
</details>

<details><summary>"Why does Adam need bias correction?"</summary>

`m` and `v` are initialised at zero, so `E[m_t] = (1 − β₁ᵗ)E[g]` — biased toward zero early, most
severely for `v` with `β₂ = 0.999`. Dividing by `(1 − βᵗ)` makes the estimates unbiased, preventing
tiny or unstable first steps.
</details>

<details><summary>"Why do transformers need learning-rate warmup?"</summary>

Adam's second-moment estimate is unreliable in the first few hundred steps and early attention
gradients are large, so a full learning rate immediately destabilises LayerNorm statistics and the
model diverges. Warmup ramps the LR while the moment estimates settle. Pre-LN architectures need far
less warmup than the original post-LN. ⭐ Connect it to your Transformer project's Noam scheduler.
</details>

<details><summary>"Explain attention and the √d_k."</summary>

`Attention(Q,K,V) = softmax(QKᵀ/√d_k)V`. Queries ask, keys advertise, values contribute — a soft,
differentiable dictionary lookup. With unit-variance components, `Var(q·k) = d_k`, so the logits
scale as `√d_k`; without rescaling the softmax saturates and its Jacobian `diag(p) − ppᵀ` vanishes.
</details>

<details><summary>"Dropout at inference?"</summary>

Off. With inverted dropout, activations are scaled by `1/(1−p)` during training and nothing changes
at test time.
</details>

<details><summary>"Your training loss won't decrease. Walk me through your debugging."</summary>

⭐ This is a favourite and the answer is a *protocol*, not a list:
```
1. Try to OVERFIT 10 EXAMPLES to near-zero loss. If that fails it is a BUG, not a
   modelling problem — the data, the loss, or the forward pass.
2. Check the loss at initialisation: ln K for K balanced classes (2.303 for 10). A very
   different value means a wiring bug.
3. Check shapes and label alignment; visualise a few inputs with their labels.
4. LR range test.
5. Per-layer gradient norms; fraction of dead ReLUs.
6. Turn OFF all regularisation, get it to overfit, then add it back.
7. Only then scale up.
```
</details>

---

## 4. NLP and LLMs ⭐⭐ — increasingly asked regardless of role

<details><summary>"RAG or fine-tuning?"</summary>

**RAG for knowledge, fine-tuning for behaviour.** If the model lacks facts — your documents, recent
events, anything that changes — retrieval is the right tool, and fine-tuning is a poor way to inject
facts. If it lacks format, style, domain tone or task structure, fine-tune. They compose.
</details>

<details><summary>"Why does an LLM hallucinate, and how do you reduce it?"</summary>

It is trained to produce *likely* continuations, not *true* ones, and has no internal separation
between knowledge and plausibility. Mitigations in order: ground with RAG and require citations;
explicitly permit "I don't know"; lower the temperature for factual tasks; structured output with
schema validation; self-consistency over `k` samples; a verification pass; and tools for anything
computable.
</details>

<details><summary>"Explain LoRA."</summary>

`W' = W₀ + BA` with `A ∈ R^{r×d}`, `B ∈ R^{d×r}`, `r ≪ d`, training only `A` and `B` while `W₀` is
frozen. The hypothesis is that the finetuning *update* is approximately low-rank. `B` is initialised
to zero so training starts from exactly the pretrained function, and `α/r` scaling decouples the
learning rate from `r`. After training `W₀ + BA` can be merged, so there is **no inference
latency** — which is why many adapters can share one base model in production. ⭐
</details>

<details><summary>"Walk me through a RAG pipeline."</summary>

Offline: chunk (structure-aware, with overlap), embed, index into a vector store plus a BM25 index.
Online: rewrite the query, hybrid retrieve (dense + BM25 fused with reciprocal rank fusion),
**rerank with a cross-encoder**, assemble the prompt with numbered context and citations, generate,
then verify groundedness.

⭐ Two things to volunteer: the reranker is usually the biggest quality win per rupee, and
permissions must be enforced **at retrieval time** as a metadata filter, never by asking the model
to withhold. Connect it to your IR project — you built the retrieval half.
</details>

---

## 5. Coding in an ML round ⭐⭐

Expect one of three kinds:
```
1. STANDARD DSA         — same as an SDE round
2. IMPLEMENT FROM SCRATCH in NumPy:  ⭐ your Feedforward-NN project prepares you for this
     - forward + backward for a linear layer
     - softmax with log-sum-exp stability
     - k-means, k-NN, linear/logistic regression by gradient descent
     - a metric: precision/recall/F1, AUC, IoU, NDCG
3. PANDAS / SQL data manipulation — groupby, joins, window functions, time-based aggregation
```

**Two snippets worth having automatic:**
```python
def softmax(z):                      # numerically stable — the point of the question
    z = z - z.max(axis=1, keepdims=True)
    e = np.exp(z)
    return e / e.sum(axis=1, keepdims=True)

def iou(boxA, boxB):                 # (x1,y1,x2,y2)
    x1 = max(boxA[0], boxB[0]); y1 = max(boxA[1], boxB[1])
    x2 = min(boxA[2], boxB[2]); y2 = min(boxA[3], boxB[3])
    inter = max(0, x2-x1) * max(0, y2-y1)
    a = (boxA[2]-boxA[0])*(boxA[3]-boxA[1])
    b = (boxB[2]-boxB[0])*(boxB[3]-boxB[1])
    return inter / (a + b - inter)
```
⚠️ For the softmax, the `z.max()` subtraction **is** the question. Writing the naive version fails
it.

---

## 6. ML system design ⭐⭐

The eight-step framework and five worked case studies are in
`../../05_AI_ML/08_ML_System_Design/`. The short version:

```
1. CLARIFY   scale, latency, freshness, the cost of each error type
2. METRICS   business → ML proxy → guardrails
3. DATA      sources, labels and their delay, splits, leakage audit
4. FEATURES  the seven families; availability at prediction time
5. MODEL     baseline first, then justify each step up
6. EVALUATE  offline per slice, then a powered A/B test
7. SERVE     latency budget, batch vs online, fallback
8. MONITOR   drift, feedback loops, retraining trigger
```
⭐ The two-stage **retrieval → ranking** pattern answers half of all such questions, and you have
genuine retrieval experience to draw on.

---

## 7. The calibration question for your profile ⚠️⭐⭐⭐

**"Your M.Tech project is a GPU compiler analysis. Why are you applying for an ML role?"**

You will get some version of this. Prepare it properly — the honest answer is strong:

> "My coursework and most of my projects are ML — the Transformer from scratch, the multi-task
> vision pipeline, the IR engine, the network built without autograd. The M.Tech project is
> systems, and I chose it deliberately, because the thing I find most interesting is where the two
> meet: ML is now fundamentally a systems problem. Training is bounded by memory bandwidth and
> communication, inference is bounded by the KV cache, and the difference between a model that
> ships and one that doesn't is usually kernel-level.
>
> Having written CUDA kernels and profiled them with Nsight means when I read about FlashAttention
> or quantisation or a fused kernel, I understand *why* it works rather than just that it does. For
> an ML infrastructure or applied role, I think that's an advantage rather than a detour."

⭐ **This answer converts the apparent mismatch into a specialisation.** ML *infrastructure* roles —
NVIDIA, the ML-systems teams at large companies, inference-optimisation teams — are exactly where
this combination is most valuable, and they are less crowded than generic ML roles. Target them.

---

## 8. The pre-interview 30-minute ML drill

```
 5 min : bias-variance, over/underfitting diagnosis, L1 vs L2
 5 min : metrics — precision/recall, PR vs ROC under imbalance, threshold by expected cost
 5 min : leakage — the taxonomy and the production symptom
 5 min : DL — vanishing gradients, residuals, BN vs LN, Adam bias correction
 5 min : Transformers — attention, √d_k, masks, warmup
 5 min : your projects' 60-second versions, said ALOUD
```

---

## Recall questions

1. Give the "extra sentence" for bias-variance, L1/L2 and bagging-vs-boosting.
2. When does ROC-AUC mislead, and what replaces it?
3. How does leakage look in production, and how does it differ from drift?
4. Give the seven-step protocol for a model that will not train.
5. State the RAG-vs-finetuning rule in five words.
6. Why is `B` initialised to zero in LoRA?
7. What is the one line that makes a from-scratch softmax correct?
8. Give your answer to "why ML if your thesis is a compiler?"
