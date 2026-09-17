# Complexity & Discrete Math — Concepts

## 1. Core idea in 3 lines
Asymptotic notation measures how cost *grows* with input size, discarding constants and lower-order terms. Every algorithmic decision in an OA is really a decision about which growth class you can afford. The number-theory material here exists because OA problems hide it inside otherwise ordinary array problems.

---

## 2. Asymptotic notation

| Notation | Meaning | Informally |
|---|---|---|
| O(f) | upper bound: g(n) ≤ c·f(n) for n ≥ n₀ | "at most, up to constants" |
| Ω(f) | lower bound | "at least" |
| Θ(f) | both | "exactly, up to constants" |
| o(f) | strictly smaller | "grows slower than" |

We say "O(n log n)" loosely to mean Θ; in an interview, saying **Θ** when you mean a tight bound is a small, cheap signal of precision.

**Rules:** drop constants (O(3n) = O(n)); keep the dominant term (O(n² + n log n) = O(n²)); nested independent loops multiply; sequential blocks add.

**Best / average / worst** are different questions from O/Ω/Θ. Quicksort is Θ(n log n) *average* and Θ(n²) *worst*; both statements use Θ.

### Amortised analysis
The average cost per operation over a worst-case *sequence*, not the average over random inputs.

- **Dynamic array push-back is O(1) amortised.** Doubling on overflow: across n pushes the total copy work is 1 + 2 + 4 + … + n < 2n, so O(1) each. This is the canonical interview example — know the summation.
- **Union-Find with path compression + union by rank is O(α(n))** amortised, where α is the inverse Ackermann function and α(n) ≤ 4 for any n you will ever see.
- A single operation can still be expensive; amortised says the *total* is bounded.

---

## 3. Recurrences and the master theorem

For **T(n) = a·T(n/b) + O(n^d)** with a ≥ 1, b > 1:

| Case | Condition | Result |
|---|---|---|
| 1 | a < b^d | Θ(n^d) — the work at the top dominates |
| 2 | a = b^d | Θ(n^d log n) — every level costs the same |
| 3 | a > b^d | Θ(n^{log_b a}) — the leaves dominate |

**Intuition:** compare the work done at the root, O(n^d), against the number of leaves, n^{log_b a}. Whichever grows faster wins; a tie gives a log n factor for the log n levels.

| Recurrence | Answer | Where it appears |
|---|---|---|
| T(n) = T(n/2) + O(1) | Θ(log n) | Binary search |
| T(n) = 2T(n/2) + O(1) | Θ(n) | Heapify, tree traversal |
| T(n) = 2T(n/2) + O(n) | Θ(n log n) | Merge sort |
| T(n) = T(n/2) + O(n) | Θ(n) | Average quickselect (sum of a geometric series) |
| T(n) = T(n−1) + O(n) | Θ(n²) | Selection sort, naive quicksort worst case |
| T(n) = 2T(n−1) + O(1) | Θ(2ⁿ) | Naive Fibonacci, subset enumeration |

The master theorem does **not** apply when the subproblems have unequal sizes (e.g. T(n) = T(n/3) + T(2n/3) + O(n) — that one is Θ(n log n), by the recursion tree).

---

## 4. Summations worth memorising

| Sum | Closed form | Class |
|---|---|---|
| 1 + 2 + … + n | n(n+1)/2 | Θ(n²) |
| 1² + 2² + … + n² | n(n+1)(2n+1)/6 | Θ(n³) |
| 1 + 2 + 4 + … + 2ᵏ | 2^{k+1} − 1 | Θ(2ᵏ) — the **last** term dominates |
| 1 + 1/2 + 1/3 + … + 1/n (Hₙ) | ≈ ln n + 0.577 | Θ(log n) |
| n/1 + n/2 + … + n/n | n·Hₙ | **Θ(n log n)** — the sieve bound |
| log 1 + log 2 + … + log n | log(n!) | Θ(n log n) |
| Σ over all subsets of their sizes | n·2^{n−1} | |
| Σ over all subsets of 2^{|S|} | 3ⁿ | submask enumeration |

The geometric-series fact — that a doubling sum is dominated by its last term — is why "half the work at each level" collapses to linear, and it is the single most reused identity in complexity arguments.

---

## 5. Number theory for OAs

### GCD / LCM
```
gcd(a, b) = gcd(b, a mod b),  gcd(a, 0) = a        # Euclid, O(log min(a,b))
lcm(a, b) = a // gcd(a, b) * b                      # divide FIRST to avoid overflow
```
Extended Euclid gives x, y with ax + by = gcd(a, b) — needed for modular inverses when the modulus is not prime.

### Modular arithmetic
```
(a + b) mod m = ((a mod m) + (b mod m)) mod m
(a × b) mod m = ((a mod m) × (b mod m)) mod m
(a − b) mod m = ((a mod m) − (b mod m) + m) mod m    # the +m matters
```
**Division does not distribute.** a/b mod m requires the modular inverse of b.

- **Fermat's little theorem:** if m is prime and gcd(b, m) = 1, then b^{m−1} ≡ 1, so **b⁻¹ ≡ b^{m−2} (mod m)**. This is why 10⁹+7 is chosen: it is prime.
- **Fast exponentiation** computes b^e mod m in O(log e) by squaring: b^e = (b^{e/2})² for even e, b·b^{e−1} for odd.

### Primes
- **Sieve of Eratosthenes:** O(n log log n) time, O(n) space. Mark multiples starting from i·i, and only for i ≤ √n.
- **Primality of a single n:** trial division by 2, 3, and then 6k±1 up to √n — O(√n).
- **Prime factorisation:** divide out each factor up to √n; whatever remains > 1 is itself prime.
- **Smallest prime factor sieve** lets you factorise any number ≤ n in O(log n) afterwards.

### Combinatorics
- Permutations of n distinct items: n! · of r from n: nPr = n!/(n−r)!
- Combinations: **nCr = n!/(r!(n−r)!)**, with nCr = nC(n−r) and Pascal's rule nCr = (n−1)C(r−1) + (n−1)Cr
- nCr mod p (p prime): precompute factorials and use Fermat for the inverse — O(n) precompute, O(log p) per query
- **Pigeonhole:** n+1 items in n boxes ⇒ some box holds ≥ 2. The backbone of "must exist a duplicate/collision" arguments
- **Inclusion-exclusion:** |A ∪ B| = |A| + |B| − |A ∩ B|; generalises with alternating signs
- **Stars and bars:** non-negative integer solutions to x₁+…+x_k = n is (n+k−1)C(k−1)

---

## 6. Overflow and precision

| Language | int range | What to do |
|---|---|---|
| C++ / Java | `int` = ±2.1×10⁹ | Use `long long` / `long` (±9.2×10¹⁸) the moment products are involved |
| Python | arbitrary precision | No overflow, but big ints are *slow* — take mod anyway |

**Triggers to watch for:** `a + b` where both are near 2³¹ (classic in `mid = (lo + hi) / 2` — use `lo + (hi − lo) / 2`); any product of two array values; prefix sums over 10⁵ elements each up to 10⁹ (10¹⁴ — overflows `int`); factorials beyond 12!.

**Floating point:** never compare with `==`. Use `abs(a − b) < 1e-9`. Binary search on reals runs a fixed ~100 iterations rather than testing equality.

---

## 7. Recall questions

1. State the master theorem's three cases and the intuition behind the comparison.
2. Why is dynamic-array push-back O(1) amortised? Give the summation.
3. What is Σ n/i for i = 1..n, and which algorithm's complexity does it give?
4. Why is 10⁹+7 the conventional modulus?
5. How do you compute nCr mod p for p prime, and what is the precompute cost?
6. Give three concrete situations where an `int` overflows in a typical OA problem.
7. What is the comparison-sort lower bound and where does it come from?
8. When does the master theorem **not** apply?
