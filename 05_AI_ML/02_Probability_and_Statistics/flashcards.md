
# Probability & Statistics — Flashcards

---

## Questions

**Foundations**
1. Bayes' rule, and the natural-frequency way of explaining it.
2. Why does a 99%-accurate test for a 1-in-1000 disease mostly produce false positives?
3. Independence vs conditional independence — give an example where one holds and the other fails.
4. Why can a PDF exceed 1?
5. Which distributions are memoryless?
6. Which distribution do you assume for: binary label, K-class label, count data, waiting time, a probability?
7. Conjugate prior for Bernoulli? For Multinomial? For Poisson?
8. What is "naive" in Naive Bayes, and what breaks without smoothing?
9. Define entropy, cross-entropy, KL, mutual information.
10. Show that minimising cross-entropy minimises KL.
11. Forward vs reverse KL — which is mode-seeking?

**Expectation and variance**
12. Does linearity of expectation need independence? Does additivity of variance?
13. `Var(aX + b)` = ?
14. Law of total expectation and law of total variance.
15. Fraction of unique rows in a bootstrap sample, and what OOB means.
16. Variance of an average of `B` models with pairwise correlation `ρ`.
17. Why `n−1` in the sample variance?
18. Give a zero-correlation, fully-dependent pair.
19. Standard error of the mean, and how much data halves it.
20. State the CLT precisely; what does it *not* claim?
21. When does the CLT fail?

**Inequalities**
22. Markov's inequality and its proof.
23. Chebyshev's inequality derived from Markov.
24. Hoeffding's bound and why it beats Chebyshev.
25. Jensen's inequality and two ML consequences.
26. `MSE = Bias² + Variance` — prove it.

**Estimation**
27. MLE definition and why we take logs.
28. MLE for Bernoulli; for Gaussian mean and variance.
29. Which noise model gives MSE? MAE? Cross-entropy?
30. Gaussian prior ⇒ which penalty, and `λ` = ?
31. Laplace prior ⇒ which penalty?
32. MAP as `n → ∞`?
33. E-step, M-step, and EM's guarantee.
34. In what sense is k-means EM?
35. Cramér–Rao and the asymptotic behaviour of the MLE.
36. AIC vs BIC.

**Testing**
37. Correct definition of a p-value, plus three wrong ones.
38. Type I vs Type II error; α and power conventions.
39. t-test vs z-test vs chi-square vs Mann–Whitney.
40. Sample-size formula for two proportions; the `1/δ²` scaling.
41. Why is peeking invalid, and what are two valid fixes?
42. Randomisation unit in an A/B test, and why.
43. What is a sample ratio mismatch?
44. Novelty effect, Simpson's paradox, network effects — one line each.
45. Bonferroni vs Benjamini–Hochberg.
46. How do you report that model B beats model A offline?
47. A/B test vs multi-armed bandit — when each?

---

## Answers

1. `P(H|E) = P(E|H)P(H)/P(E)`. Frequencies: out of 100 000 people, how many have the condition and
   test positive, versus how many are healthy and test positive.

2. Because the healthy group is ~1000× larger, its 5% false-positive rate produces far more
   positives than the sick group's 99% true-positive rate. Base rates dominate.

3. Two independent fair coins `A`, `B` with `C = A XOR B`: `A ⫫ B`, but given `C`, `A` determines
   `B`. Conversely, two symptoms may be dependent marginally yet conditionally independent given
   the disease.

4. A density is probability per unit length; only its integral must be 1. `Uniform(0, 0.5)` has
   density 2.

5. Exponential (continuous) and Geometric (discrete), and only those.

6. Bernoulli; Categorical; Poisson; Exponential; Beta.

7. Beta; Dirichlet; Gamma.

8. Features are assumed conditionally independent given the class. Without add-α smoothing a
   single unseen feature value gives a zero likelihood, which zeroes the whole product.

9. `H = −Σp log p`; `H(p,q) = −Σp log q`; `D(p‖q) = Σp log(p/q)`; `I(X;Y) = H(X) − H(X|Y)`.

10. `H(p,q) = H(p) + D(p‖q)`; `H(p)` is fixed by the data, so minimising cross-entropy over the
    model is minimising KL.

11. Reverse KL `D(q‖p)` is mode-seeking — it is what variational inference minimises, which is why
    VI/VAEs can under-cover multimodal posteriors. Forward KL is mean-seeking (the MLE direction).

12. Linearity always holds. Variance additivity needs zero covariance (implied by independence).

13. `a²Var(X)`.

14. `E[X] = E[E[X|Y]]`; `Var(X) = E[Var(X|Y)] + Var(E[X|Y])` — unexplained plus explained.

15. `1 − (1−1/n)ⁿ → 1 − e⁻¹ ≈ 63.2%`. The ~36.8% left out are out-of-bag rows, used as a free
    validation set per tree.

16. `ρσ² + (1−ρ)σ²/B`; the floor `ρσ²` motivates feature subsampling to decorrelate trees.

17. Using `x̄` instead of `μ` removes a degree of freedom;
    `E[Σ(xᵢ−x̄)²] = (n−1)σ²`.

18. `X ~ U(−1,1)`, `Y = X²`: `Cov = 0` but `Y` is a function of `X`. Correlation sees only linear
    structure.

19. `SE = σ/√n`; halving it requires 4× the data.

20. `(X̄ − μ)/(σ/√n) → N(0,1)` for i.i.d. finite-variance samples. It says nothing about the
    distribution of the raw data.

21. Infinite variance, strong dependence, or too-small `n` with heavy skew.

22. `P(X ≥ a) ≤ E[X]/a` for `X ≥ 0`; proof by splitting the integral at `a` and lower-bounding `x`
    by `a` on the tail.

23. Apply Markov to `(X−μ)²` at level `k²σ²`, giving `P(|X−μ| ≥ kσ) ≤ 1/k²`.

24. `P(|X̄ − μ| ≥ t) ≤ 2exp(−2nt²/(b−a)²)` for bounded variables — exponential in `n` rather than
    Chebyshev's `1/(nt²)`; it is the basis of generalisation bounds.

25. `φ(E[X]) ≤ E[φ(X)]` for convex `φ`. Consequences: `D(p‖q) ≥ 0`, and the ELBO used by VAEs/EM.

26. Add and subtract `E[θ̂]` inside the square; the cross-term vanishes because
    `E[θ̂ − E[θ̂]] = 0`.

27. `argmax_θ Σ log p(xᵢ|θ)`. Logs turn products into sums, avoid underflow, and preserve the
    argmax.

28. Bernoulli: `k/n`. Gaussian: `μ̂ = x̄`, `σ̂² = (1/n)Σ(xᵢ−x̄)²` (biased).

29. Gaussian ⇒ MSE; Laplace ⇒ MAE; Bernoulli/Categorical ⇒ cross-entropy.

30. L2 / ridge, with `λ = σ²/τ²`.

31. L1 / lasso.

32. MAP → MLE: the log-likelihood scales with `n` while the log-prior stays `O(1)`.

33. E: compute responsibilities `q(z) = p(z|x, θ_old)`. M: maximise `E_q[log p(x,z|θ)]`. Guarantee:
    the observed-data log-likelihood never decreases; convergence to a local optimum.

34. k-means is EM on an isotropic equal-variance Gaussian mixture with hard (argmax) assignments in
    the E-step.

35. `Var(θ̂) ≥ 1/(nI(θ))` for unbiased estimators; the MLE is consistent and asymptotically
    normal and efficient, attaining the bound as `n → ∞`.

36. `AIC = 2k − 2ℓ` (predictive focus, weaker penalty); `BIC = k log n − 2ℓ` (consistency focus,
    penalises complexity more as `n` grows).

37. `P(data at least this extreme | H₀ true)`. Wrong: probability `H₀` is true; probability the
    result is a fluke; a measure of effect size.

38. Type I = false positive (rate α, usually 0.05); Type II = false negative (rate β; power
    `1 − β`, usually 0.80).

39. t-test: means with estimated variance. z-test: means with known variance / large-sample
    proportions. Chi-square: categorical association or goodness of fit. Mann–Whitney: non-normal
    or ordinal data.

40. `n ≈ 16p(1−p)/δ²` per arm; `n ∝ 1/δ²`, so halving the detectable effect quadruples `n`.

41. Repeated significance tests on accumulating data inflate the false-positive rate well above α.
    Fixes: a pre-registered fixed horizon, or sequential/always-valid methods (SPRT, alpha
    spending, mSPRT).

42. By user (stable hash of the user id) — otherwise the same person sees both variants and the
    arms are not independent.

43. The observed traffic split differs significantly from the intended one, indicating a bug in
    assignment, logging or redirects; chi-square the split and fix before analysing.

44. Novelty: early lift from newness that decays. Simpson's paradox: aggregate direction reverses
    within every segment. Network effects: treatment spills over to control users, biasing the
    measured effect toward zero.

45. Bonferroni controls the family-wise error rate (any false positive) at `α/m`; BH controls the
    expected false-discovery proportion and is far less conservative when `m` is large.

46. Bootstrap the test set (or use paired tests across CV folds) and report the metric with a
    confidence interval, not just a point estimate; state the effect size.

47. Bandit for short-lived choices where regret matters and you want traffic to shift to the
    winner; fixed A/B test when you need an unbiased effect estimate for a durable decision.
