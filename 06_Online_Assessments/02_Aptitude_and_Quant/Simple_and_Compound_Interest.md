
# Simple Interest, Compound Interest and Instalments

> **Why it matters.** 1-3 questions per paper, and the formulas are short. This is one of the
> highest accuracy-per-minute topics available — treat it as guaranteed marks.

---

## 1. Definitions

```
P  Principal (the amount borrowed or invested)
R  Rate of interest per annum (percent)
T  Time in years
SI Simple interest        A = P + SI        (A = amount)
CI Compound interest      A = P(1 + R/100)^T
```

**The distinction:** simple interest is always computed on the *original* principal; compound
interest is computed on the *running amount*, so interest earns interest.

---

## 2. Formula table

| Quantity | Formula |
|---|---|
| **Simple interest** | `SI = PRT/100` |
| Amount (SI) | `A = P(1 + RT/100)` |
| **Compound amount**, annual | `A = P(1 + R/100)^T` |
| CI | `CI = A − P = P[(1 + R/100)^T − 1]` |
| Compounded **half-yearly** | `A = P(1 + R/200)^(2T)` ⭐ rate halves, periods double |
| Compounded **quarterly** | `A = P(1 + R/400)^(4T)` |
| Compounded `n` times a year | `A = P(1 + R/(100n))^(nT)` |
| **Different rates** each year | `A = P(1 + R₁/100)(1 + R₂/100)(1 + R₃/100)…` ⭐ |
| **CI − SI for 2 years** | `= P(R/100)²` ⭐⭐⭐ |
| **CI − SI for 3 years** | `= P(R/100)² × (300 + R)/100` ⭐ |
| SI doubles the principal in | `T = 100/R` years |
| CI: amount becomes `n`× in `t` years, then `n²`× in | `2t` years ⭐ |
| Population/depreciation | `P(1 ± R/100)^T` |
| Equal annual instalment (SI basis) | See §5 Example 6 |
| Present value of an instalment at CI | `PV = X/(1 + R/100)ⁿ` |

---

## 3. The key derivation you should be able to reproduce 📐

**Why is `CI − SI` for two years exactly `P(R/100)²`?**

```
Let r = R/100.

SI for 2 years = P·r·2 = 2Pr
CI amount      = P(1 + r)² = P(1 + 2r + r²)
CI             = P(1 + 2r + r²) − P = 2Pr + Pr²

CI − SI = (2Pr + Pr²) − 2Pr = **Pr² = P(R/100)²**            ∎
```

**Interpretation:** the difference is exactly the interest earned in the second year *on the first
year's interest*. First-year interest is `Pr`; interest on that in year two is `Pr × r = Pr²`.
Understanding it this way makes the three-year formula obvious too.

⭐ This formula is asked constantly in the form "the difference between CI and SI on a sum for 2
years at 10% is ₹50; find the sum" — answer `50 = P(0.1)² = 0.01P ⇒ P = ₹5000`, in five seconds.

---

## 4. Shortcuts and traps

```
⭐⭐⭐ For 2-year CI questions, use the successive-percentage shortcut from Percentages.md:
      CI% for 2 years at R% = 2R + R²/100.
      At 10%: 20 + 1 = 21%.  At 20%: 40 + 4 = 44%.  At 5%: 10 + 0.25 = 10.25%.
⭐ Memorise these 2-year and 3-year CI multipliers:
      5%  → 1.1025 (2 yr), 1.157625 (3 yr)
      10% → 1.21,   1.331
      20% → 1.44,   1.728
      25% → 1.5625, 1.953125
⚠️ "Compounded half-yearly at 10% for 2 years" means 4 periods at 5%, not 2 periods at 10%.
⚠️ SI and CI are EQUAL for the first year (at annual compounding). Differences start in year 2.
⭐ If a sum becomes n times in t years under CI, it becomes n² times in 2t years and n³ in 3t.
   (Because the multiplier compounds: (n)^(2) for doubled time.)
   Under SI the logic is different: if it doubles in t years, it triples in 2t years ⚠️
   (SI adds a constant each year: to double you add P; to triple you add 2P, taking twice as long.)
⭐ Depreciation is just CI with a negative rate.
```

**The SI-vs-CI doubling trap, made explicit:**
```
SI : doubles in 10 years  ⇒  R = 10%  ⇒  triples (adds 2P) in 20 years, quadruples in 30.
CI : doubles in 10 years  ⇒  quadruples in 20 years, becomes 8× in 30.
```

---

## 5. Fully solved examples

### Example 1 — CI − SI, both directions ⭐
**(a) The difference between the compound and simple interest on a certain sum at 12% per annum for
2 years is ₹90. Find the sum.
(b) For the same sum and rate, what is the difference over 3 years?**

**(a)**
```
CI − SI (2 yr) = P(R/100)²
90 = P(0.12)² = P × 0.0144
P = 90/0.0144 = **₹6,250**
```

**(b)**
```
CI − SI (3 yr) = P(R/100)² × (300 + R)/100
               = 6250 × 0.0144 × (312/100)
               = 90 × 3.12
               = **₹280.80**
```

---

### Example 2 — Half-yearly compounding ⚠️
**Find the compound interest on ₹8,000 at 10% per annum for 1.5 years, compounded half-yearly.**

```
Rate per half-year = 10/2 = 5%
Number of periods  = 1.5 × 2 = 3

A = 8000 (1.05)³
(1.05)³ = 1.157625
A = 8000 × 1.157625 = ₹9,261
CI = 9261 − 8000 = **₹1,261**
```

**Compare with annual compounding** for the same period: `8000 × 1.10 × (1.05) = ₹9,240`, giving CI
of ₹1,240. More frequent compounding always yields more. ⭐

---

### Example 3 — Different rates in different years
**A sum of ₹12,000 is invested for 3 years at 5%, 10% and 15% per annum respectively. Find the
amount and the compound interest.**

```
A = 12000 × 1.05 × 1.10 × 1.15

12000 × 1.05 = 12,600
12,600 × 1.10 = 13,860
13,860 × 1.15 = 15,939

A = **₹15,939**        CI = 15,939 − 12,000 = **₹3,939**
```
⚠️ The order of the rates does not matter (multiplication is commutative) — a fact some questions
test directly.

---

### Example 4 — Finding the rate from two amounts ⭐
**A sum amounts to ₹8,820 in 2 years and ₹9,261 in 3 years under compound interest. Find the rate
and the principal.**

The third year's interest is earned on the second year's amount:
```
Interest in year 3 = 9261 − 8820 = ₹441
Rate = 441/8820 × 100 = **5%**

Principal: P × (1.05)² = 8820
(1.05)² = 1.1025
P = 8820 / 1.1025 = **₹8,000**
```
**Check:** `8000 × 1.05 = 8400 → 8820 → 9261` ✓

⭐ **The general trick:** with CI, `Rate = (A_{n+1} − A_n)/A_n × 100`. Two consecutive amounts give
you the rate in one subtraction and one division.

---

### Example 5 — SI with a changed principal
**A person invests ₹10,000, part at 8% simple interest and the rest at 12% simple interest. After
one year the total interest is ₹1,060. How much was invested at 8%?**

```
Let ₹x be invested at 8%, so (10000 − x) at 12%.
0.08x + 0.12(10000 − x) = 1060
0.08x + 1200 − 0.12x    = 1060
−0.04x = −140
x = **₹3,500** at 8%,  ₹6,500 at 12%
```

**Alligation shortcut ⭐:**
```
Overall rate = 1060/10000 = 10.6%
        8% ──────── 10.6% ──────── 12%
distances:  |12 − 10.6| = 1.4     |10.6 − 8| = 2.6
ratio (8% part : 12% part) = 1.4 : 2.6 = 7 : 13
Total 20 parts = 10,000 → 1 part = 500
8% part = 7 × 500 = ₹3,500 ✓
```

---

### Example 6 — Equal instalments ⭐⭐
**A loan of ₹3,000 is to be repaid in 3 equal annual instalments at 10% per annum compound
interest. Find the instalment amount.**

Each instalment's **present value** must sum to the loan:
```
X/(1.1) + X/(1.1)² + X/(1.1)³ = 3000

Multiply through by (1.1)³ = 1.331:
X(1.1)² + X(1.1) + X = 3000 × 1.331
X(1.21 + 1.10 + 1.00) = 3993
X × 3.31 = 3993
X = **₹1,206.34**
```

**Verification by running the loan forward:**
```
Year 1: 3000 × 1.1 = 3300;  pay 1206.34  → 2093.66
Year 2: 2093.66 × 1.1 = 2303.03;  pay 1206.34 → 1096.69
Year 3: 1096.69 × 1.1 = 1206.36;  pay 1206.34 → ≈ 0  ✓ (rounding)
```

---

### Example 7 — Doubling and multiplying ⭐
**(a) A sum doubles in 8 years under simple interest. In how many years will it become 5 times?
(b) A sum doubles in 6 years under compound interest. In how many years will it become 8 times?**

**(a) SI.** Doubling means the interest equals the principal.
```
SI = P in 8 years  ⇒  R = 100/8 = 12.5%
To become 5 times, interest must be 4P:
  4P = P × 12.5 × T/100  ⇒  T = 400/12.5 = **32 years**
```
⭐ Shortcut: under SI, `(n − 1)` units of interest are needed for `n` times, so
`T = (n−1) × t_double`… careful: `t` for doubling covers 1 unit, so 5× needs 4 units → `4 × 8 = 32` ✓

**(b) CI.** The multiplier compounds.
```
2× in 6 years  ⇒  4× in 12 years  ⇒  8× in **18 years**
(because 8 = 2³, so three doubling periods)
```

---

## 6. Practice set

1. SI on ₹5,000 at 8% for 3 years.
2. CI on ₹10,000 at 10% for 2 years.
3. Difference between CI and SI on ₹20,000 at 5% for 2 years.
4. A sum amounts to ₹6,050 in 2 years at 10% CI. Find the principal.
5. At what rate does SI double a sum in 12.5 years?
6. CI on ₹16,000 at 20% for 1 year, compounded quarterly.
7. A machine depreciates 10% per year. Value after 3 years if bought at ₹50,000.
8. A sum triples in 12 years at SI. Rate?
9. A sum becomes 3 times in 5 years at CI. In how many years does it become 27 times?
10. The CI on a sum for the 2nd year is ₹880 and for the 3rd year is ₹968. Find the rate.

<details><summary>Answers</summary>

1. `5000 × 8 × 3/100 = **₹1,200**`.
2. `10000(1.21) − 10000 = **₹2,100**`.
3. `20000 × (0.05)² = 20000 × 0.0025 = **₹50**`.
4. `6050/1.21 = **₹5,000**`.
5. `100/12.5 = **8%**`.
6. 4 quarters at 5%: `16000 × (1.05)⁴ = 16000 × 1.21550625 = 19,448.10` → CI **₹3,448.10**.
7. `50000 × (0.9)³ = 50000 × 0.729 = **₹36,450**`.
8. Interest = 2P in 12 years → `R = 200/12 = **16⅔%**`.
9. `27 = 3³` → three periods of 5 years → **15 years**.
10. `(968 − 880)/880 × 100 = 88/880 × 100 = **10%**`.
</details>

---

## Recall questions

1. Derive `CI − SI = P(R/100)²` for two years and explain it in words.
2. Convert "10% per annum compounded quarterly for 2 years" into rate and periods.
3. Give the 2-year CI percentage shortcut and evaluate it at 20%.
4. How do you find the rate from two consecutive CI amounts?
5. Contrast the SI and CI answers to "doubles in t years, when does it become 4 times?"
6. Set up the equal-instalment equation for 3 years at rate R.
7. Why does the order of different annual rates not matter?
