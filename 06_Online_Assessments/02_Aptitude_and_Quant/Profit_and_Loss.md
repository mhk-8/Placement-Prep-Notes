
# Profit and Loss, Discount and Partnership

> **Why it matters.** 2-4 questions in almost every aptitude paper, and the second-most common
> topic after percentages. Every question is a percentage question wearing a commercial costume.

---

## 1. The vocabulary

```
CP   Cost Price          — what the seller paid
SP   Selling Price       — what the buyer paid
MP   Marked Price        — the list/tag price, before discount
Profit = SP − CP   (when SP > CP)          Loss = CP − SP   (when CP > SP)
Discount = MP − SP
Overheads / repairs are ADDED to CP        ⚠️ "CP" in a question may mean purchase price only
```

**The golden rule ⚠️⭐⭐⭐:**
```
Profit % and Loss % are ALWAYS on Cost Price.
Discount % is ALWAYS on Marked Price.
```
Mixing those two bases is the single largest source of errors in this topic.

---

## 2. Formula table

| Quantity | Formula |
|---|---|
| Profit % | `(SP − CP)/CP × 100` |
| Loss % | `(CP − SP)/CP × 100` |
| SP from CP and profit% | `SP = CP(1 + p/100)` |
| SP from CP and loss% | `SP = CP(1 − l/100)` |
| CP from SP | `CP = SP / (1 ± p/100)` ⚠️ **divide, never subtract the percentage** |
| Discount % | `(MP − SP)/MP × 100` |
| SP after discount | `SP = MP(1 − d/100)` |
| **Combined markup + discount** | `SP = CP(1 + m/100)(1 − d/100)` ⭐ |
| Net profit % from markup m and discount d | `[(1 + m/100)(1 − d/100) − 1] × 100` |
| Successive discounts d₁, d₂ | single equivalent `= d₁ + d₂ − d₁d₂/100` |
| **Two items sold at same SP, one at +x%, one at −x%** | Always a **loss** of `x²/100` % ⭐⭐⭐ |
| Profit% when n articles bought for cost of m | `(m − n)/n × 100` (if m > n) |
| **Dishonest dealer** using a false weight | `Profit% = (True weight − False weight)/False weight × 100` ⭐ |
| Dishonest dealer with markup m% and false weight | `Profit% = [(1+m/100) × (True/False) − 1] × 100` |
| Break-even | `SP = CP` |
| Partnership profit share | In the ratio of `capital × time` ⭐ |

---

## 3. The classics you must recognise instantly

### 3.1 Same SP, equal profit and loss percentages ⭐⭐⭐
**Two articles are sold at the same price; one at a 20% profit and the other at a 20% loss. What is
the overall result?**

Always a **loss** of `x²/100 = 400/100 = 4%`.

**Proof:** let each SP = `S`.
```
CP₁ = S/1.20 = 5S/6          CP₂ = S/0.80 = 5S/4
Total CP = 5S/6 + 5S/4 = (10S + 15S)/12 = 25S/12
Total SP = 2S = 24S/12
Loss = (25S − 24S)/12 = S/12
Loss% = (S/12) / (25S/12) × 100 = 1/25 × 100 = 4%      ∎
```

⚠️ The intuitive answer "no gain no loss" is wrong, and it is wrong *by design* — this is the most
frequently asked trap in the topic. Note it is a loss **regardless of the value of x**.

### 3.2 Markup then discount
Net factor `= (1 + m/100)(1 − d/100)`. If it exceeds 1, it is a profit.

| Markup | Discount | Net |
|---|---|---|
| 20% | 10% | `1.2 × 0.9 = 1.08` → 8% profit |
| 25% | 20% | `1.25 × 0.8 = 1.00` → **break even** ⭐ |
| 40% | 25% | `1.4 × 0.75 = 1.05` → 5% profit |
| 50% | 20% | `1.5 × 0.8 = 1.20` → 20% profit |
| 30% | 30% | `1.3 × 0.7 = 0.91` → **9% loss** ⭐ |

> Note the last row: **equal markup and discount percentages always lose** — same `−x²/100` effect.

### 3.3 Buying and selling different quantities
**"A man buys 12 apples for ₹10 and sells 10 apples for ₹12. Find his profit %."**
```
Take LCM of quantities = 60 apples.
CP of 60 = 5 × 10 = ₹50        (12 apples per ₹10 → 5 lots)
SP of 60 = 6 × 12 = ₹72        (10 apples per ₹12 → 6 lots)
Profit = 22 on 50  ⇒  **44%**
```
⭐ **Always take the LCM of the two quantities.** It removes all fractions.

---

## 4. Fully solved examples

### Example 1 — The dishonest dealer (compound cheating) ⭐⭐
**A shopkeeper marks his goods 20% above cost price and gives a 10% discount. He also uses a weight
of 900 g in place of 1 kg. Find his net profit percentage.**

Let the true cost be ₹1 per gram, so 1000 g costs ₹1000.
```
Step 1 — what he actually gives: 900 g, which cost him ₹900.
Step 2 — what he charges: he charges for 1000 g.
         MP for 1000 g = 1000 × 1.20 = ₹1200
         SP after 10% discount = 1200 × 0.90 = ₹1080
Step 3 — profit = 1080 − 900 = ₹180 on a cost of ₹900
         Profit% = 180/900 × 100 = **20%**
```

**Formula check:** `[(1+m/100)(1−d/100) × (True/False) − 1] × 100`
`= [1.2 × 0.9 × (1000/900) − 1] × 100 = [1.08 × 1.1111 − 1] × 100 = 20%` ✓

---

### Example 2 — Working backwards from two scenarios
**By selling an article for ₹720, a man loses 10%. At what price should he sell it to gain 15%?**

```
Step 1 — find CP.  SP = CP × 0.90  ⇒  CP = 720/0.90 = ₹800
Step 2 — required SP = 800 × 1.15 = **₹920**
```
⚠️ The wrong method is `720 + 10% + 15%`. Always recover CP first by **dividing**.

**Shortcut for this family:** new SP `= old SP × (1 + new%)/(1 + old%)` `= 720 × 1.15/0.90 = 920`.

---

### Example 3 — Profit as a fraction of selling price ⚠️
**A trader's profit is 25% of the selling price. What is his profit percentage on cost?**

```
Let SP = 100.  Profit = 25.  ⇒  CP = 100 − 25 = 75.
Profit% on CP = 25/75 × 100 = **33⅓%**
```
> **General:** if profit is `k%` of SP, then profit on CP is `100k/(100−k)` %. This is the same
> reciprocal relationship as in `Percentages.md`.

---

### Example 4 — Multiple successive transactions
**A sells a bike to B at a 20% profit. B sells it to C at a 10% loss. C sells it to D at a 25%
profit for ₹27,000. What did A pay for it?**

Work backwards from D's price.
```
C's SP = 27000 = C's CP × 1.25  ⇒  C's CP = 21,600  (= B's SP)
B's SP = 21600 = B's CP × 0.90  ⇒  B's CP = 24,000  (= A's SP)
A's SP = 24000 = A's CP × 1.20  ⇒  A's CP = **₹20,000**
```

**Forward check:** `20000 × 1.2 = 24000 × 0.9 = 21600 × 1.25 = 27000` ✓

---

### Example 5 — Partnership with different times ⭐
**A starts a business with ₹40,000. After 3 months B joins with ₹60,000. After a further 3 months
C joins with ₹80,000. At the end of the year, the total profit is ₹1,68,000. Find each share.**

Profit is shared in the ratio of **capital × time**.
```
A: 40,000 × 12 = 4,80,000
B: 60,000 ×  9 = 5,40,000
C: 80,000 ×  6 = 4,80,000

Ratio = 480 : 540 : 480 = 48 : 54 : 48 = 8 : 9 : 8   (divide by 6)
Total parts = 25
A = 8/25 × 168000 = ₹53,760
B = 9/25 × 168000 = ₹60,480
C = 8/25 × 168000 = ₹53,760
```
Check: `53760 + 60480 + 53760 = 168,000` ✓

> **Working vs sleeping partner:** a working partner may take a salary or a fixed % of profit
> *first*; the remainder is then divided in the capital×time ratio. Read carefully for this clause.

---

### Example 6 — Equating two conditions
**A shopkeeper sold an article at a loss of 10%. Had he sold it for ₹150 more, he would have made a
15% profit. Find the cost price.**

```
Let CP = x.
Loss case:   SP₁ = 0.90x
Profit case: SP₂ = 1.15x
SP₂ − SP₁ = 150  ⇒  0.25x = 150  ⇒  x = **₹600**
```
Check: SP₁ = 540 (10% loss ✓); SP₂ = 690 = 540 + 150 and `690/600 = 1.15` ✓

⭐ **Pattern:** whenever two scenarios differ by a fixed amount of money, the difference of the
two percentage factors times CP equals that amount. One line of algebra.

---

### Example 7 — Discount chain with a target
**The marked price of an article is ₹2000. After two successive discounts of 20% and 10%, the
shopkeeper still earns 8% profit. Find the cost price, and the single discount equivalent to the
two.**

```
SP = 2000 × 0.80 × 0.90 = 2000 × 0.72 = ₹1440
CP = 1440 / 1.08 = **₹1333.33**

Single equivalent discount: 1 − 0.72 = 0.28 → **28%**
(Or by formula: 20 + 10 − 200/100 = 28%)
```

---

## 5. Practice set

1. A man buys an article for ₹500 and sells it at a 12% profit. Find SP.
2. An article costing ₹1200 is sold at ₹1020. Find loss %.
3. By selling 33 m of cloth a shopkeeper gains the cost of 11 m. Find gain %.
4. Two articles are sold at ₹1500 each, one at 25% profit and one at 25% loss. Net result?
5. A dealer marks up 50% and allows a 20% discount. Profit %?
6. If the cost price of 15 articles equals the selling price of 12 articles, find the profit %.
7. A trader gives a 10% discount but uses a 950 g weight for 1 kg while marking up 25%. Profit %?
8. A and B invest ₹25,000 and ₹35,000 for the full year; profit ₹24,000. Find B's share.
9. After a 20% discount, an article sells for ₹640. Find the marked price.
10. A sells to B at 25% profit, B sells to C at 20% loss. If C paid ₹1000, what did A pay?

<details><summary>Answers</summary>

1. `500 × 1.12 = **₹560**`.
2. `180/1200 × 100 = **15%**`.
3. Gain on 33 m = cost of 11 m → `11/33 × 100 = **33⅓%**`.
4. Loss of `25²/100 = **6.25%**`.
5. `1.5 × 0.8 = 1.20` → **20% profit**.
6. `(15 − 12)/12 × 100 = **25%**`.
7. `[1.25 × 0.90 × (1000/950) − 1] × 100 = [1.125 × 1.05263 − 1] × 100 = **18.42%**`.
8. Ratio `25 : 35 = 5 : 7`; B = `7/12 × 24000 = **₹14,000**`.
9. `640/0.8 = **₹800**`.
10. `1000 = A_CP × 1.25 × 0.80 = A_CP × 1.00` → **₹1000**.
</details>

---

## Recall questions

1. On what base is profit% computed? On what base is discount%?
2. State and prove the result for two items sold at the same price at ±x%.
3. Give the net factor for markup m% followed by discount d%, and the markup/discount pair that
   breaks even.
4. Give the dishonest-dealer formula including a markup and a discount.
5. In a partnership, in what ratio is profit divided, and what changes for a working partner?
6. Convert "profit is 25% of SP" into a profit percentage on CP.
7. Why must you divide rather than subtract when recovering CP from SP?
