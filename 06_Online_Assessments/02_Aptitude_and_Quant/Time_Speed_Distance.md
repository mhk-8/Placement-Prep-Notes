
# Time, Speed and Distance — including Trains, Boats and Races

> **Why it matters.** 3-5 questions in a typical paper, spread across plain TSD, relative speed,
> trains, boats and streams, and races. The whole topic rests on one equation and one idea
> (relative speed), so the return on study time is very high.

---

## 1. The core relation

```
                    Distance = Speed × Time

Speed  = Distance / Time      Time = Distance / Speed
```

**Unit conversion ⭐⭐⭐ (memorise both directions):**
```
km/h → m/s :  × 5/18
m/s → km/h :  × 18/5
e.g. 72 km/h = 72 × 5/18 = 20 m/s        20 m/s = 20 × 18/5 = 72 km/h
```

**The inverse-proportion idea ⭐⭐⭐**
```
Distance FIXED  ⇒  Speed and Time are inversely proportional.
   Speed ratio a : b   ⇒   Time ratio b : a
```
This one line solves most "if he had walked faster / slower" questions in a single step.

---

## 2. Formula table

| Situation | Formula |
|---|---|
| Average speed (equal **distances**, speeds u and v) | `2uv/(u+v)` — the **harmonic mean** ⭐⭐⭐ |
| Average speed (equal distances, three speeds) | `3uvw/(uv+vw+wu)` |
| Average speed (equal **times**) | `(u+v)/2` — the arithmetic mean ⚠️ |
| Average speed, general | `Total distance / Total time` (always safe) |
| Relative speed — **same direction** | `\|u − v\|` |
| Relative speed — **opposite directions** | `u + v` |
| Train crossing a pole/man | `Time = L_train / speed` |
| Train crossing a platform/bridge | `Time = (L_train + L_platform) / speed` |
| Two trains crossing each other | `Time = (L₁ + L₂) / relative speed` |
| Boat downstream speed | `b + s` (boat speed + stream speed) |
| Boat upstream speed | `b − s` |
| From downstream `d` and upstream `u` speeds | `b = (d+u)/2`, `s = (d−u)/2` ⭐ |
| Round trip in a stream, distance D each way | `T = D/(b+s) + D/(b−s) = 2Db/(b²−s²)` |
| Race: "A beats B by `x` m" | A finishes; B is `x` m behind at that moment |
| Race: "A gives B a start of `x` m" | B runs `(L − x)` while A runs `L` |
| Race: dead heat | Both finish simultaneously |
| Two people meet (start together, opposite ends, distance D) | Meet after `D/(u+v)` |
| After meeting, times to destination | `t₁/t₂ = (v/u)²`… more usefully: `u/v = √(t₂/t₁)` ⭐ |
| Circular track, same direction, meet at start | `LCM(L/u, L/v)` |
| Circular track, first meeting anywhere, opposite | `L/(u+v)` |

---

## 3. Shortcuts and traps

```
⚠️ AVERAGE SPEED IS NOT THE AVERAGE OF SPEEDS when the distances are equal.
   Delhi→Agra at 60, Agra→Delhi at 40  ⇒  average = 2(60)(40)/100 = 48, NOT 50.
   The slower leg takes more time, so it is weighted more heavily.
⭐ Equal distances → harmonic mean.  Equal times → arithmetic mean.  Memorise the pair.
⭐ "Crossing a pole" uses only the train's length; "crossing a platform" adds the platform.
   A "man standing" and a "pole" are the same thing (zero length).
⚠️ A "man walking" has a speed, so use relative speed.
⭐ Convert everything to one unit system at the START. Most errors are unit errors.
⭐ For "if he increases speed by x he saves t minutes" problems, set up the two-equation form:
   D/v − D/(v+x) = t   (with t in hours).
⭐ In races, "A beats B by t seconds" and "A beats B by x metres" are different statements —
   convert one to the other using B's speed.
```

---

## 4. Fully solved examples

### Example 1 — Average speed with three legs
**A man covers the first one-third of a journey at 20 km/h, the next one-third at 30 km/h and the
last one-third at 60 km/h. Find his average speed for the whole journey.**

Let the total distance be `3d` (chosen so each leg is `d` — always pick a convenient total).
```
Time₁ = d/20     Time₂ = d/30     Time₃ = d/60
Total time = d(1/20 + 1/30 + 1/60) = d(3 + 2 + 1)/60 = 6d/60 = d/10
Average speed = 3d / (d/10) = **30 km/h**
```

**Formula check:** `3uvw/(uv+vw+wu) = 3(20)(30)(60)/(600 + 1800 + 1200) = 108000/3600 = 30` ✓

⚠️ The arithmetic mean `(20+30+60)/3 = 36.67` is wrong.

---

### Example 2 — The "increase speed, save time" family ⭐
**A person travelling at 40 km/h reaches his office 15 minutes late. Travelling at 60 km/h he
reaches 10 minutes early. Find the distance to the office and the correct time he should leave.**

Let the distance be `D` km and the scheduled travel time be `T` hours.
```
D/40 = T + 15/60 = T + 1/4
D/60 = T − 10/60 = T − 1/6

Subtract:  D/40 − D/60 = 1/4 + 1/6 = 5/12
D(3 − 2)/120 = 5/12
D/120 = 5/12
D = **50 km**

T = D/40 − 1/4 = 50/40 − 1/4 = 1.25 − 0.25 = **1 hour**
Required speed to be on time = 50/1 = 50 km/h
```

**Shortcut for this family ⭐:**
```
D = (u × v × total time difference) / (v − u)
  = (40 × 60 × (25/60)) / 20 = (2400 × 5/12)/20 = 1000/20 = 50 ✓
```
where the total time difference is `15 + 10 = 25` minutes = 5/12 hour.

---

### Example 3 — Two trains crossing ⭐
**Two trains of lengths 180 m and 220 m run on parallel tracks at 54 km/h and 36 km/h. Find the
time to cross each other (a) when moving in opposite directions, (b) when moving in the same
direction.**

Convert first:
```
54 km/h = 54 × 5/18 = 15 m/s
36 km/h = 36 × 5/18 = 10 m/s
Total length to cover = 180 + 220 = 400 m
```

**(a) Opposite directions:** relative speed `= 15 + 10 = 25 m/s`
```
Time = 400/25 = **16 seconds**
```

**(b) Same direction:** relative speed `= 15 − 10 = 5 m/s`
```
Time = 400/5 = **80 seconds**
```

> Note the factor-of-five difference. This is why the direction clause must be read carefully — it
> changes the answer by a large factor, and both values usually appear among the options.

---

### Example 4 — Train, platform and man combined
**A train crosses a man standing on a platform in 12 seconds and crosses the 280 m platform itself
in 26 seconds. Find the length and speed of the train.**

Let the train's length be `L` m and speed be `v` m/s.
```
Crossing the man (zero length):        L = 12v          … (1)
Crossing the platform:            L + 280 = 26v          … (2)

Substituting (1) into (2):   12v + 280 = 26v
                                   280 = 14v
                                     v = **20 m/s = 72 km/h**
                                     L = 12 × 20 = **240 m**
```

⭐ **Pattern:** subtracting the two equations isolates the platform: `platform = (t₂ − t₁) × v`.
Here `280 = (26 − 12) × v` gives `v = 20` in one step.

---

### Example 5 — Boats and streams, full treatment
**A boat covers 24 km downstream in 2 hours and returns upstream in 3 hours. Find the speed of the
boat in still water, the speed of the stream, and the time for a 60 km round trip.**

```
Downstream speed  d = 24/2 = 12 km/h
Upstream speed    u = 24/3 =  8 km/h

Boat in still water  b = (d + u)/2 = (12 + 8)/2 = **10 km/h**
Stream               s = (d − u)/2 = (12 − 8)/2 = **2 km/h**

Round trip of 60 km each way:
  T = 60/12 + 60/8 = 5 + 7.5 = **12.5 hours**
```

**Sanity check with the formula** `T = 2Db/(b² − s²) = 2(60)(10)/(100 − 4) = 1200/96 = 12.5` ✓

---

### Example 6 — Races with a start ⭐
**In a 1000 m race, A beats B by 100 m, and B beats C by 150 m. By how many metres does A beat C in
the same race?**

```
When A runs 1000, B runs 900.       ⇒ B/A = 900/1000 = 9/10
When B runs 1000, C runs 850.       ⇒ C/B = 850/1000 = 17/20

When A runs 1000, B runs 900.
In that time C runs = 900 × (17/20) = 765 m
⇒ A beats C by 1000 − 765 = **235 m**
```

⚠️ The common wrong answer is `100 + 150 = 250`. The two head-starts are measured against
*different* reference runs, so they must be chained multiplicatively, not added.

---

### Example 7 — Circular track meetings
**A and B run around a circular track of 600 m, starting together from the same point, at 6 m/s and
4 m/s respectively. (a) If they run in opposite directions, when do they first meet? (b) In the
same direction? (c) When do they next meet at the starting point (same direction)?**

```
(a) Opposite: relative speed = 6 + 4 = 10 m/s
    First meeting after 600/10 = **60 s**

(b) Same direction: relative speed = 6 − 4 = 2 m/s
    First meeting after 600/2 = **300 s**

(c) At the STARTING POINT: each must complete whole laps.
    A's lap time = 600/6 = 100 s;  B's lap time = 600/4 = 150 s
    Together at start = LCM(100, 150) = **300 s**
```

⚠️ "First meeting anywhere" and "first meeting at the starting point" are different questions with
different methods (relative speed vs LCM of lap times). Read which is asked.

---

### Example 8 — Meeting and the √ ratio ⭐
**Two people start simultaneously from towns A and B towards each other. After meeting, one takes 4
hours to reach B and the other takes 9 hours to reach A. Find the ratio of their speeds and how
long after starting they met.**

```
Standard result:  u/v = √(t₂/t₁)  where t₁, t₂ are the post-meeting times of the two travellers.
u/v = √(9/4) = 3/2   ⇒  **speeds in the ratio 3 : 2**

Time to meeting t = √(t₁ × t₂) = √(4 × 9) = **6 hours**
```

**Why:** let them meet after time `t`. The first covers `ut` before and `vt`-worth after, etc.;
equating distances gives `ut = v·t₂` and `vt = u·t₁`, so `t² = t₁t₂` and `(u/v)² = t₂/t₁`.

---

## 5. Practice set

1. Convert 90 km/h to m/s and 25 m/s to km/h.
2. A car covers 150 km in 2.5 hours. Find its speed in m/s.
3. A man walks at 5 km/h and misses a train by 7 minutes. At 6 km/h he reaches 5 minutes early.
   Find the distance.
4. Average speed if half the distance is at 40 km/h and half at 60 km/h.
5. A 150 m train at 90 km/h crosses a bridge in 20 s. Find the bridge's length.
6. A boat goes 30 km upstream in 5 h and the same distance downstream in 3 h. Find the stream speed.
7. In a 200 m race A beats B by 20 m. By how much would A beat B in a 500 m race (same speeds)?
8. Two trains 120 m and 180 m long, moving in the same direction at 72 km/h and 54 km/h. Crossing
   time?
9. A and B run on a 400 m track at 5 m/s and 3 m/s in the same direction. When do they first meet?
10. If a train's speed increases by 25%, by what % does the journey time fall?

<details><summary>Answers</summary>

1. `90 × 5/18 = **25 m/s**`; `25 × 18/5 = **90 km/h**`.
2. `150/2.5 = 60 km/h = 60 × 5/18 = **16.67 m/s**`.
3. Time difference `= 12 min = 1/5 h`. `D/5 − D/6 = 1/5 ⇒ D/30 = 1/5 ⇒ D = **6 km**`.
4. `2(40)(60)/100 = **48 km/h**`.
5. `90 km/h = 25 m/s`; `25 × 20 = 500 m` total → bridge `= 500 − 150 = **350 m**`.
6. `d = 10`, `u = 6` → `s = (10−6)/2 = **2 km/h**`.
7. Speed ratio `B/A = 180/200 = 9/10`. In 500 m, B covers `450` → A beats B by **50 m**.
8. Lengths `300 m`; relative speed `= (72−54) × 5/18 = 5 m/s` → `300/5 = **60 s**`.
9. Relative `2 m/s` → `400/2 = **200 s**`.
10. Speed `×5/4` → time `×4/5` → a fall of `1/5 = **20%**`.
</details>

---

## Recall questions

1. State the two average-speed formulas and the condition under which each applies.
2. Give the relative speed for same and opposite directions.
3. What is the difference between crossing a pole and crossing a platform?
4. From downstream and upstream speeds, recover boat and stream speeds.
5. In a race, why can head-starts not be added directly?
6. Distinguish "first meeting anywhere" from "first meeting at the start" on a circular track.
7. State the `√(t₁t₂)` meeting result and where it comes from.
8. If speed increases by 25%, what happens to the time?
