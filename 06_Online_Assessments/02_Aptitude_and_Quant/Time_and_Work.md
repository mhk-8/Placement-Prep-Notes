
# Time and Work, Pipes and Cisterns, Work-Wages

> **Why it matters.** 2-3 questions per paper, and every one of them is solved by a single idea:
> **convert work into units per day**. Once you do that, the topic becomes addition.

---

## 1. The two methods

### Method A — the unitary/fraction method (standard)
```
If A completes a job in n days, A's one-day work = 1/n.
Combined one-day work = 1/a + 1/b + …
Time together = 1 / (combined one-day work)
```

### Method B — the LCM method ⭐⭐⭐ (much faster, use this)
```
Let total work = LCM of the individual times.
Then each person's efficiency = total work / their time (a whole number, no fractions).
```

**Why it is better:** it removes every fraction from the problem.

**Example.** A does a job in 12 days, B in 15 days, C in 20 days. How long together?
```
Total work = LCM(12, 15, 20) = 60 units
A's rate = 60/12 = 5 units/day
B's rate = 60/15 = 4 units/day
C's rate = 60/20 = 3 units/day
Together = 12 units/day
Time = 60/12 = **5 days**
```
Compare with the fraction method: `1/12 + 1/15 + 1/20 = 5/60 + 4/60 + 3/60 = 12/60 = 1/5` → 5 days.
Same answer, but the LCM method kept every number an integer.

---

## 2. Formula table

| Situation | Formula |
|---|---|
| A alone in `a` days, B alone in `b` days, together | `ab/(a+b)` days ⭐ |
| Three together | `1/T = 1/a + 1/b + 1/c` |
| A and B together in `t`, A alone in `a`; B alone | `at/(a−t)` days |
| **Efficiency ratio** | If A is `k` times as efficient as B, `time_A : time_B = 1 : k` ⚠️ inverse |
| `M₁D₁H₁/W₁ = M₂D₂H₂/W₂` | Men-Days-Hours-Work chain rule ⭐⭐ |
| Wages | Divided in the ratio of **work done**, i.e. in the ratio of efficiencies (for equal time) |
| Pipe fills in `a` hours | Rate `= +1/a` |
| Pipe empties in `b` hours | Rate `= −1/b` ⚠️ negative |
| Fill and empty together | `1/T = 1/a − 1/b` (net fill if `a < b`) |
| Work left after `d` days | `1 − d × (combined rate)` |
| A alternates with B, day by day | Compute a **2-day block**, then count blocks ⭐ |

---

## 3. Shortcuts and traps

```
⭐ Efficiency is INVERSELY proportional to time.  "A is twice as fast as B" ⇒ A takes half the time.
   Ratio of times 3 : 4  ⇒  ratio of efficiencies 4 : 3.
⚠️ "A is 50% more efficient than B" ⇒ efficiency ratio 3 : 2 ⇒ TIME ratio 2 : 3.
   Do not write time ratio 3 : 2.
⭐ Wages are shared in proportion to WORK DONE, not to days worked.
⚠️ Leaks and outlet pipes are negative. If the net rate is negative, the tank never fills — and
   some questions ask exactly that.
⭐ For alternate-day problems, always build the 2-day (or 3-day) block first and see how many
   whole blocks fit, then handle the remainder day separately. Never simulate day by day.
⭐ MDH/W: any of men, days, hours per day and amount of work can change; the ratio is preserved.
⚠️ Read whether a worker "leaves after n days" or "joins after n days" — the arithmetic differs.
```

---

## 4. Fully solved examples

### Example 1 — Someone leaves partway ⭐
**A can do a piece of work in 20 days and B in 30 days. They start together, but A leaves 5 days
before the work is completed. In how many days was the work finished?**

```
Total work = LCM(20, 30) = 60 units
A's rate = 3 units/day        B's rate = 2 units/day

Let the total duration be D days.
B works all D days:        2D units
A works (D − 5) days:      3(D − 5) units

2D + 3(D − 5) = 60
2D + 3D − 15 = 60
5D = 75
D = **15 days**
```
**Check:** A works 10 days → 30 units; B works 15 days → 30 units; total 60 ✓

---

### Example 2 — Alternate days ⭐⭐
**A can complete a task in 12 days and B in 18 days. They work on alternate days, with A starting.
In how many days is the work completed?**

```
Total work = LCM(12, 18) = 36 units
A = 3 units/day       B = 2 units/day

One 2-day block (A then B) = 3 + 2 = 5 units

How many whole blocks fit in 36?
  7 blocks = 14 days = 35 units.  Remaining = 1 unit.
Day 15 is A's turn; A does 3 units/day, so 1 unit takes 1/3 day.

Total = 14 + 1/3 = **14⅓ days**
```

⚠️ **The order matters.** If **B** had started: 7 blocks = 35 units in 14 days, then day 15 is B's
turn at 2 units/day, so 1 unit takes 1/2 day → 14½ days. Always check who starts.

---

### Example 3 — Efficiency ratio given as a percentage
**A is 40% more efficient than B. Working together they finish a job in 15 days. How long would
each take alone?**

```
Efficiency ratio A : B = 140 : 100 = 7 : 5
Let A = 7k units/day, B = 5k units/day.
Together = 12k units/day for 15 days  ⇒  total work = 180k units

A alone: 180k / 7k = 180/7 = **25 5/7 days**
B alone: 180k / 5k = **36 days**
```
**Check:** `1/(180/7) + 1/36 = 7/180 + 5/180 = 12/180 = 1/15` ✓

---

### Example 4 — Pipes with a leak ⭐
**Two pipes A and B can fill a tank in 12 and 16 hours respectively. A third pipe C can empty the
full tank in 8 hours. All three are opened together. (a) Will the tank fill? (b) If A and B are
opened first and C is opened after 3 hours, when does the tank fill?**

```
Total capacity = LCM(12, 16, 8) = 48 units
A = +4 units/h      B = +3 units/h      C = −6 units/h
```

**(a)** Net rate `= 4 + 3 − 6 = +1 unit/h` → the tank **does fill**, in `48/1 = 48 hours`.

**(b)**
```
First 3 hours: only A and B → 7 units/h × 3 = 21 units filled
Remaining = 48 − 21 = 27 units
After C opens, net rate = +1 unit/h
Additional time = 27/1 = 27 hours
Total = 3 + 27 = **30 hours**
```

⚠️ Note how large the effect of a single leak is. A common trap version makes C faster (say, empty
in 5 hours), giving a **negative** net rate — the correct answer is then "the tank never fills",
which is usually one of the options.

---

### Example 5 — Men-Days-Hours chain rule ⭐
**If 12 men working 8 hours a day can complete a wall 40 m long in 10 days, how many men working 6
hours a day are needed to build a wall 60 m long in 8 days?**

Use `M₁D₁H₁/W₁ = M₂D₂H₂/W₂`:
```
(12 × 10 × 8)/40 = (M₂ × 8 × 6)/60

960/40 = 48M₂/60
24 = 0.8 M₂
M₂ = **30 men**
```

**Sanity check by reasoning:** the wall is 1.5× longer (needs 1.5× the effort), there are fewer
days (10→8, a factor of 10/8 = 1.25 more men) and fewer hours (8→6, a factor of 8/6 = 1.333 more
men). `12 × 1.5 × 1.25 × 1.3333 = 30` ✓

---

### Example 6 — Wages ⭐
**A can do a job in 6 days, B in 8 days. With C's help they finish it in 3 days and are paid ₹3200
in total. Find C's share.**

```
Total work = LCM(6, 8, 3) = 24 units
A = 4 units/day     B = 3 units/day     Together (with C) = 24/3 = 8 units/day
⇒ C = 8 − 4 − 3 = 1 unit/day

Over the 3 days:
  A did 12 units,  B did 9 units,  C did 3 units.     (Total 24 ✓)

Wages split in the ratio of work done = 12 : 9 : 3 = 4 : 3 : 1
C's share = 1/8 × 3200 = **₹400**
```

⚠️ The trap is dividing by days worked (all three worked 3 days, which would give equal shares).
Wages follow **work done**, i.e. efficiency.

---

### Example 7 — The "fraction of work completed" variant
**A, B and C can complete a work in 10, 12 and 15 days. They begin together, but A leaves after 2
days and B leaves 3 days before the work is finished. In how many days is the work completed?**

```
Total work = LCM(10, 12, 15) = 60 units
A = 6/day     B = 5/day     C = 4/day

Let the total duration be D days.
  A works 2 days          → 12 units
  C works all D days      → 4D units
  B works (D − 3) days    → 5(D − 3) units

12 + 4D + 5D − 15 = 60
9D − 3 = 60
9D = 63
D = **7 days**
```
**Check:** A: 12, B: 4 days × 5 = 20, C: 7 × 4 = 28. Total `12 + 20 + 28 = 60` ✓

---

## 5. Practice set

1. A does a job in 15 days, B in 10 days. Together?
2. A and B together take 8 days; A alone takes 12 days. How long does B take alone?
3. 15 men complete a work in 20 days. How many days for 25 men?
4. A pipe fills a tank in 6 h; a leak empties it in 10 h. Time to fill with the leak?
5. A is thrice as efficient as B and together they finish in 9 days. B alone?
6. A tap fills a tank in 4 h and another in 6 h. Both opened for 1 h, then the first is closed.
   Total time to fill?
7. 8 men or 12 women can do a work in 10 days. How long for 4 men and 4 women?
8. A and B work alternately (A first); A takes 9 days, B takes 12 days. Total time?
9. A does 2/5 of a work in 6 days. How long for the remainder at the same rate?
10. Three pipes fill in 10, 15 and 20 h. All opened; after 2 h the first is closed. Total time?

<details><summary>Answers</summary>

1. `15×10/25 = **6 days**`.
2. `at/(a−t) = 12×8/(12−8) = **24 days**`.
3. `15×20/25 = **12 days**`.
4. LCM 30; `+5 − 3 = +2/h` → `30/2 = **15 hours**`.
5. Efficiency `3 : 1`; total work `= 4k × 9 = 36k`; B alone `= 36k/k = **36 days**`.
6. LCM 12; rates 3 and 2. After 1 h: 5 units done, 7 left; second tap alone at 2/h → 3.5 h.
   Total `1 + 3.5 = **4.5 hours**`.
7. `8M = 12W ⇒ M = 1.5W`. Work `= 12W × 10 = 120W-days`. `4M + 4W = 6W + 4W = 10W`.
   Time `= 120/10 = **12 days**`.
8. LCM 36; A = 4, B = 3. Block = 7 units/2 days. 5 blocks = 35 units in 10 days; 1 unit left on
   A's turn → 1/4 day. Total **10¼ days**.
9. 2/5 in 6 days → full in 15 days → remaining 3/5 takes **9 days**.
10. LCM 60; rates 6, 4, 3 = 13/h. After 2 h: 26 units, 34 left; remaining rate `4+3 = 7/h`
    → `34/7 = 4 6/7 h`. Total `**6 6/7 hours**`.
</details>

---

## Recall questions

1. Describe the LCM method and say why it beats the fraction method.
2. If A is 25% more efficient than B, what is the ratio of their times?
3. In what ratio are wages divided, and what is the common error?
4. How do you handle an outlet pipe or a leak?
5. Give the method for alternate-day problems and explain why the starting worker matters.
6. State the MDH/W chain rule and apply it to a doubled workload.
7. A and B together take `t`, A alone takes `a` — give B's time.
