
# Aptitude Formulas and Shortcuts — One Pager

> **Use:** the 10 minutes before an aptitude OA. Shortcuts only — the full theory and worked
> examples live in `../06_Online_Assessments/02_Aptitude_and_Quant/`. ⭐
> **Target:** 45-60 seconds per question. Anything slower, mark and move on.

---

## 1. Percentages ⭐⭐

```
x% of y = xy/100                       % change = (new − old)/old × 100
Increase by a%, then decrease by a%  → NET LOSS of a²/100 %   ⭐ always a loss
Successive a% then b%                → a + b + ab/100  (signs included)
A is x% more than B  → B is  100x/(100+x) % less than A  ⭐ the asymmetry question
A is x% less than B  → B is  100x/(100−x) % more than A
To keep expenditure fixed when price rises x% → cut consumption by 100x/(100+x) %
```

| Fraction | % | Fraction | % |
|---|---|---|---|
| 1/2 | 50 | 1/8 | 12.5 |
| 1/3 | 33⅓ | 1/9 | 11⅑ |
| 1/4 | 25 | 1/11 | 9¹/₁₁ |
| 1/5 | 20 | 1/12 | 8⅓ |
| 1/6 | 16⅔ | 1/15 | 6⅔ |
| 1/7 | 14²/₇ | 1/16 | 6.25 |

> ⭐ **Memorise this table.** "Increase by 16⅔%" means "multiply by 7/6" — that is one mental
> step instead of three.

---

## 2. Profit, loss and discount ⭐⭐

```
Profit% = (SP − CP)/CP × 100            Loss% = (CP − SP)/CP × 100
SP = CP(100 + P%)/100                   CP = 100·SP/(100 + P%)
Discount% = (MP − SP)/MP × 100          SP = MP(100 − D%)/100

Two successive discounts a, b   → single equivalent = a + b − ab/100
Sell two items at the same SP, one at +x% and one at −x% → NET LOSS = x²/100 %  ⭐ always a loss
Dishonest shopkeeper using a false weight:
      gain% = (true − false)/false × 100           e.g. 1000 g claimed, 900 g given → 100/9 %
Marked up m% then discounted d% → net = m − d − md/100
Buy x get y free → discount = y/(x+y) × 100 %
```

---

## 3. Simple and compound interest ⭐

```
SI  = PRT/100                               A = P + SI
CI  : A = P(1 + r/100)^n                    CI = A − P
Half-yearly: rate r/2, periods 2n           Quarterly: r/4, 4n
CI − SI for 2 years = P(r/100)²             ⭐ appears constantly
CI − SI for 3 years = P(r/100)²·(300 + r)/100
Doubling time: Rule of 72 → years ≈ 72/r    (and exactly: n = log2 / log(1+r/100))
Equal instalments: P = X/(1+r/100) + X/(1+r/100)² + …
Population/depreciation growth → same CI formula, negative r for decline
```

---

## 4. Ratio, proportion, mixtures ⭐

```
a:b = c:d  ⇔  ad = bc.    If a:b and b:c given → a:b:c by scaling the common term.
Divide N in a:b:c → each gets a/(a+b+c)·N
ALLIGATION ⭐ : (cheaper qty)/(dearer qty) = (dearer price − mean)/(mean − cheaper price)
Replacement: after n replacements of volume x from a vessel of V,
      pure remaining = V(1 − x/V)^n                       ⭐ the standard milk-and-water formula
```

---

## 5. Averages and ages ⭐

```
Average = sum/count.     Sum = average × count
New average when one value v is replaced by w: shifts by (w − v)/n
If average of n numbers is A and one is removed, remaining average = (nA − x)/(n − 1)
First n naturals: average = (n+1)/2;  sum = n(n+1)/2
Sum of squares = n(n+1)(2n+1)/6 ;  sum of cubes = [n(n+1)/2]²
Weighted average = Σwᵢxᵢ / Σwᵢ   ⭐ most "average" traps are really weighted averages
```

---

## 6. Time, speed, distance ⭐⭐

```
D = S × T.      km/h → m/s : × 5/18.      m/s → km/h : × 18/5   ⭐
Constant distance → S ∝ 1/T, so S₁/S₂ = T₂/T₁
AVERAGE SPEED for equal distances at a and b = 2ab/(a+b)   ⭐ harmonic mean, NOT (a+b)/2
   for three legs: 3abc/(ab+bc+ca)
For equal TIMES at a and b → arithmetic mean (a+b)/2
Relative speed: opposite directions a + b; same direction |a − b|

TRAINS
  crossing a pole   : t = L_train / S
  crossing platform : t = (L_train + L_platform) / S
  two trains crossing: (L₁+L₂)/relative speed

BOATS
  downstream = b + s ;  upstream = b − s
  b = (down + up)/2 ;  s = (down − up)/2   ⭐
```

📐 **The classic late/early setup:** walking at a km/h he is late by t₁, at b km/h early by t₂.
Then distance `D = (a·b/(b−a)) × (t₁ + t₂)` with times in hours.

---

## 7. Time and work ⭐⭐

```
Think in RATES, not days. ⭐ A does 1/a per day.
A and B together: 1/a + 1/b → time = ab/(a+b)
Three together   : abc/(ab+bc+ca)
A and B together in t, A alone in a → B alone = at/(a−t)
M₁D₁H₁/W₁ = M₂D₂H₂/W₂                 ⭐ the universal work-equivalence relation
Efficiency ∝ 1/time. If A is twice as efficient as B, A takes half the time.
Negative work (a leak / pipe draining): subtract its rate
PIPES: fill rates add, empty rates subtract — identical algebra to work problems
```
> ⭐ **The LCM trick:** instead of fractions, set total work = LCM of the given days. If A takes
> 12 and B takes 18, let work = 36 units: A does 3/day, B does 2/day, together 5/day → 7.2 days.
> This removes almost all fraction arithmetic.

---

## 8. Permutations, combinations, probability ⭐⭐

```
nPr = n!/(n−r)!           nCr = n!/(r!(n−r)!)         nCr = nC(n−r)
nCr + nC(r−1) = (n+1)Cr   Σ nCr over r = 2^n
Arrangements of n with repeats p, q, …  = n!/(p!·q!·…)
Circular arrangements = (n−1)!  ;  if reflections are identical (necklace) = (n−1)!/2  ⭐
At least one = total − none                                              ⭐ use this constantly
Objects together → treat the block as one item: (n−k+1)! × k!
No two together → arrange the rest, then insert into gaps: (n−k)! × C(n−k+1, k)

P(E) = favourable/total.        P(A∪B) = P(A) + P(B) − P(A∩B)
Independent: P(A∩B) = P(A)P(B).  Mutually exclusive: P(A∩B) = 0
P(A|B) = P(A∩B)/P(B)
BAYES: P(A|B) = P(B|A)P(A)/P(B)   ⭐ also an ML-interview formula
Binomial: P(k of n) = nCk p^k (1−p)^(n−k);  mean np, variance np(1−p)
Expected value = Σ xᵢ·pᵢ        (linearity of expectation solves most "expected count" problems ⭐)
```

```
Dice (2): 36 outcomes. Sum 7 is most likely (6 ways).
Cards: 52 = 4 suits × 13. 26 red, 26 black, 12 face cards, 4 aces.
Birthday-style problems: use 1 − P(all distinct).
```

---

## 9. Numbers, divisibility, HCF/LCM ⭐

```
HCF × LCM = a × b (two numbers only) ⭐
HCF of fractions = HCF(numerators)/LCM(denominators);  LCM of fractions = LCM(num)/HCF(den)
Number of divisors of N = p₁^a·p₂^b… → (a+1)(b+1)…     ⭐
Sum of divisors = Π (pᵢ^(aᵢ+1) − 1)/(pᵢ − 1)
Trailing zeros in n!  = ⌊n/5⌋ + ⌊n/25⌋ + ⌊n/125⌋ + …    ⭐
Units digit cycles with period 4 for most bases → reduce the exponent mod 4
Remainder theorems: Fermat a^(p−1) ≡ 1 (mod p) for prime p ∤ a;  Euler a^φ(n) ≡ 1 (mod n)
φ(n) = n·Π(1 − 1/p)
```

| Divisible by | Test |
|---|---|
| 3 / 9 | Digit sum divisible by 3 / 9 |
| 4 / 8 | Last 2 / 3 digits |
| 6 | By 2 and 3 |
| 7 | Double the last digit, subtract from the rest, repeat |
| 11 | Alternating digit sum divisible by 11 ⭐ |
| 13 | ×4 the last digit, add to the rest |

```
Identities: (a+b)² = a²+2ab+b² · a²−b² = (a−b)(a+b) · a³±b³ = (a±b)(a²∓ab+b²)
a³+b³+c³−3abc = (a+b+c)(a²+b²+c²−ab−bc−ca)  → if a+b+c = 0 then a³+b³+c³ = 3abc ⭐
AP: Tn = a+(n−1)d ; Sn = n/2[2a+(n−1)d] = n(first+last)/2
GP: Tn = ar^(n−1) ; Sn = a(rⁿ−1)/(r−1) ; S∞ = a/(1−r) for |r| < 1
```

---

## 10. Geometry and mensuration ⭐

```
Triangle area = ½·b·h = ½·ab·sinC = √(s(s−a)(s−b)(s−c)), s = (a+b+c)/2
Equilateral: area = (√3/4)a², height = (√3/2)a
Pythagorean triples: 3-4-5 · 5-12-13 · 8-15-17 · 7-24-25 · 9-40-41  ⭐ recognise instantly
Circle: area πr², circumference 2πr; sector area = (θ/360)πr², arc = (θ/360)2πr
Cylinder : V = πr²h, lateral = 2πrh, total = 2πr(r+h)
Cone     : V = ⅓πr²h, slant l = √(r²+h²), lateral = πrl
Sphere   : V = (4/3)πr³, surface = 4πr²;  hemisphere total = 3πr²
Cuboid   : V = lbh, surface = 2(lb+bh+hl), diagonal = √(l²+b²+h²)
Cube     : V = a³, surface = 6a², diagonal = a√3
Similar figures: sides k → areas k², volumes k³  ⭐ the ratio question
```

---

## 11. Data interpretation, clocks, calendars ⭐

```
CLOCKS
  Minute hand 6°/min, hour hand 0.5°/min → relative 5.5°/min  ⭐
  Angle = |30H − 5.5M|
  Hands coincide 11 times in 12 hours, every 65 5/11 minutes
  Right angle 22 times in 12 hours; opposite 11 times
CALENDARS
  Odd days: 1 ordinary year = 1, leap = 2. 100 yr = 5, 200 = 3, 300 = 1, 400 = 0 ⭐
  Leap year: divisible by 4, except centuries not divisible by 400
DATA INTERPRETATION ⭐
  Read the UNITS and the footnotes first. Approximate aggressively: 48.7/197 ≈ 49/200 ≈ 24.5%.
  Percentage-change questions rarely need exact division — compare magnitudes.
```

---

## 12. The 10 minutes before the test ⭐

```
□ Fraction-to-% table (1/2 … 1/16)
□ Squares to 30, cubes to 15, √2 = 1.414, √3 = 1.732, √5 = 2.236
□ a²/100 net-loss rule (both versions)
□ 2ab/(a+b) for equal distances
□ Rates not days for work problems; LCM trick
□ "At least one" = 1 − P(none)
□ Trailing zeros = ⌊n/5⌋ + ⌊n/25⌋ + …
□ 5.5°/min for clocks
□ TIME RULE: 45-60 s per question. Mark and move on. Two skipped questions cost less than one
  five-minute question. ⭐
```

---

## Recall questions
1. A price rises 20% then falls 20%. Net change?
2. Equal distances at 40 and 60 km/h — average speed?
3. 1000 g claimed, 900 g delivered. Gain percent?
4. Trailing zeros in 100!?
5. Angle between the hands at 3:40?
6. A takes 12 days, B takes 18. Together? (Use the LCM trick.)
7. CI − SI over two years at 10% on ₹20,000?
