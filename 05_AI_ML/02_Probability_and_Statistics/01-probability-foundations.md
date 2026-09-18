
# Probability Foundations ⭐⭐⭐

> **Core idea in 3 lines**
> 1. Probability is a measure on events; everything else (conditioning, independence, Bayes) is
>    bookkeeping on top of that measure.
> 2. Bayes' rule is how a model turns evidence into belief — it is the skeleton of Naive Bayes,
>    MAP estimation, and every generative model.
> 3. Distributions are not trivia: each one encodes an assumption, and naming that assumption is
>    what interviewers are testing.

---

## 1. Axioms and the rules you actually use

Sample space `Ω`, events `A ⊆ Ω`.

```
(A1)  P(A) ≥ 0
(A2)  P(Ω) = 1
(A3)  A₁, A₂, … disjoint ⇒ P(∪Aᵢ) = Σ P(Aᵢ)
```

Consequences worth memorising:

| Rule | Statement |
|---|---|
| Complement | `P(Aᶜ) = 1 − P(A)` |
| Inclusion–exclusion | `P(A ∪ B) = P(A) + P(B) − P(A ∩ B)` |
| Conditional | `P(A\|B) = P(A ∩ B)/P(B)`, `P(B) > 0` |
| Chain rule | `P(A₁…A_n) = P(A₁)P(A₂\|A₁)…P(A_n\|A₁…A_{n−1})` |
| Total probability | `P(A) = Σᵢ P(A\|Bᵢ)P(Bᵢ)` for a partition `{Bᵢ}` |
| Bayes | `P(B\|A) = P(A\|B)P(B)/P(A)` |

**Independence** `P(A ∩ B) = P(A)P(B)` ⟺ `P(A|B) = P(A)`.
**Conditional independence** `P(A ∩ B | C) = P(A|C)P(B|C)`.

⚠️ Independence and conditional independence do **not** imply each other in either direction.
Classic example: two independent coin flips `A`, `B`, and `C = A XOR B`. `A ⫫ B`, but given `C`,
knowing `A` determines `B` — conditioning *destroyed* independence. This is exactly the
"explaining away" effect in Bayesian networks. ⭐

---

## 2. Bayes' rule in the form interviews use ⭐⭐⭐

```
                 P(E | H) · P(H)
P(H | E)  =  ───────────────────────
              P(E|H)P(H) + P(E|¬H)P(¬H)

posterior  =  (likelihood × prior) / evidence
```

### The medical-test problem (asked constantly)

A disease affects 1 in 1000. A test has 99% sensitivity `P(+|D) = 0.99` and 95% specificity, i.e.
false-positive rate `P(+|¬D) = 0.05`. You test positive. `P(D|+)`?

```
P(+) = 0.99(0.001) + 0.05(0.999) = 0.00099 + 0.049950 = 0.050940
P(D|+) = 0.00099 / 0.050940 ≈ 0.0194  ≈ 1.9%
```

**Natural-frequency version** (say this — it is far more convincing than algebra):

```
100 000 people
├── 100 have the disease      → 99 test positive
└── 99 900 healthy            → 4 995 test positive
                                 ─────
positives = 5 094, of which 99 are truly sick → 99/5094 ≈ 1.9%
```

⚠️ **Base-rate neglect** is the lesson: with a rare condition, even an accurate test yields mostly
false positives. The identical arithmetic explains why a fraud detector with 99% accuracy is
useless at 0.1% fraud prevalence — the link to class imbalance and to precision/recall. ⭐⭐⭐

---

## 3. Random variables

**Discrete** — PMF `p(x) = P(X = x)`, `Σp(x) = 1`.
**Continuous** — PDF `f(x) ≥ 0`, `∫f = 1`; `P(X = x) = 0`; `P(a ≤ X ≤ b) = ∫_a^b f`.
**CDF** `F(x) = P(X ≤ x)`, non-decreasing, right-continuous, `F(−∞)=0`, `F(∞)=1`, `f = F'`.

⚠️ A PDF may exceed 1 (e.g. `Uniform(0, 0.5)` has `f = 2`). Density is not probability.

**Joint, marginal, conditional:**

```
marginal    p(x) = Σ_y p(x, y)        or  ∫ p(x,y) dy
conditional p(y | x) = p(x, y)/p(x)
independent p(x, y) = p(x)p(y)  for all x, y
```

**Change of variables** (`Y = g(X)`, `g` monotone):

```
f_Y(y) = f_X(g⁻¹(y)) · |d g⁻¹/dy|
```

📐 This Jacobian factor is the entire basis of **normalising flows** and appears in the
reparameterisation trick for VAEs.

---

## 4. The distributions worth knowing cold

| Distribution | Use / meaning | Mean | Variance |
|---|---|---|---|
| Bernoulli(p) | one binary trial | `p` | `p(1−p)` |
| Binomial(n,p) | `n` independent trials | `np` | `np(1−p)` |
| Categorical(π) | one draw from `K` classes | — | — |
| Multinomial(n,π) | counts over `K` classes | `nπ_k` | `nπ_k(1−π_k)` |
| Geometric(p) | trials until first success | `1/p` | `(1−p)/p²` |
| Poisson(λ) | rare events per interval | `λ` | `λ` |
| Uniform(a,b) | no preference on an interval | `(a+b)/2` | `(b−a)²/12` |
| Exponential(λ) | waiting time, memoryless | `1/λ` | `1/λ²` |
| Normal(μ,σ²) | sums of many small effects (CLT) | `μ` | `σ²` |
| Beta(α,β) | belief about a probability | `α/(α+β)` | — |
| Gamma(α,β) | belief about a rate / positive scale | `α/β` | `α/β²` |
| Laplace(μ,b) | heavy-tailed; prior ⇒ L1 | `μ` | `2b²` |
| Student-t(ν) | heavy-tailed normal, small samples | `0` (ν>1) | `ν/(ν−2)` |

### Where each one shows up in ML ⭐

- **Bernoulli / Categorical** → the likelihood behind binary and multiclass cross-entropy.
- **Normal** → the likelihood behind MSE; also weight init and the noise model in linear regression.
- **Laplace prior** → L1 / lasso. **Normal prior** → L2 / ridge. (Proved in `03-estimation-mle-map.md`.)
- **Beta** → conjugate prior for Bernoulli; the "add-α smoothing" in Naive Bayes is a Beta/Dirichlet prior.
- **Dirichlet** → prior over topic mixtures in LDA.
- **Poisson** → count regression, click/event modelling.
- **Exponential** → survival analysis, time-to-churn.
- **Student-t** → robust regression; also the assumption behind small-sample t-tests.

### Memorylessness ⚠️

`P(X > s + t | X > s) = P(X > t)` holds **only** for Exponential (continuous) and Geometric
(discrete). A common trick question: "a bus arrives on average every 10 minutes; you have waited
10 minutes — how much longer?" If inter-arrivals are exponential, still 10 minutes on average.

### Normal facts to have ready

```
68 / 95 / 99.7  within 1 / 2 / 3 σ
X ~ N(μ, σ²)     ⇒  aX + b ~ N(aμ + b, a²σ²)
X ⫫ Y, both normal ⇒ X + Y ~ N(μ_X + μ_Y, σ_X² + σ_Y²)
standardise:  Z = (X − μ)/σ ~ N(0,1)
```

⚠️ Variances add for **independent** variables; in general
`Var(X + Y) = Var(X) + Var(Y) + 2Cov(X, Y)`.

---

## 5. Counting (OA questions still test this)

```
permutations   nPr = n!/(n−r)!
combinations   nCr = n!/(r!(n−r)!)
with repetition (multiset)  C(n + r − 1, r)
Stirling        n! ≈ √(2πn)(n/e)^n
```

Two classic results to be able to state instantly:

- **Birthday problem.** `P(no collision among k people) = Π_{i=0}^{k−1}(1 − i/365)`; at `k = 23`
  this drops below 0.5. Collisions become likely at about `√N` draws from `N` values — the same
  bound that governs hash collisions and the security of digests.
- **Coupon collector.** Expected draws to see all `n` coupons is `n·H_n ≈ n ln n`. This is the
  reason a "random sample until every class is seen" strategy is expensive for many classes.

---

## 6. A worked Bayes chain — Naive Bayes ⭐⭐

Naive Bayes is Bayes' rule plus one strong conditional-independence assumption:

```
P(y | x₁…x_d) ∝ P(y) Π_j P(xⱼ | y)          ← "naive": features independent GIVEN the class
```

```
   features x₁ … x_d
         │
         ▼
 ┌──────────────────────┐        ┌──────────────────────┐
 │ class prior P(y)     │        │ per-feature          │
 │ (counts / N)         │        │ likelihoods P(xⱼ|y)  │
 └─────────┬────────────┘        └──────────┬───────────┘
           └────────────┬───────────────────┘
                        ▼
          log P(y) + Σⱼ log P(xⱼ | y)      ← work in LOG space ⚠️
                        ▼
                  argmax over y
```

Points that earn marks:

- Work in log space; multiplying hundreds of small probabilities underflows to zero. ⚠️
- **Laplace / add-α smoothing**: an unseen word gives `P(xⱼ|y) = 0`, which annihilates the whole
  product. Use `(count + α)/(total + αV)`. This is exactly a Dirichlet prior. ⭐
- Variants: Multinomial NB (word counts), Bernoulli NB (word presence), Gaussian NB (continuous
  features, one `μ, σ²` per feature per class).
- It is a **generative** model — it models `P(x, y)` — whereas logistic regression is
  **discriminative**, modelling `P(y|x)` directly. Naive Bayes needs less data and trains in one
  pass; logistic regression usually wins asymptotically because it does not make the
  independence assumption. ⭐⭐

---

## 7. Probability ↔ information theory bridge

```
Entropy        H(X)   = −Σ p(x) log p(x)
Cross-entropy  H(p,q) = −Σ p(x) log q(x)
KL divergence  D(p‖q) = Σ p(x) log(p(x)/q(x)) = H(p,q) − H(p)
Mutual info    I(X;Y) = H(X) − H(X|Y) = D(p(x,y) ‖ p(x)p(y))
```

📐 **Minimising cross-entropy = minimising KL to the true distribution**, because `H(p)` does not
depend on the model. That is why the classification loss is called cross-entropy.

⚠️ KL is **not** symmetric and is not a distance.
- `D(p‖q)` (forward, "mean-seeking") blows up wherever `p > 0` but `q ≈ 0` ⇒ `q` must cover all of
  `p`'s support. This is the MLE direction.
- `D(q‖p)` (reverse, "mode-seeking") is what variational inference minimises ⇒ `q` collapses onto
  one mode. This is why VAEs can produce blurry/mode-collapsed samples. ⭐⭐

Information gain in a decision tree is exactly `I(X; Y)`: `H(parent) − Σ weighted H(children)`.

---

## Recall questions

1. State Bayes' rule and solve the 1-in-1000 disease test from scratch.
2. Give a concrete example where `A ⫫ B` but `A` and `B` are dependent given `C`.
3. Why can a PDF be greater than 1?
4. Which distributions are memoryless, and what does that mean operationally?
5. Which likelihood gives MSE, and which gives cross-entropy?
6. What exactly is "naive" about Naive Bayes, and what breaks without smoothing?
7. Show that minimising cross-entropy is minimising KL divergence.
8. Forward vs reverse KL — which does VI use, and what artefact does that cause?
