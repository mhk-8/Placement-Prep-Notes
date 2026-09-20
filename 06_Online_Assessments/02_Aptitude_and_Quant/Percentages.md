
# Percentages

> **Why it matters.** Percentages are the substrate of profit & loss, interest, data
> interpretation and most word problems. If percentage manipulation is slow, the whole paper is
> slow. This is the highest-leverage single topic in the aptitude section.

---

## 1. The core idea

`x% of N = (x/100) × N`. That is the whole definition. Everything else is a manipulation.

**The one habit that changes your speed ⭐⭐⭐:** convert percentages to fractions
(`00-Core_Arithmetic_Toolkit.md` §2) and work multiplicatively.

```
"increase by 25%"   →  × 5/4
"decrease by 20%"   →  × 4/5
"increase by 12.5%" →  × 9/8
"decrease by 16⅔%"  →  × 5/6
"increase by 37.5%" →  × 11/8
```

---

## 2. Formula table

| Concept | Formula |
|---|---|
| Percentage change | `(New − Old)/Old × 100` |
| Increase by x% | `N × (1 + x/100)` |
| Decrease by x% | `N × (1 − x/100)` |
| **Successive changes** a% then b% | `net% = a + b + ab/100` ⭐⭐⭐ |
| A is x% more than B | `A = B(1 + x/100)`; **B is `100x/(100+x)`% less than A** ⚠️ |
| A is x% less than B | `A = B(1 − x/100)`; **B is `100x/(100−x)`% more than A** ⚠️ |
| If price rises x%, consumption must fall by | `100x/(100+x)` % to keep expenditure constant ⭐ |
| If price falls x%, consumption may rise by | `100x/(100−x)` % |
| Net change after +x% and −x% | `−x²/100` % (always a **loss**) ⭐ |
| Population after n years at r% growth | `P(1 + r/100)ⁿ` |
| Value after n years at r% depreciation | `P(1 − r/100)ⁿ` |
| Percentage error | `|approx − exact| / exact × 100` |
| x% of y | `= y% of x` ⭐ (useful: 16% of 25 = 25% of 16 = 4) |

### The reciprocal relationship ⚠️⭐⭐

This is the most commonly mis-answered percentage concept.

```
If A is 25% more than B, then B is NOT 25% less than A.

A = 1.25B  ⇒  B = A/1.25 = 0.8A  ⇒  B is 20% less than A.

General:  x% more  ⇒  reverse is  100x/(100+x) %  less
          x% less  ⇒  reverse is  100x/(100−x) %  more
```

**Memorise this table** — it converts a 60-second computation into instant recall:

| x% more | reverse: % less | | x% less | reverse: % more |
|---|---|---|---|---|
| 100% (double) | 50% | | 50% | 100% |
| 50% | 33⅓% | | 33⅓% | 50% |
| 33⅓% | 25% | | 25% | 33⅓% |
| 25% | 20% | | 20% | 25% |
| 20% | 16⅔% | | 16⅔% | 20% |
| 12.5% | 11⅑% | | 11⅑% | 12.5% |
| 10% | 9¹/₁₁% | | 9¹/₁₁% | 10% |

> **Pattern:** `x% more` with `x = 1/n` as a fraction ⇒ reverse is `1/(n+1)`. So `1/4 more`
> ⇒ `1/5 less`; `1/5 more` ⇒ `1/6 less`. Learn the fraction version, not the decimals.

---

## 3. Shortcuts and traps

```
⭐ Percentage POINTS vs PERCENT. "Interest rose from 5% to 7%" is a rise of 2 percentage points
   but a 40% increase. Read which one is asked.
⭐ Base matters. "20% of the boys" and "20% of the students" have different bases.
⭐ Successive discounts of 20% and 10% are NOT 30%; they are 20+10−2 = 28%.
⭐ To reverse a percentage, divide — never subtract the same percentage.
⚠️ "Increased by 200%" means the result is 3× the original, not 2×.
⚠️ In "by what percent is A greater than B", the base is B (the second one).
⚠️ Percentage of a percentage: 10% of 20% of 500 = 0.1 × 0.2 × 500 = 10.
```

**The "fixed product" shortcut ⭐⭐:** whenever a problem says one quantity goes up and another
must adjust so the *product* stays constant (price × consumption = expenditure; speed × time =
distance; workers × days = work), use:

```
If one factor becomes k times, the other becomes 1/k times.
Price ×6/5 (up 20%) ⇒ consumption ×5/6 ⇒ a fall of 1/6 = 16⅔%
```
This single idea solves price-consumption, speed-time and worker-day problems identically.

---

## 4. Fully solved examples

### Example 1 — Successive changes with a twist
**The price of a commodity increased by 30% in the first year, decreased by 20% in the second and
increased by 10% in the third. What is the net percentage change over three years?**

**Multiplicative method (always safer than adding):**
```
Net factor = 1.30 × 0.80 × 1.10
           = 1.30 × 0.80 = 1.04
           = 1.04 × 1.10 = 1.144
Net change = (1.144 − 1) × 100 = **+14.4%**
```

**Fraction method (faster mentally):**
```
13/10 × 4/5 × 11/10 = (13 × 4 × 11)/(10 × 5 × 10) = 572/500 = 1.144  ✓
```

⚠️ Adding `30 − 20 + 10 = 20%` is wrong. The pairwise formula only works two at a time; for three
or more, multiply.

---

### Example 2 — Price, consumption and expenditure ⭐
**The price of sugar rises by 25%. By what percentage must a family reduce consumption so that the
expenditure on sugar increases by only 5%?**

Let original price = 100, consumption = 100, so expenditure = 10,000.
```
New price = 125
Required new expenditure = 10,000 × 1.05 = 10,500
New consumption = 10,500 / 125 = 84
Reduction = 100 − 84 = 16  ⇒  **16% reduction**
```

**Fraction check:** `New consumption / Old = (1.05)/(1.25) = 105/125 = 21/25 = 0.84` ✓.

> **General formula:** required consumption change = `[(1 + e/100)/(1 + p/100) − 1] × 100`,
> where `p` = price change and `e` = allowed expenditure change.
> Here: `(1.05/1.25 − 1) = −0.16`.

---

### Example 3 — Election problem (a perennial favourite)
**In an election between two candidates, one got 55% of the total valid votes. 20% of the total
votes were invalid. If the total number of votes was 7500, how many valid votes did the other
candidate get?**

```
Total votes         = 7500
Invalid (20%)       = 1500
Valid votes         = 7500 − 1500 = 6000
Winner's share 55%  → loser's share = 45%
Loser's valid votes = 45% of 6000 = 0.45 × 6000 = **2700**
```

⚠️ The trap is taking 45% of 7500 (= 3375). The base is **valid** votes, not total votes. Underline
the base in the question before computing.

---

### Example 4 — The two-stage reduction
**A shopkeeper marks an item 40% above cost and then offers a discount of 25%. He still makes ₹50
profit. Find the cost price.**

Let cost price = `C`.
```
Marked price   = 1.40 C
Selling price  = 1.40 C × 0.75 = 1.05 C
Profit         = 1.05 C − C = 0.05 C
0.05 C = 50  ⇒  C = **₹1000**
```
Selling price = ₹1050, marked price = ₹1400. ✓

> **The pattern:** "marks up by m%, discounts by d%" gives a net factor of
> `(1 + m/100)(1 − d/100)`. Here `1.4 × 0.75 = 1.05`, i.e. a 5% profit. Recognising the net factor
> instantly is the shortcut.

---

### Example 5 — Population with differential growth
**The population of a town is 50,000. Males increase by 10% and females by 15% in a year, making
the total 56,000. Find the original number of males.**

Let males = `M`, females = `50000 − M`.
```
1.10 M + 1.15 (50000 − M) = 56000
1.10 M + 57500 − 1.15 M   = 56000
−0.05 M = −1500
M = **30,000**
```
Check: males → 33,000; females 20,000 → 23,000; total 56,000 ✓.

**Alligation shortcut ⭐** (much faster):
```
Overall increase = 6000/50000 = 12%
              10% ────── 12% ────── 15%
  distance from 12:   |12−15| = 3        |12−10| = 2
  ratio males : females = 3 : 2
  males = 3/5 × 50000 = 30,000  ✓
```

---

### Example 6 — Percentage of a percentage, with a reverse
**A's salary is 20% less than B's. By what percentage is B's salary more than A's? If A's salary is
₹48,000, what is B's?**

```
A = 0.8 B
B/A = 1/0.8 = 1.25  ⇒  B is **25% more** than A
B = 48000 × 1.25 = **₹60,000**
```
(Direct from the reciprocal table: 20% less ⇒ 25% more.)

---

### Example 7 — Compound percentage error
**A student was asked to find 7/12 of a number but mistakenly found 12/7 of it. If his answer was
95 more than the correct answer, find the number.**

```
(12/7) N − (7/12) N = 95
N (144 − 49)/84 = 95
N (95/84) = 95
N = **84**
```
Correct answer = `7/12 × 84 = 49`; wrong answer = `12/7 × 84 = 144`; difference 95 ✓.

---

## 5. Practice set

1. If 30% of a number is 45, what is 80% of it?
2. A number increased by 25% then decreased by 25%. Net change?
3. The price of petrol rises 20%. By what % must consumption drop to keep expenditure the same?
4. In a class, 40% are girls. 25% of the girls and 40% of the boys passed. What % of the class
   passed?
5. A's income is 25% more than B's. B's income is 20% less than C's. A's income is what % of C's?
6. Two successive discounts of 15% and 20% are equivalent to a single discount of what %?
7. A mixture has 20% alcohol. 10 litres of water is added to 40 litres of it. New alcohol %?
8. If the radius of a circle is increased by 20%, by what % does the area increase?
9. 60% of the students passed in Maths, 70% in English, 20% failed in both. What % passed in both?
10. The salary of a person was reduced by 20% and later increased by 20%. Net change?

<details><summary>Answers</summary>

1. Number = `45/0.3 = 150`; 80% = **120**.
2. `1.25 × 0.75 = 0.9375` → **6.25% decrease** (or `−25²/100 = −6.25%`).
3. `100×20/120 = 16⅔%`.
4. Girls pass = `0.25 × 40 = 10%` of class; boys pass = `0.40 × 60 = 24%`. Total **34%**.
5. `B = 0.8C`, `A = 1.25B = 1.25 × 0.8C = C` → **100%**.
6. `0.85 × 0.80 = 0.68` → a single discount of **32%**.
7. Alcohol = `0.2 × 40 = 8 L`; new volume = 50 L → `8/50 = **16%**`.
8. `1.2² = 1.44` → **44%**.
9. Passed at least one = `100 − 20 = 80%`. `60 + 70 − both = 80` → both = **50%**.
10. `0.8 × 1.2 = 0.96` → **4% decrease**.
</details>

---

## Recall questions

1. Give the successive-change formula and say why it cannot be extended to three changes by adding.
2. If A is x% more than B, express by what percent B is less than A.
3. If price rises by p%, by what % must consumption fall to keep expenditure fixed?
4. Explain the "fixed product" idea and name three topics it applies to.
5. What is the net change after a +x% followed by a −x%?
6. In an election question, what is the base for the candidates' percentages?
7. Why is the alligation method faster for two-group percentage problems?
