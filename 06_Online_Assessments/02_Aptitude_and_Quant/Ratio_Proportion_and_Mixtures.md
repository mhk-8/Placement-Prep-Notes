
# Ratio, Proportion, Mixtures and Alligation

> **Why it matters.** 2-4 questions directly, plus ratio thinking underpins partnership, mixtures,
> ages and data interpretation. **Alligation in particular is the single biggest time-saver in the
> whole aptitude section** — it turns a two-equation problem into one subtraction.

---

## 1. Ratio basics

```
a : b  means  a/b.  Multiplying or dividing BOTH terms by the same non-zero number
                     leaves the ratio unchanged.
a : b = c : d   ⇔   ad = bc        (cross multiplication)
```

**Working with ratios — the `k` trick ⭐:** whenever a ratio `a : b : c` is given, write the actual
quantities as `ak, bk, ck`. One unknown replaces three.

| Operation | Rule |
|---|---|
| Compounded ratio | `(a:b)` and `(c:d)` → `ac : bd` |
| Duplicate ratio | `a² : b²` |
| Sub-duplicate ratio | `√a : √b` |
| Triplicate ratio | `a³ : b³` |
| Inverse ratio | `b : a` |
| Combining `a:b` and `b:c` | Make `b` common: `a:b = 2:3`, `b:c = 4:5` → `8:12:15` ⭐ |
| Combining three ratios `a:b`, `b:c`, `c:d` | Chain them through the shared terms |

**Combining ratios worked:** `A:B = 2:3` and `B:C = 4:5`.
```
Make B common. LCM(3,4) = 12.
A:B = 2:3 = 8:12       (×4)
B:C = 4:5 = 12:15      (×3)
⇒ A : B : C = 8 : 12 : 15
```

---

## 2. Proportion

```
Continued proportion a : b = b : c   ⇒  b² = ac   (b is the mean proportional)
Third proportional to a and b        ⇒  c = b²/a
Fourth proportional to a, b, c       ⇒  d = bc/a
```

**Direct proportion:** `y = kx` — as one rises, the other rises.
**Inverse proportion:** `y = k/x` — as one rises, the other falls (speed–time, workers–days).

**Componendo and dividendo ⭐** (saves real time on `(a+b)/(a−b)` questions):
```
If a/b = c/d  then  (a+b)/(a−b) = (c+d)/(c−d)
```

---

## 3. Mixtures and alligation ⭐⭐⭐

### The alligation rule
When two ingredients at prices/concentrations `c` (cheaper) and `d` (dearer) are mixed to give mean
value `m`:

```
        Cheaper (c)                 Dearer (d)
              \                       /
               \                     /
                \                   /
                 \                 /
                  ---- Mean (m) ----
                 /                 \
                /                   \
          (d − m)                 (m − c)

Quantity of cheaper : Quantity of dearer = (d − m) : (m − c)
```

> **In words: the ratio of quantities is the ratio of the *opposite* distances from the mean.**
> The ingredient further from the mean is used in the smaller quantity.

**Instant example.** Milk at ₹40/L and ₹60/L mixed to sell at ₹45/L.
```
        40 ──────── 45 ──────── 60
distances:  |60−45| = 15    |45−40| = 5
cheaper : dearer = 15 : 5 = **3 : 1**
```
Three litres of the ₹40 milk for every litre of the ₹60 milk. That took five seconds.

### Repeated replacement ⭐⭐⭐
A vessel holds `V` litres of pure liquid. `x` litres are removed and replaced with water. This is
repeated `n` times.

```
Pure liquid remaining after n operations = V (1 − x/V)ⁿ

Ratio of pure liquid to total after n operations = (1 − x/V)ⁿ
```

⚠️ This formula is worth memorising exactly; the question appears in almost every paper in some
form and the manual simulation is slow and error-prone.

---

## 4. Shortcuts and traps

```
⭐ Alligation works for ANY weighted average: price, concentration, speed, percentage, marks,
   interest rate, age. If a question gives you two groups and an overall average, use alligation.
⚠️ Alligation gives the ratio of QUANTITIES, and the distances are taken from the OPPOSITE side.
   Getting this backwards is the classic error — sanity check: the result nearer the mean should
   have the larger quantity.
⭐ For "ratio becomes x:y after adding/removing", set the original as `ak` and `bk` and solve for k.
⚠️ "The ratio of A to B is 3:4" does NOT mean A = 3 and B = 4. Always introduce `k`.
⭐ In a mixture of milk and water, "milk : water = 3 : 2" means milk is 3/5 of the total, not 3/2.
   Read whether a part-to-part or part-to-whole ratio is given.
⭐ When two mixtures are themselves mixed, convert both to a common basis (e.g. % milk) first,
   then apply alligation.
```

---

## 5. Fully solved examples

### Example 1 — Ratio with a change ⭐

**The monthly incomes of A and B are in the ratio 5 : 4, and their expenditures are in the ratio
3 : 2. If each saves Rs 6,000 per month, find A's income.**

Introduce one unknown per ratio — this is the standard move.
```
Income:      A = 5x,  B = 4x
Expenditure: A = 3y,  B = 2y
Savings = income - expenditure, and both save 6000:

  5x - 3y = 6000     ... (1)
  4x - 2y = 6000     ... (2)
```
From (2): `2x - y = 3000`, so `y = 2x - 3000`. Substitute into (1):
```
5x - 3(2x - 3000) = 6000
5x - 6x + 9000     = 6000
-x = -3000
x = 3000
```
**A's income = 5x = Rs 15,000** (B's = Rs 12,000; expenditures Rs 9,000 and Rs 6,000; both save
Rs 6,000 ✓).

> **The pattern to internalise:** two independent ratios need two independent unknowns (`x` and
> `y`). Trying to force both onto a single `k` is the most common mistake in this family.

---

### Example 2 — Classic alligation on price ⭐
**In what ratio must a grocer mix two varieties of pulses costing ₹15 and ₹20 per kg to get a
mixture worth ₹16.50 per kg?**

```
        15 ─────── 16.50 ─────── 20
distances:  |20 − 16.5| = 3.5      |16.5 − 15| = 1.5
ratio (₹15 : ₹20) = 3.5 : 1.5 = **7 : 3**
```
**Verification:** `(7 × 15 + 3 × 20)/10 = (105 + 60)/10 = 16.5` ✓

---

### Example 3 — Repeated replacement ⭐⭐
**A vessel contains 80 litres of pure milk. 8 litres are drawn out and replaced with water. This
operation is performed twice more. How much pure milk remains, and what is the final ratio of milk
to water?**

```
V = 80, x = 8, n = 3
Milk remaining = 80 (1 − 8/80)³ = 80 (1 − 0.1)³ = 80 × 0.9³
0.9³ = 0.729
Milk = 80 × 0.729 = **58.32 litres**
Water = 80 − 58.32 = 21.68 litres
Ratio milk : water = 58.32 : 21.68 ≈ **729 : 271**
```
(Exactly: `729/1000 : 271/1000` — since `0.9³ = 729/1000`.)

⚠️ A frequent wrong method is subtracting `3 × 8 = 24` litres. That ignores the fact that later
draws remove water as well as milk.

---

### Example 4 — Mixing two mixtures ⭐⭐
**Vessel A contains milk and water in the ratio 4 : 1 and vessel B in the ratio 2 : 3. In what
ratio must their contents be mixed so that the resulting mixture has milk and water in the ratio
3 : 2?**

**Step 1 — convert everything to "fraction of milk".**
```
A: milk = 4/5 = 0.8
B: milk = 2/5 = 0.4
Target: milk = 3/5 = 0.6
```

**Step 2 — alligation on the milk fraction.**
```
        0.4 ──────── 0.6 ──────── 0.8
              (B)          (A)
distances:  |0.8 − 0.6| = 0.2     |0.6 − 0.4| = 0.2
B : A = 0.2 : 0.2 = 1 : 1
⇒ A : B = **1 : 1**
```
**Check:** equal volumes, say 5 L each → milk `= 4 + 2 = 6`, water `= 1 + 3 = 4` → `6:4 = 3:2` ✓

---

### Example 5 — Alligation on a non-price quantity
**A shopkeeper has 100 kg of sugar, part of which he sells at 10% profit and the rest at 20%
profit. He gains 14% on the whole. Find the quantity sold at 10% profit.**

```
        10% ──────── 14% ──────── 20%
distances:  |20 − 14| = 6      |14 − 10| = 4
(10% lot) : (20% lot) = 6 : 4 = 3 : 2
Total 5 parts = 100 kg  ⇒  1 part = 20 kg
Quantity at 10% profit = 3 × 20 = **60 kg**
```

---

### Example 6 — Three-way ratio chain
**If `A : B = 3 : 4`, `B : C = 6 : 5` and `C : D = 10 : 9`, find `A : D`.**

```
A : B = 3 : 4
B : C = 6 : 5      → make B common: LCM(4, 6) = 12
   A : B = 9 : 12  (×3)
   B : C = 12 : 10 (×2)
   ⇒ A : B : C = 9 : 12 : 10
C : D = 10 : 9     → C is already 10, so append directly
   ⇒ A : B : C : D = 9 : 12 : 10 : 9

A : D = **9 : 9 = 1 : 1**
```

**Shortcut ⭐:** multiply the chain: `A/D = (3/4)(6/5)(10/9) = 180/180 = 1`.

---

### Example 7 — Ratio after addition
**The ratio of milk to water in a 60-litre mixture is 7 : 3. How much water must be added to make
the ratio 3 : 2?**

```
Milk  = 7/10 × 60 = 42 L        Water = 3/10 × 60 = 18 L
Milk is unchanged; let `x` litres of water be added.
42 / (18 + x) = 3/2
84 = 3(18 + x) = 54 + 3x
3x = 30
x = **10 litres**
```
Check: 42 : 28 = 3 : 2 ✓

---

## 6. Practice set

1. If `a : b = 2 : 3` and `b : c = 4 : 5`, find `a : c`.
2. Divide ₹1500 among A, B, C in the ratio 2 : 3 : 5.
3. In what ratio must rice at ₹32/kg be mixed with rice at ₹40/kg to get a mixture at ₹35/kg?
4. 40 L of a 20%-acid solution: how much water to add to make it 16%?
5. A 45 L mixture has milk and water 4 : 1. How much water to make it 3 : 2?
6. From 100 L of pure milk, 10 L are replaced with water twice. Milk remaining?
7. Two numbers are in the ratio 5 : 7. If 6 is added to each, the ratio becomes 7 : 9. Find them.
8. Mean proportional between 9 and 25.
9. A mixture of 30 L has spirit and water 2 : 3. How much spirit to add to make it 1 : 1?
10. Average age of a class of 30 is 15. Two groups: 20 boys averaging 16 and the rest girls. Girls'
    average?

<details><summary>Answers</summary>

1. `a:c = (2/3)(4/5) = 8/15` → **8 : 15**.
2. Total 10 parts → **₹300, ₹450, ₹750**.
3. Distances `|40−35| = 5` and `|35−32| = 3` → **5 : 3**.
4. Acid `= 8 L`. `8/(40+x) = 0.16 ⇒ 40 + x = 50 ⇒ x = **10 L**`.
5. Milk `= 36`, water `= 9`. `36/(9+x) = 3/2 ⇒ 9 + x = 24 ⇒ x = **15 L**`.
6. `100 × (0.9)² = **81 L**`.
7. `(5k+6)/(7k+6) = 7/9 ⇒ 45k + 54 = 49k + 42 ⇒ k = 3` → **15 and 21**.
8. `√(9×25) = **15**`.
9. Spirit `= 12`, water `= 18`. Need spirit `= 18` → add **6 L**.
10. `30×15 = 450`; boys `20×16 = 320`; girls `= 130` over 10 girls → **13**.
</details>

---

## Recall questions

1. State the alligation rule and explain why the distances are taken from the opposite side.
2. Give the repeated-replacement formula and say why subtracting `nx` is wrong.
3. How do you combine `A:B` and `B:C` into `A:B:C`?
4. Define mean, third and fourth proportional.
5. What is componendo and dividendo, and when does it save time?
6. When mixing two mixtures, what is the first step?
7. Give three non-price quantities alligation applies to.
