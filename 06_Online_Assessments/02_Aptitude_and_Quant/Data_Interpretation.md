
# Data Interpretation

> **Why it matters.** A DI set gives **3-5 questions from one block of context**. That is the best
> marks-per-minute ratio in the entire aptitude paper — *if* your arithmetic is fast and you read
> the chart correctly. Most candidates lose DI marks to misreading, not to inability.

---

## 1. The five formats you will meet

```
1. TABLE            rows × columns of raw numbers            (most common, most arithmetic)
2. BAR CHART        simple, grouped, or stacked
3. LINE GRAPH       trends over time; sometimes two y-axes ⚠️
4. PIE CHART        percentages or degrees of a whole  ⭐ 100% = 360°
5. CASELET          a paragraph of data with no chart at all — you build the table yourself
```

Mixed/combined sets (a table plus a pie, or two charts that must be read together) are the harder
variant and are increasingly common.

---

## 2. The reading protocol ⭐⭐⭐

Before attempting **any** question in a set, spend 30-45 seconds on this:

```
□ What is the UNIT?  (₹, ₹ lakh, ₹ crore, thousands, %, tonnes)   ⚠️ #1 source of errors
□ What does each AXIS / column / segment represent?
□ Is the data ABSOLUTE or PERCENTAGE?  Percentage OF WHAT?
□ Are there TWO scales (left and right y-axis)?                    ⚠️ #2 source of errors
□ Is there a footnote?  ("*excluding exports", "figures rounded")
□ Do the percentages sum to 100?  If not, there is an "others" category.
□ What is the TOTAL, if given — you will need it repeatedly.
```

Then, and only then, read the questions.

> **The single biggest DI mistake is computing a correct number for the wrong quantity.** The
> arithmetic is rarely the problem.

---

## 3. Pie charts

```
100% = 360°           1% = 3.6°           1° = 5/18 %
Value of a segment = (segment angle/360) × Total
                   = (segment %/100) × Total
```

**Memorise the common angles:**

| % | Degrees | | % | Degrees |
|---|---|---|---|---|
| 5 | 18° | | 25 | 90° |
| 10 | 36° | | 30 | 108° |
| 12.5 | 45° | | 33⅓ | 120° |
| 15 | 54° | | 40 | 144° |
| 20 | 72° | | 50 | 180° |

---

## 4. The formulas DI questions actually use

| Question type | Formula |
|---|---|
| Percentage of total | `part/total × 100` |
| Percentage increase from A to B | `(B − A)/A × 100` ⚠️ base is **A** |
| Ratio between two values | direct division; simplify |
| Average over a period | `sum/count` |
| Contribution of one category | `category/total × 100` |
| **CAGR** (compound growth) | `[(Final/Initial)^(1/n) − 1] × 100` |
| Approximate growth over n years at r% | `≈ n·r + (combinations of cross terms)` — use the successive-change formula from `Percentages.md` |
| "By what percent is A more than B" | `(A − B)/B × 100` ⚠️ base is **B** |
| Combined average of two groups | weighted average / alligation |

---

## 5. Speed techniques ⭐⭐⭐

```
1. APPROXIMATE FIRST. Options in DI are usually far apart. 4783/619 ≈ 4800/600 = 8.
   Compute exactly only when two options are within ~5% of each other.

2. USE FRACTIONS, NOT DECIMALS.  "37.5% of 4800" → 3/8 × 4800 = 1800, instantly.
   (The fraction-percentage table in 00-Core_Arithmetic_Toolkit.md is the tool here.)

3. COMPARE RATIOS WITHOUT DIVIDING. To compare a/b and c/d, cross-multiply: a·d vs c·b.
   Comparing 47/83 and 52/91 → 47×91 = 4277 vs 52×83 = 4316 → the second is larger.

4. DON'T COMPUTE WHAT YOU DON'T NEED. For "which year had the highest growth RATE", you often
   only need to eyeball the ratios, not compute all of them.

5. REUSE INTERMEDIATE RESULTS. The total you computed for Q1 is usually needed again in Q3.
   Write it down in the margin. ⭐

6. FOR "WHICH IS GREATEST" QUESTIONS, scan for the obvious outliers first and only compute the
   two or three plausible candidates.

7. PERCENTAGE POINT DIFFERENCES: if a value goes from 20% to 25% of the total, that is a 5
   percentage-point rise but a 25% relative increase. Read which is asked. ⚠️
```

---

## 6. Fully solved set 1 — Table

**The table shows the number of units (in thousands) sold by a company in five cities over three
years.**

| City | 2023 | 2024 | 2025 |
|---|---|---|---|
| Delhi | 120 | 150 | 180 |
| Mumbai | 200 | 180 | 240 |
| Chennai | 80 | 100 | 130 |
| Kolkata | 150 | 165 | 155 |
| Bengaluru | 90 | 135 | 195 |
| **Total** | **640** | **730** | **900** |

**Q1. What is the percentage increase in total sales from 2023 to 2025?**
```
(900 − 640)/640 × 100 = 260/640 × 100
260/640 = 13/32 = 0.40625
= **40.625% ≈ 40.6%**
```

**Q2. Which city showed the highest percentage growth from 2023 to 2025?**
```
Delhi     : 180/120 = 1.50  → +50%
Mumbai    : 240/200 = 1.20  → +20%
Chennai   : 130/80  = 1.625 → +62.5%
Kolkata   : 155/150 ≈ 1.033 → +3.3%
Bengaluru : 195/90  ≈ 2.167 → +116.7%     ← highest

**Bengaluru**, at about +117%.
```
⭐ Note that Mumbai has the largest *absolute* increase (+40) but a small *percentage* increase.
Questions deliberately offer both as options.

**Q3. In 2024, Chennai's sales are what percentage of Mumbai's?**
```
100/180 × 100 = 10000/180 = **55.56%**
```

**Q4. What is the average annual sales of Kolkata over the three years?**
```
(150 + 165 + 155)/3 = 470/3 = **156.67 thousand units**
```

**Q5. If the 2026 total is expected to grow at the same percentage rate as 2024→2025, what will it
be?**
```
2024 → 2025 growth = (900 − 730)/730 = 170/730 ≈ 23.29%
2026 ≈ 900 × 1.2329 = **1109.6 thousand ≈ 1.11 million units**
```

---

## 7. Fully solved set 2 — Pie chart

**A family's monthly budget of ₹60,000 is distributed as follows:**

| Category | Angle |
|---|---|
| Food | 108° |
| Rent | 90° |
| Education | 72° |
| Transport | 36° |
| Savings | 54° |
| **Total** | **360°** |

**Q1. How much is spent on food?**
```
108/360 = 3/10 = 30%
30% of 60,000 = **₹18,000**
```

**Q2. By what percentage does spending on rent exceed spending on savings?**
```
Rent    = 90/360 = 25%   → ₹15,000
Savings = 54/360 = 15%   → ₹9,000
Excess  = (15000 − 9000)/9000 × 100 = 6000/9000 × 100 = **66.67%**
```
⚠️ Base is savings (the second quantity). Answering `(6000/15000) = 40%` is the planted trap.

**Q3. If the family's income rises to ₹75,000 and all proportions stay the same, what is the new
transport spend?**
```
Transport = 36/360 = 10%
10% of 75,000 = **₹7,500**
```

**Q4. What is the central angle of the combined Education and Transport categories, and what
fraction of the total do they represent?**
```
72° + 36° = **108°**  →  108/360 = 3/10 = **30%** = ₹18,000
```

**Q5. If food spending is reduced by 20% and the saving is added to Savings, what is the new
Savings amount and its new central angle?**
```
Food reduction = 20% of 18,000 = ₹3,600
New savings = 9,000 + 3,600 = **₹12,600**
New angle = (12600/60000) × 360 = 0.21 × 360 = **75.6°**
```

---

## 8. Fully solved set 3 — Caselet ⭐

**In a college of 1,200 students, 55% are boys. 40% of the boys and 25% of the girls opted for the
Computer Science elective. Of those who opted for Computer Science, 30% also opted for the AI
minor, of whom two-thirds are boys.**

**Step 0 — build the table.** Never answer a caselet without tabulating first.
```
Total = 1200
Boys  = 55% × 1200 = 660
Girls = 1200 − 660 = 540

CS (boys)  = 40% of 660 = 264
CS (girls) = 25% of 540 = 135
CS total   = 264 + 135 = 399

AI minor total = 30% of 399 = 119.7 ≈ 120      (the data intends a round number)
AI boys  = ⅔ × 120 = 80
AI girls = 120 − 80 = 40
```

| | Boys | Girls | Total |
|---|---|---|---|
| Total students | 660 | 540 | 1200 |
| Opted CS | 264 | 135 | 399 |
| CS + AI minor | 80 | 40 | 120 |

**Q1. What percentage of the college opted for Computer Science?**
```
399/1200 × 100 = **33.25%**
```

**Q2. What fraction of the girls who took CS also took the AI minor?**
```
40/135 = 8/27 ≈ **29.6%**
```

**Q3. What is the ratio of boys to girls among those who took CS but *not* the AI minor?**
```
Boys  : 264 − 80 = 184
Girls : 135 − 40 = 95
Ratio = 184 : 95   (no common factor) → **184 : 95 ≈ 1.94 : 1**
```

**Q4. If 10% of the boys who did not opt for CS later join it, what is the new CS total?**
```
Boys not in CS = 660 − 264 = 396
10% of 396 = 39.6 ≈ 40
New CS total = 399 + 40 = **439**
```

> ⭐ Notice that every question after Step 0 took under 20 seconds. **The table is the work.**

---

## 9. Common DI traps ⚠️

| Trap | How it is planted |
|---|---|
| Unit confusion | Chart in ₹ lakh, question asks in ₹ crore |
| Wrong base for % change | "A is what % more than B" — base is B |
| Percentage vs percentage point | 20% → 25% is 5 points but a 25% rise |
| Two y-axes | Bars on the left scale, the line on the right |
| Absolute vs relative | Largest increase ≠ largest percentage increase |
| "Approximately" | Signals you should estimate, not compute exactly ⭐ |
| Missing category | Percentages sum to 92%; the remainder is "others" |
| Cumulative vs annual | A line showing *total so far*, not yearly figures |
| Averages of percentages | Cannot be averaged unless the bases are equal |

---

## 10. Practice protocol

```
Week 1-2 : one DI set per day, untimed, focusing on the reading protocol and on building tables
Week 3-4 : two sets per day at 6 minutes per 5-question set
Week 5+  : three sets per day at 5 minutes each, mixed formats, with a log of every error
           and its CAUSE (misread / wrong base / arithmetic / ran out of time)
```

Tracking the *cause* of each DI error is what actually fixes it. Use the mistake table in
`../06_Timed_Mock_Logs/Mock_Test_Template.md`.

---

## Recall questions

1. Give the seven items in the pre-reading checklist.
2. Convert 15% and 33⅓% into pie-chart degrees.
3. For "A is what percent more than B", which value is the base?
4. How do you compare two fractions without dividing?
5. Distinguish percentage points from percentage change with an example.
6. Why is the table the whole work in a caselet?
7. Name four ways a DI set plants a trap.
