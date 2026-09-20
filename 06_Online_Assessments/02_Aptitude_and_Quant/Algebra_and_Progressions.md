
# Algebra, Equations, Progressions, Logarithms and Surds

> **Why it matters.** Appears directly (1-3 questions) and indirectly — almost every word problem
> resolves into a linear or quadratic equation. Progressions and logarithms are short, formulaic
> and worth guaranteed marks.

---

## 1. Algebraic identities (recall list)

```
(a + b)²        = a² + 2ab + b²
(a − b)²        = a² − 2ab + b²
a² − b²         = (a + b)(a − b)
(a + b)³        = a³ + b³ + 3ab(a + b)
(a − b)³        = a³ − b³ − 3ab(a − b)
a³ + b³         = (a + b)(a² − ab + b²)
a³ − b³         = (a − b)(a² + ab + b²)
(a + b + c)²    = a² + b² + c² + 2(ab + bc + ca)
a³ + b³ + c³ − 3abc = (a + b + c)(a² + b² + c² − ab − bc − ca)
If a + b + c = 0  ⇒  a³ + b³ + c³ = 3abc                ⭐ appears surprisingly often
```

**The reciprocal family ⭐⭐** (a perennial exam favourite):
```
If x + 1/x = k, then
   x² + 1/x² = k² − 2
   x³ + 1/x³ = k³ − 3k
   x⁴ + 1/x⁴ = (k² − 2)² − 2
If x − 1/x = k, then
   x² + 1/x² = k² + 2
   x³ − 1/x³ = k³ + 3k
```

---

## 2. Quadratic equations

For `ax² + bx + c = 0`:

| Quantity | Formula |
|---|---|
| Roots | `x = [−b ± √(b² − 4ac)] / 2a` |
| Discriminant | `D = b² − 4ac` |
| Sum of roots | `α + β = −b/a` ⭐ |
| Product of roots | `αβ = c/a` ⭐ |
| Equation from roots | `x² − (sum)x + (product) = 0` |
| `α² + β²` | `(α+β)² − 2αβ` |
| `α³ + β³` | `(α+β)³ − 3αβ(α+β)` |
| `1/α + 1/β` | `(α+β)/αβ` |
| `α − β` | `±√D/a` |

**Nature of the roots:**
```
D > 0 and a perfect square → real, rational, distinct
D > 0 not a perfect square → real, irrational, distinct (conjugate surds)
D = 0                      → real, equal
D < 0                      → complex conjugates
```

---

## 3. Progressions

### Arithmetic progression (AP)
```
Terms:  a, a+d, a+2d, …
nth term       Tₙ = a + (n − 1)d
Sum of n terms Sₙ = n/2 [2a + (n − 1)d]  =  n/2 (first + last)    ⭐ use the second form
Number of terms n = (last − first)/d + 1                          ⚠️ the "+1" is missed constantly
Arithmetic mean of a and b = (a + b)/2
```

### Geometric progression (GP)
```
Terms:  a, ar, ar², …
nth term        Tₙ = a r^(n−1)
Sum of n terms  Sₙ = a(rⁿ − 1)/(r − 1)   for r > 1
                Sₙ = a(1 − rⁿ)/(1 − r)   for r < 1
Sum to infinity S∞ = a/(1 − r)   valid only when |r| < 1    ⭐⭐
Geometric mean of a and b = √(ab)
```

### Harmonic progression (HP)
Reciprocals form an AP. `Harmonic mean of a and b = 2ab/(a + b)` ⭐ — the same formula as
average speed over equal distances. That is not a coincidence.

**The mean inequality ⭐:** `AM ≥ GM ≥ HM`, with equality only when all terms are equal.
Also `GM² = AM × HM`.

### Standard sums
```
Σ n      = n(n + 1)/2
Σ n²     = n(n + 1)(2n + 1)/6
Σ n³     = [n(n + 1)/2]²                     ⭐ = (Σn)²
Sum of the first n odd numbers  = n²
Sum of the first n even numbers = n(n + 1)
```

---

## 4. Logarithms

| Rule | Statement |
|---|---|
| Definition | `log_a N = x  ⇔  aˣ = N` |
| Product | `log(mn) = log m + log n` |
| Quotient | `log(m/n) = log m − log n` |
| Power | `log(mⁿ) = n log m` |
| Base change ⭐ | `log_a b = log_c b / log_c a` |
| Reciprocal | `log_a b = 1 / log_b a` |
| Identity | `log_a a = 1`, `log_a 1 = 0` |
| `a^(log_a x)` | `= x` |

**Values to memorise:** `log₁₀2 = 0.3010`, `log₁₀3 = 0.4771`, `log₁₀5 = 0.6990`, `log₁₀7 = 0.8451`.

⭐ **The digit-count trick:** the number of digits in `N` is `⌊log₁₀ N⌋ + 1`.
*Example:* digits in `2⁵⁰` → `50 × 0.3010 = 15.05` → `⌊15.05⌋ + 1 = **16 digits**`.

---

## 5. Surds and indices

```
aᵐ · aⁿ = a^(m+n)        aᵐ/aⁿ = a^(m−n)        (aᵐ)ⁿ = a^(mn)
a⁰ = 1  (a ≠ 0)          a^(−n) = 1/aⁿ          a^(1/n) = ⁿ√a
Rationalising:  1/(√a + √b) = (√a − √b)/(a − b)      ⭐
```

---

## 6. Fully solved examples

### Example 1 — Quadratic without solving ⭐
**If α and β are the roots of `2x² − 7x + 3 = 0`, find `α² + β²` and `1/α + 1/β` without finding
the roots.**

```
α + β = −b/a = 7/2
αβ    =  c/a = 3/2

α² + β² = (α+β)² − 2αβ = (7/2)² − 2(3/2) = 49/4 − 3 = 49/4 − 12/4 = **37/4**

1/α + 1/β = (α+β)/αβ = (7/2)/(3/2) = **7/3**
```
⭐ Never solve the quadratic if the question only asks for a symmetric function of the roots.

---

### Example 2 — Sum of an AP with a twist
**Find the sum of all three-digit numbers divisible by 7.**

```
First three-digit multiple of 7: 105 (since 7 × 15 = 105)
Last:  994 (7 × 142)
d = 7

n = (994 − 105)/7 + 1 = 889/7 + 1 = 127 + 1 = **128 terms**

S = n/2 (first + last) = 128/2 × (105 + 994) = 64 × 1099 = **70,336**
```
⚠️ The `+1` when counting terms is the most commonly dropped step in the entire topic.

---

### Example 3 — Infinite GP ⭐
**Evaluate `4 + 4/3 + 4/9 + 4/27 + …` and find the sum of `3 + 6 + 12 + 24 + …` up to 8 terms.**

**(a)** `a = 4`, `r = 1/3` (since `|r| < 1`, the infinite sum converges):
```
S∞ = a/(1 − r) = 4/(1 − 1/3) = 4/(2/3) = **6**
```

**(b)** `a = 3`, `r = 2`, `n = 8`:
```
S = a(rⁿ − 1)/(r − 1) = 3(2⁸ − 1)/(2 − 1) = 3(256 − 1) = 3 × 255 = **765**
```

---

### Example 4 — Logarithms and digits ⭐
**(a) Find `log₁₀ 720` using the standard values. (b) How many digits are there in `3⁴⁰`?**

**(a)**
```
720 = 72 × 10 = 8 × 9 × 10 = 2³ · 3² · 10
log 720 = 3 log 2 + 2 log 3 + log 10
        = 3(0.3010) + 2(0.4771) + 1
        = 0.9030 + 0.9542 + 1
        = **2.8572**
```

**(b)**
```
log₁₀(3⁴⁰) = 40 × 0.4771 = 19.084
Digits = ⌊19.084⌋ + 1 = **20 digits**
```

---

### Example 5 — Word problem to simultaneous equations
**The cost of 3 pens and 5 notebooks is ₹255. The cost of 5 pens and 3 notebooks is ₹225. Find the
cost of one pen and one notebook.**

```
3p + 5n = 255   … (1)
5p + 3n = 225   … (2)

ADD:      8p + 8n = 480   ⇒  p + n = 60      ⭐ the elegant step
SUBTRACT: 2p − 2n = −30   ⇒  p − n = −15

Solving:  2p = 45  ⇒  p = **₹22.50**,   n = **₹37.50**
```
⭐ **When the coefficients are mirror images (3,5 and 5,3), add and subtract the equations rather
than eliminating a variable the long way.** It halves the work.

---

### Example 6 — Reciprocal identity
**If `x + 1/x = 5`, find `x³ + 1/x³` and `x⁴ + 1/x⁴`.**

```
x² + 1/x² = (x + 1/x)² − 2 = 25 − 2 = 23
x³ + 1/x³ = (x + 1/x)³ − 3(x + 1/x) = 125 − 15 = **110**
x⁴ + 1/x⁴ = (x² + 1/x²)² − 2 = 23² − 2 = 529 − 2 = **527**
```

---

### Example 7 — AM-GM in an optimisation dressing ⭐
**The sum of two positive numbers is 20. What is their maximum possible product?**

```
By AM ≥ GM:  (a + b)/2 ≥ √(ab)
             10 ≥ √(ab)
             100 ≥ ab
Maximum product = **100**, attained when a = b = 10.
```
⭐ **The general result:** for a fixed sum, the product is maximised when the numbers are equal;
for a fixed product, the sum is minimised when they are equal. This single principle answers a
whole family of "maximum area of a rectangle with fixed perimeter" questions too (a square).

---

## 7. Practice set

1. If `α, β` are roots of `x² − 5x + 6 = 0`, find `α³ + β³`.
2. Find the 20th term of the AP 7, 11, 15, …
3. Sum of the first 25 terms of the AP 3, 8, 13, …
4. Sum to infinity of `1 + 1/2 + 1/4 + …`
5. If `log 2 = 0.3010`, find the number of digits in `2¹⁰⁰`.
6. Solve `2x + 3y = 13` and `3x − y = 3`.
7. If `x − 1/x = 3`, find `x² + 1/x²`.
8. The AM of two numbers is 25 and their GM is 20. Find the numbers.
9. How many terms of the AP 5, 9, 13, … sum to 275?
10. Simplify `1/(√5 − √3)`.

<details><summary>Answers</summary>

1. `α+β = 5`, `αβ = 6`; `α³+β³ = 125 − 3(6)(5) = 125 − 90 = **35**`.
2. `7 + 19×4 = **83**`.
3. `25/2 [2(3) + 24(5)] = 12.5 × 126 = **1575**`.
4. `1/(1 − 1/2) = **2**`.
5. `100 × 0.3010 = 30.10` → `**31 digits**`.
6. From the second, `y = 3x − 3`; substitute: `2x + 9x − 9 = 13 ⇒ x = 2`, `y = 3`.
7. `k² + 2 = 9 + 2 = **11**`.
8. `a+b = 50`, `ab = 400` → `x² − 50x + 400 = 0` → `x = 10, 40`.
9. `n/2[10 + (n−1)4] = 275 ⇒ n(4n + 6) = 550 ⇒ 2n² + 3n − 275 = 0 ⇒ n = **11**`.
10. `(√5 + √3)/(5 − 3) = **(√5 + √3)/2**`.
</details>

---

## Recall questions

1. Give the sum and product of the roots, and use them for `α² + β²`.
2. State the AP term-count formula and name the step people drop.
3. When is the infinite GP sum valid, and what is it?
4. State AM ≥ GM ≥ HM and give an optimisation it solves.
5. Give the digit-count formula using logarithms.
6. List the reciprocal identities for `x + 1/x = k`.
7. When two simultaneous equations have mirrored coefficients, what is the fast method?
