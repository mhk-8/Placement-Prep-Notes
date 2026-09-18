
# The Derivations You Must Be Able to Reproduce ⭐⭐⭐

Twenty derivations, ranked by how often they are asked. Work through them with pen and paper until
each takes under five minutes. Each entry states the claim, the key steps and where the full
treatment lives.

---

## Tier 1 — asked in almost every ML interview

**1. Bias–variance decomposition**
`E[(y − ĥ(x))²] = σ² + (f − E[ĥ])² + E[(ĥ − E[ĥ])²]`.
Insert `f(x)` and `h̄(x) = E_D[ĥ]`, expand the square, and show all three cross-terms vanish because
`E[ε] = 0`, `ε ⫫ D`, and `E_D[ĥ − h̄] = 0`.
→ `03_Classical_ML/01-learning-theory-bias-variance.md`

**2. Logistic regression gradient**
`σ' = σ(1−σ)`; `∂L/∂p = (p−y)/(p(1−p))`; multiply to get `∂L/∂z = p − y`, hence
`∇_w = Xᵀ(p − y)`.
→ `01_Math_Foundations/02-matrix-calculus.md`

**3. Softmax + cross-entropy gradient**
Softmax Jacobian `∂p_i/∂z_j = p_i(δ_ij − p_j)`; combined with `L = −Σ y_i log p_i` it collapses to
`∂L/∂z_j = p_j − y_j`.
→ `01_Math_Foundations/02-matrix-calculus.md`

**4. Normal equation**
`∇‖Xw − y‖² = 2Xᵀ(Xw − y) = 0 ⇒ XᵀXw = Xᵀy`. Geometric reading: the residual is orthogonal to
`col(X)`. Ridge: `(XᵀX + λI)⁻¹Xᵀy`, always invertible.
→ `01_Math_Foundations/02-matrix-calculus.md`

**5. Backpropagation (BP1–BP4)**
`δ^L = ∇_aL ⊙ g'(z^L)`; `δ^l = (W^{l+1ᵀ}δ^{l+1}) ⊙ g'(z^l)`; `∂L/∂W^l = δ^l a^{l−1ᵀ}`;
`∂L/∂b^l = δ^l`.
→ `04_Deep_Learning/01-neural-networks-and-backprop.md`

**6. PCA as variance maximisation**
`max vᵀΣv` s.t. `vᵀv = 1`; the Lagrangian gives `Σv = λv`, and substituting back shows the objective
equals `λ`, so the maximiser is the top eigenvector.
→ `01_Math_Foundations/04-pca-derivation.md`

**7. The `√d_k` scaling in attention**
`Var(q·k) = Σ Var(qᵢkᵢ) = d_k` for unit-variance independent components, so the logits have scale
`√d_k`; dividing restores unit variance and keeps the softmax out of saturation.
→ `05_NLP_and_LLMs/02-attention-and-transformers.md`

---

## Tier 2 — asked often

**8. MSE is MLE under Gaussian noise**
`y = f(x) + ε`, `ε ~ N(0,σ²)` ⇒ `ℓ(w) = const − Σ(y − f)²/(2σ²)`, so maximising the likelihood is
minimising squared error.
→ `02_Probability_and_Statistics/03-estimation-mle-map.md`

**9. Ridge = MAP with a Gaussian prior**
Adding `log p(w) = −‖w‖²/(2τ²)` to the Gaussian log-likelihood gives
`argmin Σ(y − wᵀx)² + λ‖w‖²` with `λ = σ²/τ²`. Laplace prior gives L1 similarly.
→ `02_Probability_and_Statistics/03-estimation-mle-map.md`

**10. Bagging variance formula**
`Var((1/B)Σf_b) = ρσ² + (1−ρ)σ²/B`, from `Var(Σf) = Bσ² + B(B−1)ρσ²`. The floor `ρσ²` motivates
feature subsampling in random forests.
→ `03_Classical_ML/04-trees-and-ensembles.md`

**11. SVM margin and the dual**
Distance to a hyperplane is `(wᵀx+b)/‖w‖`; fixing the scale makes the margin `2/‖w‖`. The
Lagrangian's stationarity gives `w = Σαᵢyᵢxᵢ` and `Σαᵢyᵢ = 0`; substituting yields the dual, which
depends only on inner products.
→ `03_Classical_ML/05-svm-and-kernels.md`

**12. Complementary slackness ⇒ support vectors**
`αᵢ[yᵢ(wᵀxᵢ+b) − 1 + ξᵢ] = 0`, so `αᵢ > 0` only when the constraint is active.
→ `03_Classical_ML/05-svm-and-kernels.md`

**13. He initialisation**
`Var(z) = n_in Var(w) Var(x)`; ReLU halves the variance, so `Var(w) = 2/n_in` preserves it. Xavier
uses `2/(n_in + n_out)` for symmetric activations.
→ `04_Deep_Learning/01-neural-networks-and-backprop.md`

**14. Vanishing gradients as a matrix product**
`δ^l = [Π_{k>l} W^{kᵀ}D^{k−1}] δ^L` — exponential decay or growth in depth.
→ `04_Deep_Learning/02-activations-and-gradient-problems.md`

**15. LSTM cell-state gradient**
`∂c_t/∂c_{t−1} = f_t`, so `∂c_T/∂c_t = Π f_k` stays near 1 when the forget gates are open.
→ `04_Deep_Learning/05-rnns-lstms-and-sequence-models.md`

**16. Adam bias correction**
`m_t = (1−β₁)Σβ₁^{t−i}g_i` ⇒ `E[m_t] = (1−β₁ᵗ)E[g]`, hence `m̂ = m/(1−β₁ᵗ)`.
→ `01_Math_Foundations/03-optimization.md`

---

## Tier 3 — good differentiators

**17. Spectral theorem**
Real eigenvalues from `v*Av = λv*v` with both sides real; orthogonality from
`(λ₁ − λ₂)(v₁ᵀv₂) = 0`.
→ `01_Math_Foundations/01-linear-algebra.md`

**18. Chebyshev from Markov**
Apply `P(X ≥ a) ≤ E[X]/a` to `(X−μ)²` at level `k²σ²`.
→ `02_Probability_and_Statistics/02-expectation-variance-inequalities.md`

**19. Bootstrap 63.2%**
`P(row excluded) = (1 − 1/n)ⁿ → e⁻¹`, so ~63.2% of rows appear and ~36.8% are out-of-bag.
→ `02_Probability_and_Statistics/02-expectation-variance-inequalities.md`

**20. Cross-entropy minimisation = KL minimisation**
`H(p,q) = H(p) + D(p‖q)` and `H(p)` is independent of the model.
→ `02_Probability_and_Statistics/01-probability-foundations.md`

**Bonus. Gradient boosting as functional gradient descent**
Fit each new learner to `−∂L/∂F` at the current ensemble; for squared loss that is the residual, for
log-loss it is `y − p`.
→ `03_Classical_ML/04-trees-and-ensembles.md`

---

## How to practise ⭐

```
Week 1: Tier 1, one per day, written from memory on paper, timed.
Week 2: Tier 2, same protocol; re-do two Tier-1 derivations each day.
Week 3: Tier 3; then random-order drills — draw a number 1–21 and derive it cold.
Target: any Tier-1 derivation in under 4 minutes, spoken aloud while writing.
```

⚠️ Write them out. Recognising a derivation on a page is not the same as producing it on a
whiteboard with someone watching.
