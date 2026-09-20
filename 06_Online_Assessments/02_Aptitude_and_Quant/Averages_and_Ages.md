
# Averages, Ages and Weighted Means

> **Why it matters.** 2-3 questions per paper. Averages are easy marks *if* you use the deviation
> method instead of recomputing sums. Age problems are pure linear algebra dressed as family
> stories.

---

## 1. Averages — the core

```
Average = Sum of observations / Number of observations
Sum = Average × Count                    ⭐ this rearrangement is what you actually use
```

| Situation | Formula |
|---|---|
| New average when one item is added | `(old sum + new item)/(n + 1)` |
| **Replacement** — one person of weight `a` replaced by one of weight `b` | Average changes by `(b − a)/n` ⭐⭐ |
| **Weighted average** | `(n₁a₁ + n₂a₂)/(n₁ + n₂)` |
| Average of first `n` natural numbers | `(n+1)/2` |
| Average of first `n` odd numbers | `n` |
| Average of first `n` even numbers | `n + 1` |
| Average of squares of first `n` naturals | `(n+1)(2n+1)/6` |
| Average of consecutive numbers / an AP | `(first + last)/2` ⭐ |
| Average speed | See `Time_Speed_Distance.md` — **not** the average of the speeds |

### The deviation (assumed mean) method ⭐⭐⭐

Instead of adding large numbers, subtract a convenient base from each and average the deviations.

**Example.** Average of 342, 347, 351, 338, 352.
```
Assume a mean of 345.
Deviations: −3, +2, +6, −7, +7  → sum = +5
Average = 345 + 5/5 = **346**
```
That is far faster and far less error-prone than summing 1730 and dividing.

### The "average changes by" shortcut ⭐⭐⭐
```
If adding a new item changes the average by d, then:
     new item = old average + (n + 1) × d
If replacing an item changes the average by d over n items:
     difference between the two items = n × d
```

---

## 2. Age problems — the method

```
1. Let the PRESENT age be the variable (x), never the past or future age.
2. "5 years ago" → (x − 5).  "In 5 years" → (x + 5).
3. Write one equation per sentence of the problem.
4. ⚠️ When applying a ratio at two different times, remember BOTH people age by the SAME amount.
```

**Standard patterns:**

| Statement | Equation |
|---|---|
| A is twice as old as B | `A = 2B` |
| A is 5 years older than B | `A = B + 5` |
| 5 years ago A was thrice B | `A − 5 = 3(B − 5)` |
| The ratio of ages is `a:b` now and `c:d` in `t` years | `(ax + t)/(bx + t) = c/d` ⭐ |
| Average age of a group increases when someone joins | Use the deviation shortcut above |
| Sum of ages of a family is `S`; `n` years later | `S + n × (number of members)` ⚠️ |

⚠️ **The family-age trap:** if the average age of a family of 5 is 20 now, then after 4 years the
average is 24 *only if the family still has 5 members*. If a baby is born, recompute from the sum.

---

## 3. Shortcuts and traps

```
⭐ For consecutive numbers, the average is the middle term (odd count) or the mean of the two
  middle terms (even count). You never need to add them.
⭐ In a "wrong reading corrected" problem, the change in the total is (correct − wrong), and the
  change in the average is that divided by n.
⚠️ Average of averages is NOT the overall average unless the group sizes are equal.
   Use the weighted average.
⭐ Alligation (from Ratio_Proportion_and_Mixtures.md) is the fastest tool for two-group averages.
⚠️ "Excluding the highest and lowest" changes n by 2, not by 1.
```

---

## 4. Fully solved examples

### Example 1 — The replacement shortcut ⭐⭐
**The average weight of 8 people increases by 2.5 kg when a new person replaces one of them
weighing 65 kg. What is the weight of the new person?**

```
Total increase = 8 × 2.5 = 20 kg
This entire increase is due to the swap.
New person = 65 + 20 = **85 kg**
```

**Longhand check:** old sum `= 8w`; new sum `= 8w − 65 + x`; new average `= w + 2.5`
⇒ `8w − 65 + x = 8w + 20` ⇒ `x = 85` ✓

> **The shortcut generalises:** `difference between the two people = n × change in average`.
> Memorise this; it converts a three-line algebra problem into one multiplication.

---

### Example 2 — Corrected wrong reading
**The average of 50 numbers was found to be 38. Later it was discovered that two numbers, 48 and
55, were misread as 84 and 25. Find the correct average.**

```
Wrong total   = 50 × 38 = 1900
Correction    = (48 + 55) − (84 + 25) = 103 − 109 = −6
Correct total = 1900 − 6 = 1894
Correct average = 1894/50 = **37.88**
```
⚠️ Work with the *net* correction, not each number separately — and mind the sign.

---

### Example 3 — Weighted average with three groups ⭐
**A class has 20 boys with an average height of 160 cm and 30 girls with an average height of
150 cm. Ten more students join with an average height of 165 cm. Find the new class average.**

```
Boys   : 20 × 160 = 3200
Girls  : 30 × 150 = 4500
New    : 10 × 165 = 1650
Total sum   = 3200 + 4500 + 1650 = 9350
Total count = 20 + 30 + 10 = 60
Average = 9350/60 = **155.83 cm**
```
⚠️ The naive "average of 160, 150, 165 = 158.33" is wrong because the groups differ in size.

---

### Example 4 — Age ratio at two times ⭐⭐
**The ratio of the present ages of A and B is 4 : 5. Eight years from now the ratio will be 6 : 7.
Find their present ages.**

```
Let present ages be 4x and 5x.
(4x + 8)/(5x + 8) = 6/7

7(4x + 8) = 6(5x + 8)
28x + 56  = 30x + 48
8         = 2x
x         = 4

A = 4x = **16 years**,   B = 5x = **20 years**
```
**Check:** in 8 years they are 24 and 28 → `24:28 = 6:7` ✓

---

### Example 5 — Three-person age chain
**A father is three times as old as his son. Five years ago, he was four times as old. Find their
present ages and after how many years the father will be twice as old.**

```
Let the son be s, the father 3s.
(3s − 5) = 4(s − 5)
3s − 5   = 4s − 20
15       = s

Son = **15**, Father = **45**

Father twice the son after t years:
45 + t = 2(15 + t)
45 + t = 30 + 2t
t = **15 years**
```
**Check:** in 15 years they will be 60 and 30 ✓

---

### Example 6 — Average with an excluded extreme
**The average of 11 numbers is 60. If the average of the first 6 is 58 and that of the last 6 is
63, find the 6th number.**

```
Total of 11          = 11 × 60 = 660
Total of first 6     =  6 × 58 = 348
Total of last 6      =  6 × 63 = 378
Sum of both groups   = 348 + 378 = 726
```
The 6th number is counted in **both** groups, so:
```
726 − 660 = **66** → the 6th number is 66
```
⭐ This overlap trick is the whole question. Whenever two overlapping groups are given, the
overlap equals (sum of the group totals) − (overall total).

---

### Example 7 — Cricket-average style ⭐
**A batsman's average after 16 innings is 36. In the 17th innings he scores 85. What is his new
average?**

```
Old total = 16 × 36 = 576
New total = 576 + 85 = 661
New average = 661/17 = **38.88**
```

**Deviation shortcut ⭐:** the new score exceeds the old average by `85 − 36 = 49`, spread over 17
innings: `36 + 49/17 = 36 + 2.88 = 38.88` ✓

The common reverse question -- *"how many runs must he score in the 17th innings to raise his
average to 38?"* -- is solved the same way:
```
Required new total = 17 x 38 = 646
Current total      = 16 x 36 = 576
Runs needed        = 646 - 576 = **70**
```
**Deviation view of the same thing:** he must "pay for" the new average of 38 *and* lift each of
the 16 previous innings by 2: `38 + (16 x 2) = 38 + 32 = 70` runs. Both routes agree; use the
total method as the primary and the deviation method as a check.

---

## 5. Practice set

1. Average of the first 20 natural numbers.
2. The average of 5 numbers is 27. If one is excluded the average becomes 25. Find the excluded number.
3. The average age of 30 students is 14. With the teacher included it is 15. Find the teacher's age.
4. The average of 7 consecutive numbers is 20. Find the largest.
5. The average weight of A, B, C is 45 kg. A and B average 40; B and C average 43. Find B.
6. A man's average expenditure for 8 months is ₹1,200 and for the next 4 months is ₹1,500. Yearly
   average?
7. The present ages of A and B are in the ratio 3:5. Ten years ago it was 1:3. Find A's age.
8. The average of 6 numbers is 8. Two more numbers make the average 9. Find the average of the
   two new numbers.
9. A father is 30 years older than his son. In 12 years he will be twice as old. Present ages?
10. The average marks of a class of 40 is 62. Four students with average 30 leave. New average?

<details><summary>Answers</summary>

1. `(20+1)/2 = **10.5**`.
2. `5×27 − 4×25 = 135 − 100 = **35**`.
3. `31×15 − 30×14 = 465 − 420 = **45**`.
4. Average is the middle (4th) term = 20 → the seven are 17…23 → largest **23**.
5. `B = (A+B) + (B+C) − (A+B+C) = 80 + 86 − 135 = **31 kg**`.
6. `(8×1200 + 4×1500)/12 = (9600 + 6000)/12 = **₹1,300**`.
7. `(3x−10)/(5x−10) = 1/3 ⇒ 9x − 30 = 5x − 10 ⇒ x = 5` → A = **15**.
8. `8×9 − 6×8 = 72 − 48 = 24` over 2 numbers → **12**.
9. `s + 12 + 30 = 2(s + 12) ⇒ s = 18` → son **18**, father **48**.
10. `(40×62 − 4×30)/36 = (2480 − 120)/36 = 2360/36 = **65.56**`.
</details>

---

## Recall questions

1. State the replacement shortcut and derive it in one line.
2. Explain the deviation (assumed mean) method with an example.
3. Why is the average of averages usually wrong?
4. Give the overlapping-groups trick from Example 6.
5. In age problems, which age should be the variable, and why?
6. Set up the equation for "ratio `a:b` now, `c:d` in `t` years".
7. What is the average of a set of consecutive numbers, and why do you never need to add them?
