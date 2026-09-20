
# Number System, HCF/LCM, Remainders and Divisibility

> **Why it matters.** Every aptitude paper has 2-4 questions here, and the topic also feeds
> Programming Logic MCQs (prime checks, GCD, digit manipulation). It is the most "learnable" quant
> topic: the questions are formulaic once you know the theorems.

---

## 1. Classification of numbers

```
                        Real numbers
                    ┌────────┴────────┐
              Rational            Irrational  (√2, π, e, non-terminating non-repeating)
          ┌───────┴───────┐
      Integers         Fractions
   ┌──────┴──────┐
 Negative  Whole (0,1,2,…)
              └── Natural (1,2,3,…)
                     ├── Prime (exactly 2 factors)
                     ├── Composite (more than 2 factors)
                     └── 1 (neither prime nor composite) ⚠️
```

**Primes under 100** (memorise — saves real time):
```
2  3  5  7  11 13 17 19 23 29
31 37 41 43 47 53 59 61 67 71
73 79 83 89 97                      → 25 primes below 100
```
There are 168 primes below 1000.

⚠️ **2 is the only even prime.** 1 is neither prime nor composite. 0 is even.

---

## 2. Formula table

| Concept | Formula |
|---|---|
| **Number of factors** of `N = p₁^a · p₂^b · p₃^c …` | `(a+1)(b+1)(c+1)…` |
| **Sum of factors** | `[(p₁^(a+1)−1)/(p₁−1)] · [(p₂^(b+1)−1)/(p₂−1)] …` |
| **Product of factors** | `N^(number of factors / 2)` |
| **Number of odd factors** | Same formula, ignoring the power of 2 |
| **Number of even factors** | (total factors) − (odd factors) |
| **Number of ways to write N as a product of two factors** | `d(N)/2`, or `(d(N)+1)/2` if N is a perfect square |
| **HCF × LCM** | `= product of the two numbers` (two numbers only!) ⚠️ |
| **HCF of fractions** | `HCF(numerators) / LCM(denominators)` |
| **LCM of fractions** | `LCM(numerators) / HCF(denominators)` |
| **Euclid's algorithm** | `gcd(a,b) = gcd(b, a mod b)` |
| **Number of trailing zeros in n!** | `⌊n/5⌋ + ⌊n/25⌋ + ⌊n/125⌋ + …` ⭐ |
| **Highest power of prime p in n!** (Legendre) | `⌊n/p⌋ + ⌊n/p²⌋ + ⌊n/p³⌋ + …` |
| **Euler's totient** `φ(N)` | `N(1 − 1/p₁)(1 − 1/p₂)…` = count of numbers < N coprime to N |
| **Sum of numbers coprime to N and less than N** | `N·φ(N)/2` |

### Remainder theorems ⭐⭐

| Theorem | Statement | Use |
|---|---|---|
| **Fermat's little** | If `p` is prime and `gcd(a,p)=1`, then `a^(p−1) ≡ 1 (mod p)` | Reduce huge exponents mod a prime |
| **Euler's** | If `gcd(a,N)=1`, then `a^φ(N) ≡ 1 (mod N)` | Same, for composite modulus |
| **Wilson's** | `(p−1)! ≡ −1 (mod p)` for prime `p` | Factorial remainders |
| **Chinese Remainder** | Unique solution mod `m·n` when `gcd(m,n)=1` | "Remainder 2 by 3 and 3 by 5" problems |
| **Cyclicity of units digit** | Units digits repeat with period 1, 2 or 4 | Last-digit questions |

**Cyclicity table (units digit of aⁿ):**

| Last digit of a | Cycle | Period |
|---|---|---|
| 0, 1, 5, 6 | same digit always | 1 |
| 4 | 4, 6 | 2 |
| 9 | 9, 1 | 2 |
| 2 | 2, 4, 8, 6 | 4 |
| 3 | 3, 9, 7, 1 | 4 |
| 7 | 7, 9, 3, 1 | 4 |
| 8 | 8, 4, 2, 6 | 4 |

> **Method for "last digit of a^n":** take `n mod 4`. If the remainder is 0, use the 4th element of
> the cycle; otherwise use the r-th element.

---

## 3. Shortcuts and traps

```
⚠️ HCF × LCM = product of numbers holds for exactly TWO numbers, not three.
⚠️ The HCF of a set is always ≤ the smallest number; the LCM is always ≥ the largest.
⚠️ "Numbers which when divided by a,b,c leave the SAME remainder r"  → N = k·LCM(a,b,c) + r
⚠️ "…leave remainders a−k, b−k, c−k" (same deficiency k) → N = m·LCM(a,b,c) − k       ⭐
⚠️ Trailing zeros are governed by the count of 5s, not 2s (there are always more 2s).
⭐ To test primality of N, trial-divide by primes up to √N only.
⭐ A perfect square has an ODD number of factors — because one factor pairs with itself.
   (This is the whole basis of the "100 lockers / 100 doors" puzzle.)
⭐ Any prime > 3 is of the form 6k ± 1.
⭐ The product of any r consecutive integers is divisible by r!.
```

---

## 4. Fully solved examples

### Example 1 — Number and sum of factors
**Find the number of factors of 7560, the number of even factors, and the sum of all its factors.**

**Step 1 — prime factorise.**
```
7560 = 756 × 10 = (4 × 189) × 10 = 2² × 189 × 2 × 5 = 2³ × 189 × 5
189 = 27 × 7 = 3³ × 7
⇒ 7560 = 2³ · 3³ · 5¹ · 7¹
```

**Step 2 — number of factors** = `(3+1)(3+1)(1+1)(1+1) = 4 · 4 · 2 · 2 = **64**`.

**Step 3 — odd factors:** drop the 2s. `(3+1)(1+1)(1+1) = 4·2·2 = 16`.
**Even factors** = `64 − 16 = **48**`.

**Step 4 — sum of factors:**
```
(2⁴−1)/(2−1) × (3⁴−1)/(3−1) × (5²−1)/(5−1) × (7²−1)/(7−1)
= 15 × 40 × 6 × 8
= 15 × 40 = 600;  600 × 6 = 3600;  3600 × 8 = 28,800
```
**Answers: 64 factors, 48 even factors, sum = 28,800.**

---

### Example 2 — The "same deficiency" LCM pattern ⭐
**Find the least number which, when divided by 12, 15, 20 and 54, leaves remainders 8, 11, 16 and
50 respectively.**

**Observation:** `12−8 = 4`, `15−11 = 4`, `20−16 = 4`, `54−50 = 4`. **Common deficiency of 4.**

So `N + 4` is divisible by all four numbers.
```
LCM(12, 15, 20, 54):
  12 = 2²·3     15 = 3·5     20 = 2²·5     54 = 2·3³
  LCM = 2² · 3³ · 5 = 4 · 27 · 5 = 540
N + 4 = 540  ⇒  N = **536**
```
**Check:** 536 ÷ 12 = 44 r 8 ✓, 536 ÷ 54 = 9 r 50 ✓.

> If instead all remainders had been *equal* to r, the answer would be `LCM + r`. Spotting which of
> the two patterns you have is the entire question.

---

### Example 3 — Remainder with a large exponent
**Find the remainder when `7^103` is divided by 11.**

11 is prime and `gcd(7,11)=1`, so Fermat gives `7^10 ≡ 1 (mod 11)`.
```
103 = 10 × 10 + 3
7^103 = (7^10)^10 × 7³ ≡ 1^10 × 7³ = 343 (mod 11)
343 = 31 × 11 + 2   ⇒  remainder = **2**
```

**Cross-check by cyclicity of 7 mod 11:** 7, 5, 2, 3, 10, 4, 6, 9, 8, 1 — a cycle of length 10,
and the 3rd element is 2 ✓.

---

### Example 4 — Trailing zeros and highest power
**(a) How many trailing zeros does 200! have? (b) What is the highest power of 12 that divides
100!?**

**(a)**
```
⌊200/5⌋ + ⌊200/25⌋ + ⌊200/125⌋ = 40 + 8 + 1 = **49 zeros**
```

**(b)** `12 = 2² · 3`, so we need the powers of 2 and 3 in 100!.
```
Power of 2: ⌊100/2⌋+⌊100/4⌋+⌊100/8⌋+⌊100/16⌋+⌊100/32⌋+⌊100/64⌋
          = 50 + 25 + 12 + 6 + 3 + 1 = 97
Power of 3: ⌊100/3⌋+⌊100/9⌋+⌊100/27⌋+⌊100/81⌋ = 33 + 11 + 3 + 1 = 48

12^k needs 2^(2k) and 3^k.
From 2s:  2k ≤ 97  ⇒ k ≤ 48
From 3s:   k ≤ 48
⇒ k = **48**
```
⚠️ Note that you must take the **minimum** of the two bounds — and that the power of 2 is halved
because each 12 consumes two 2s. Both steps are common mistakes.

---

### Example 5 — Units digit and last two digits
**Find the units digit of `2^143 × 3^76 × 7^39`.**

```
2^143 : cycle (2,4,8,6), period 4.  143 mod 4 = 3  → 3rd element = 8
3^76  : cycle (3,9,7,1), period 4.   76 mod 4 = 0  → 4th element = 1
7^39  : cycle (7,9,3,1), period 4.   39 mod 4 = 3  → 3rd element = 3

Units digit = (8 × 1 × 3) mod 10 = 24 mod 10 = **4**
```

---

### Example 6 — HCF/LCM word problem
**Three bells ring at intervals of 36, 40 and 48 seconds. They ring together at 9:00:00 a.m. When
do they next ring together, and how many times do they ring together between 9 a.m. and 10 a.m.
(inclusive of 9:00)?**

```
LCM(36, 40, 48):
  36 = 2²·3²    40 = 2³·5    48 = 2⁴·3
  LCM = 2⁴ · 3² · 5 = 16 · 9 · 5 = 720 seconds = 12 minutes
```
**Next together: 9:12:00 a.m.**

In one hour = 3600 s, the number of coincidences after 9:00 is `⌊3600/720⌋ = 5` (at 9:12, 9:24,
9:36, 9:48, 10:00), plus the initial ring at 9:00 → **6 times inclusive**.

⚠️ The inclusive/exclusive count is where marks are lost. Read whether the starting instant counts.

---

### Example 7 — Chinese remainder style
**Find the smallest positive integer that leaves remainder 1 when divided by 2, 2 when divided by
3, 3 when divided by 4, 4 when divided by 5 and 5 when divided by 6.**

Deficiency check: `2−1=1`, `3−2=1`, `4−3=1`, `5−4=1`, `6−5=1` — **common deficiency 1**.
```
N + 1 = LCM(2,3,4,5,6) = 60  ⇒  N = **59**
```

---

## 5. Practice set

1. Number of factors of 1440.
2. The LCM of two numbers is 495 and their HCF is 5. If their sum is 100, find the numbers.
3. Remainder when `2^100` is divided by 7.
4. Trailing zeros in 125!.
5. Least number subtracted from 5029 to make it divisible by 43.
6. Units digit of `13^47 + 27^33`.
7. How many numbers below 1000 are divisible by neither 3 nor 5?
8. The greatest 4-digit number divisible by 15, 25 and 40.
9. Sum of all numbers less than 30 that are coprime to 30.
10. Highest power of 7 in 500!.

<details><summary>Answers</summary>

1. `1440 = 2⁵·3²·5` → `6·3·2 = 36`.
2. Product = `495×5 = 2475`; with sum 100 the numbers are roots of `x²−100x+2475=0` → `x = 45, 55`.
3. `2³ ≡ 1 (mod 7)`; `100 = 3×33+1` → `2^100 ≡ 2^1 = **2**`.
4. `25 + 5 + 1 = **31**`.
5. `5029 ÷ 43 = 116` r `41` → subtract **41**.
6. `13^47`: cycle of 3 is (3,9,7,1); `47 mod 4 = 3` → 7. `27^33`: cycle of 7 is (7,9,3,1);
   `33 mod 4 = 1` → 7. Sum `7+7 = 14` → units digit **4**.
7. `999 − (⌊999/3⌋ + ⌊999/5⌋ − ⌊999/15⌋) = 999 − (333 + 199 − 66) = 999 − 466 = **533**`.
8. `LCM(15,25,40) = 600`; `9999 ÷ 600 = 16` r `399` → `9999 − 399 = **9600**`.
9. `φ(30) = 30(1−1/2)(1−1/3)(1−1/5) = 8`; sum `= 30 × 8/2 = **120**`.
10. `⌊500/7⌋+⌊500/49⌋+⌊500/343⌋ = 71 + 10 + 1 = **82**`.
</details>

---

## Recall questions

1. Give the formula for the number of factors, and adapt it to count only even factors.
2. State the two LCM word-problem patterns (same remainder vs same deficiency).
3. Why do trailing zeros depend on the count of 5s?
4. State Fermat's little theorem and use it to reduce `a^n mod p`.
5. Give the cyclicity period for each possible units digit.
6. Why does a perfect square have an odd number of factors?
7. `HCF × LCM = product` — for how many numbers does this hold?
8. State Legendre's formula for the highest power of a prime in `n!`.
