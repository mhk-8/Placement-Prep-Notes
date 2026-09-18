
# Math Foundations — Flashcards

Questions first, answers below. Cover the answers, run the questions, then check.
Aim for a 15-minute pass.

---

## Questions

**Linear algebra**
1. State the rank–nullity theorem.
2. What does `rank(A) = rank(AᵀA)` let you conclude about the normal equation?
3. Spectral theorem — statement?
4. Why are the eigenvalues of a real symmetric matrix real?
5. Define positive semi-definite in two equivalent ways.
6. What is the SVD, and how is it related to `eig(AᵀA)`?
7. State Eckart–Young.
8. Three ML places where low-rank approximation appears.
9. Geometric meaning of `‖A‖₂` for a matrix.
10. Relation between Euclidean distance and cosine similarity on unit vectors.
11. Why is `inv(A) @ b` bad practice?
12. What does the condition number tell you about a linear system?
13. Why does ridge regression always have a unique solution?
14. Why does the L1 ball produce sparsity but the L2 ball does not?

**Matrix calculus**
15. `∇(aᵀx)`, `∇(xᵀAx)`, `∇‖x‖²` — all three.
16. Shape of the Jacobian of `f: R^n → R^m`.
17. Vector chain rule through `y = f(x)`, `z = g(y)`.
18. Three gradients of a linear layer `y = Wx + b`.
19. Derive `σ'(z)`.
20. `∂L/∂z` for sigmoid + BCE, and for softmax + CE.
21. Why cross-entropy rather than MSE for classification?
22. Why should a loss take logits rather than probabilities?
23. Normal equation, plus the geometric interpretation of the residual.
24. L2 penalty as weight decay — show the update.
25. Why does AdamW exist?

**Optimisation**
26. Three equivalent characterisations of convexity.
27. Prove local min ⇒ global min for a convex function.
28. Which standard ML losses are convex?
29. Why is `−∇f` the steepest-descent direction?
30. Convergence rate of GD on smooth convex vs strongly convex objectives.
31. Three reasons mini-batch beats both full-batch and single-sample.
32. Linear scaling rule.
33. Momentum: update equations and effective step amplification.
34. What does Nesterov change?
35. AdaGrad's flaw and RMSProp's fix.
36. Adam's full update including bias correction.
37. Why bias correction is needed at all.
38. When does plain SGD+momentum beat Adam?
39. Why do transformers need warmup?
40. Cosine schedule — what and why.
41. KKT conditions, all four.
42. What complementary slackness implies for SVMs.
43. Why is Newton's method impractical for deep nets?
44. Why are saddles, not local minima, the high-dimensional problem?
45. Training loss won't decrease at all — diagnostic checklist.

**PCA**
46. Set up PC1 as a constrained optimisation and state the stationarity condition.
47. Why does the objective value equal the eigenvalue?
48. Equivalence of variance maximisation and reconstruction-error minimisation.
49. PCA from SVD: where do components, scores and variances come from?
50. Why SVD instead of `eig(XᵀX)`?
51. Covariance PCA vs correlation PCA.
52. Two ways to choose `k`.
53. PCA vs LDA.
54. PCA vs a linear autoencoder.
55. Where must PCA sit in a train/test pipeline?
56. Cheap route when `d ≫ n`.

---

## Answers

1. For `A ∈ R^{m×n}`: `rank(A) + dim(null(A)) = n` (number of **columns**).

2. `AᵀA` is invertible iff `A` has full column rank; otherwise the normal equation has infinitely
   many solutions and you need a pseudoinverse or regularisation.

3. Every real symmetric `A` can be written `A = QΛQᵀ` with `Q` orthogonal and `Λ` real diagonal —
   an orthonormal eigenbasis always exists.

4. `Av = λv` ⇒ `v*Av = λv*v`. `v*Av` is real (it equals its own conjugate transpose since `A` is
   real symmetric) and `v*v > 0` is real, so `λ = (v*Av)/(v*v)` is real.

5. `A` is PSD iff `vᵀAv ≥ 0 ∀v` ⟺ all eigenvalues `≥ 0`. (PD: strict, all `> 0`.)

6. `A = UΣVᵀ` with `U, V` orthogonal and `Σ` diagonal non-negative. `V` holds eigenvectors of
   `AᵀA`, `U` of `AAᵀ`, and `σᵢ = √λᵢ(AᵀA)`.

7. The best rank-`k` approximation of `A` in both Frobenius and spectral norm is
   `A_k = Σ_{i≤k} σᵢuᵢvᵢᵀ`, with error `√(Σ_{i>k} σᵢ²)` (Frobenius) or `σ_{k+1}` (spectral).

8. PCA / LSA (truncated SVD of a term-document matrix) / LoRA (low-rank weight updates); also
   matrix-factorisation recommenders and model compression.

9. The maximum stretch factor: `‖A‖₂ = max_{‖x‖=1} ‖Ax‖ = σ_max`.

10. `‖u − v‖² = 2(1 − cos θ)` — monotone, so nearest-neighbour rankings agree.

11. Explicit inversion is `O(n³)` with worse numerical error than a solve; use `np.linalg.solve`,
    or a Cholesky/QR factorisation. Inversion also destroys sparsity.

12. `κ(A) = σ_max/σ_min` bounds how much relative input error is amplified into relative solution
    error. Large `κ` means the solution is untrustworthy and iterative methods converge slowly.

13. `XᵀX + λI` has eigenvalues `λᵢ(XᵀX) + λ > 0`, so it is PD and hence invertible for any `λ > 0`.

14. The L1 ball has corners on the axes; the first contour of the loss to touch the ball
    generically hits a corner, where some coordinates are exactly zero. The L2 sphere is smooth,
    so contact happens at a generic point with all coordinates non-zero but shrunk.

15. `∇(aᵀx) = a`; `∇(xᵀAx) = (A + Aᵀ)x` (`= 2Ax` if symmetric); `∇‖x‖² = 2x`.

16. `m × n`: `J_ij = ∂fᵢ/∂xⱼ`.

17. `∇_x z = Jᵀ ∇_y z` — backprop is exactly repeated application of this.

18. `∂L/∂W = δxᵀ`, `∂L/∂x = Wᵀδ`, `∂L/∂b = δ`, with `δ = ∂L/∂y`.

19. `σ(z) = 1/(1+e^{−z})`; `σ' = e^{−z}/(1+e^{−z})² = σ(z)(1 − σ(z))`.

20. Both give `p − y` (per-class `p_j − y_j` for softmax).

21. MSE with a sigmoid gives `∂L/∂z = (p − y)σ'(z)`; when the unit is saturated `σ' ≈ 0` and the
    gradient vanishes even though the prediction is badly wrong. Cross-entropy cancels that
    factor. Cross-entropy is also the MLE loss for a Bernoulli/categorical likelihood, and it is
    convex in the parameters for linear models whereas MSE+sigmoid is not.

22. Numerical stability: computing `log(softmax)` fused with log-sum-exp avoids overflow in
    `exp` and `log(0)` when a probability underflows to zero.

23. `XᵀXw = Xᵀy`. Geometrically, the residual `y − Xw` is orthogonal to the column space of `X`,
    i.e. `Xw` is the orthogonal projection of `y` onto `col(X)`.

24. `w ← (1 − 2ηλ)w − η∇L` — a multiplicative shrink each step.

25. Under Adam the L2 gradient is rescaled by `1/√v̂`, so parameters with large gradient history
    get *less* decay — not the intended uniform shrinkage. AdamW applies `−ηλw` outside the
    adaptive rescaling.

26. (i) `f(tx + (1−t)y) ≤ tf(x) + (1−t)f(y)`; (ii) `f(y) ≥ f(x) + ∇f(x)ᵀ(y − x)` — the function
    lies above its tangents; (iii) `∇²f ⪰ 0` everywhere.

27. See Q17 of the OA set: any strictly better point `y` gives a point on the segment arbitrarily
    close to `x*` with smaller value, contradicting local minimality.

28. Linear regression (MSE), ridge, lasso, logistic regression, softmax regression, SVM hinge
    loss. Neural networks with hidden layers: no.

29. First-order expansion `f(x + d) ≈ f(x) + ∇fᵀd`; minimising `∇fᵀd` over `‖d‖ = 1` gives
    `d = −∇f/‖∇f‖` by Cauchy–Schwarz.

30. `O(1/t)` for smooth convex; linear (geometric) `O(ρᵗ)` with `ρ = (κ−1)/(κ+1)` for strongly
    convex.

31. Gradient-noise averaging (variance `∝ 1/B`) without full-batch cost; hardware efficiency —
    matrix–matrix products saturate GPUs; enough noise remains to escape sharp minima and
    saddles.

32. If you multiply batch size by `k`, multiply the learning rate by `k` (with warmup) to keep the
    per-example step size roughly constant.

33. `v ← βv + g`, `w ← w − ηv`. On a constant gradient the velocity saturates at `g/(1−β)`, a
    `1/(1−β)` amplification (10× at `β = 0.9`).

34. Nesterov evaluates the gradient at the *look-ahead* point `w − ηβv`, so the correction
    responds to where momentum is taking you — less overshoot.

35. AdaGrad accumulates `Σg²` forever, so the effective LR decays monotonically to zero and
    training stalls. RMSProp replaces the sum by an exponential moving average.

36. `m ← β₁m + (1−β₁)g`; `v ← β₂v + (1−β₂)g²`; `m̂ = m/(1−β₁ᵗ)`, `v̂ = v/(1−β₂ᵗ)`;
    `w ← w − η m̂/(√v̂ + ε)`.

37. `m₀ = v₀ = 0` biases both estimates towards zero: `E[m_t] = (1−β₁ᵗ)E[g]`. Without correction
    the first steps are far too small and the `m/√v` ratio is unstable.

38. Well-tuned SGD+momentum often generalises better on convolutional vision models; Adam
    dominates for transformers, sparse gradients and NLP, and is the safer default when you
    cannot tune.

39. Unreliable early second-moment estimates plus large initial attention gradients; warmup ramps
    the LR while `v̂` stabilises. Pre-LN transformers need much less warmup than post-LN.

40. `η_t = η_min + ½(η_max − η_min)(1 + cos(πt/T))` — a smooth decay to near zero, which anneals
    into a flatter minimum; typically preceded by linear warmup.

41. Stationarity (`∇f + Σλᵢ∇gᵢ + Σμⱼ∇hⱼ = 0`), primal feasibility, dual feasibility (`μⱼ ≥ 0`),
    complementary slackness (`μⱼhⱼ = 0`).

42. Non-zero dual variables occur only for active constraints — the points exactly on the margin.
    Those are the support vectors, and the solution depends only on them.

43. The Hessian is `P × P` for `P` parameters (billions), so forming or inverting it is impossible;
    quasi-Newton methods still need large history and behave badly with mini-batch noise.

44. At a random critical point each Hessian eigenvalue is roughly equally likely to be positive or
    negative, so a true local minimum needs all `n` to be positive — probability `≈ 2/2ⁿ`.
    Almost every critical point is a saddle.

45. LR far too small or too large; the model cannot even overfit 10 samples (bug in the loss,
    labels, or the forward pass); labels shuffled relative to inputs; missing `optimizer.step()`
    or a stale `zero_grad()`; dead activations; inputs not normalised; last layer frozen.

46. `max vᵀΣv s.t. vᵀv = 1`. Lagrangian `vᵀΣv − λ(vᵀv − 1)`; stationarity gives `Σv = λv`.

47. Substituting `Σv = λv` into `vᵀΣv` gives `λvᵀv = λ`, so maximising the variance means picking
    the largest eigenvalue.

48. `‖x − Px‖² = ‖x‖² − xᵀPx` for an orthogonal projector `P`; summing, the first term is
    constant, so minimising the residual maximises `trace(V_kᵀΣV_k)`.

49. From `X_c = USVᵀ`: components are columns of `V`, scores are `US = X_cV`, variances are
    `σ²/(n−1)`.

50. Forming `XᵀX` squares the condition number and loses small singular values to round-off; SVD
    factors `X` directly.

51. Covariance PCA when all features share units; correlation PCA (standardise first) when they
    do not — otherwise the largest-scale feature dominates.

52. Cumulative explained-variance threshold (e.g. 95%) or treating `k` as a hyperparameter tuned
    by cross-validation on the downstream task; the scree/elbow plot as an informal check.

53. PCA is unsupervised and maximises total variance; LDA is supervised and maximises the ratio of
    between-class to within-class scatter, giving at most `C−1` components.

54. A linear autoencoder with squared reconstruction loss recovers the PCA subspace (though not
    necessarily orthonormal, ordered axes); non-linear autoencoders are strictly more expressive
    but lose the closed form and the ordering.

55. Fit on the training fold only, inside a `Pipeline` so cross-validation refits it per fold;
    transform validation/test with the fitted parameters. Otherwise leakage.

56. When `d ≫ n`, eigendecompose the `n × n` Gram matrix `X_cX_cᵀ` and map eigenvectors back via
    `v = X_cᵀu/‖X_cᵀu‖`; or use randomised truncated SVD.
