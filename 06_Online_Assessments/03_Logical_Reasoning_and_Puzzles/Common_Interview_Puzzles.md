
# Common Interview Puzzles — The Standard Canon

> **Why it matters.** Infosys has a dedicated puzzle section; Microsoft, Adobe, Goldman Sachs and
> every quant firm ask these in interviews; and they appear in OAs as "analytical reasoning".
> There are perhaps **thirty puzzles** that account for the overwhelming majority of what is asked.
> This file contains them with full reasoning.

> **How to use this file.** Read the puzzle, cover the solution, and give yourself five minutes.
> Then read the solution and — more importantly — read the **"pattern"** note, which names the
> transferable idea. The patterns matter more than the puzzles.

---

## How to attack any puzzle ⭐⭐⭐

```
1. RESTATE the problem in your own words. Half of all puzzle failures are misreadings.
2. IDENTIFY the constraint that is doing the work (a limit on weighings, a one-way trip,
   an information bottleneck).
3. ASK: "what is the theoretical limit?"  — information theory bounds how few
   comparisons/weighings/questions can possibly suffice. ⭐
4. TRY A SMALLER CASE. n = 2 or n = 3, then generalise.
5. LOOK FOR AN INVARIANT — something that never changes (parity, a sum, a colour).
6. THINK IN REVERSE — start from the goal state and work backwards.
7. SAY YOUR REASONING ALOUD. In an interview, the process is what is graded, not the answer.
```

---

## 1. The 25 Horses ⭐⭐⭐

**Puzzle.** You have 25 horses and a racetrack that fits 5 horses at a time. You have no timer —
you can only see the finishing order of each race. What is the **minimum number of races** needed
to find the **fastest 3** horses?

<details><summary>Solution</summary>

**Answer: 7 races.**

**Races 1-5:** divide the horses into five groups of five and race each group.
```
Group A:  A1 > A2 > A3 > A4 > A5
Group B:  B1 > B2 > B3 > B4 > B5
Group C:  C1 > C2 > C3 > C4 > C5
Group D:  D1 > D2 > D3 > D4 > D5
Group E:  E1 > E2 > E3 > E4 > E5
```
We now know each group's internal ordering. The 4th and 5th of every group are eliminated
immediately — they cannot be in the overall top 3, since three horses in their own group beat them.

**Race 6:** race the five group winners `A1, B1, C1, D1, E1`. Suppose the result is
```
A1 > B1 > C1 > D1 > E1
```
Now eliminate aggressively:
- `A1` is the **overall fastest** — no race is needed for it. ⭐
- `D1` and `E1` lost to three horses (`A1`, `B1`, `C1`), so they and everyone in their groups are out.
- `C2` and below are out: `C2` is behind `C1`, which is behind `A1` and `B1` — at least 3 faster.
- `B3` and below are out: `B3` is behind `B1`, `B2`, and `A1` — at least 3 faster.
- `A4` and below are out.

**The survivors for places 2 and 3** are exactly five horses:
```
A2, A3   (could be 2nd and 3rd behind A1)
B1, B2   (B1 could be 2nd; B2 could be 3rd)
C1       (could be 3rd)
```

**Race 7:** race those five. The top two finishers are the overall 2nd and 3rd.

**Total: 5 + 1 + 1 = 7 races.**

**Why not fewer?** Six races give at most 6 × 5 = 30 finishing positions of information, and you
must distinguish a large number of possible orderings; more concretely, after race 6 there are
genuinely five candidates for 2nd/3rd that no prior race has compared, so one more race is forced.

**Variants to be ready for:**
- *"Find the fastest 5"* → 5 + 1 = 6 races give you candidates, then it takes more; the standard
  answer for top-5 of 25 is **8 races** (with a careful elimination argument).
- *"Find only the fastest horse"* → **6 races**.
- *"n² horses, track fits n"* → `n + 1` races to find the top 3 (for n ≥ 3), by the same argument.

> **Pattern: elimination by transitivity.** Every result eliminates a *set* of horses, not just
> one. Count what each race rules out before deciding the next race.
</details>

---

## 2. The 8 Balls / 12 Balls Weighing ⭐⭐⭐

**Puzzle A.** 8 identical-looking balls; one is heavier. With a balance scale (no weights), find it
in **2 weighings**.

<details><summary>Solution</summary>

**Weighing 1:** put 3 balls vs 3 balls, leaving 2 aside.
- **If they balance:** the heavy ball is one of the 2 set aside. **Weighing 2:** compare those two.
- **If one side is heavier:** the heavy ball is among those 3. **Weighing 2:** take any 2 of them
  and compare; if they balance, it is the third.

**Answer: 2 weighings.**

**Why 3-vs-3 and not 4-vs-4?** A balance has **three** outcomes (left, right, balanced), so two
weighings can distinguish at most `3² = 9` cases. With 8 balls you need to split into three groups
as equally as possible: 3, 3, 2. A 4-4 split wastes the "balanced" outcome entirely.
</details>

**Puzzle B (the hard version).** 12 balls; one is either heavier **or** lighter — you do not know
which. Find it **and** determine whether it is heavy or light, in **3 weighings**.

<details><summary>Solution</summary>

**Answer: 3 weighings**, and it is tight: there are `12 × 2 = 24` possible answers, and three
weighings distinguish at most `3³ = 27`.

Label the balls 1-12.

**Weighing 1:** `{1,2,3,4}` vs `{5,6,7,8}`.

---
**Case A — they balance.** The odd ball is among `{9,10,11,12}`, and 1-8 are known good.

**Weighing 2:** `{9,10,11}` vs `{1,2,3}` (known good).
- *Balanced* → the odd ball is `12`. **Weighing 3:** `12` vs `1` tells you heavy or light.
- *Left heavy* → one of `9,10,11` is **heavy**. **Weighing 3:** `9` vs `10` — heavier one is it; if
  balanced, it is `11`.
- *Left light* → one of `9,10,11` is **light**. Same third weighing, lighter one wins.

---
**Case B — left side `{1,2,3,4}` is heavier.** So either one of `1-4` is heavy, or one of `5-8` is
light. Balls `9-12` are known good.

**Weighing 2:** `{1,2,5}` vs `{3,4,6}`.   ⭐ *(the clever mixed weighing)*
- *Balanced* → the odd ball is among `{7,8}` and must be **light**.
  **Weighing 3:** `7` vs `8` — the lighter one is the odd ball.
- *Left heavy* → either `1` or `2` is heavy, or `6` is light.
  **Weighing 3:** `1` vs `2`. Heavier → that one (heavy). Balanced → `6` is light.
- *Left light* → either `3` or `4` is heavy, or `5` is light.
  **Weighing 3:** `3` vs `4`. Heavier → that one (heavy). Balanced → `5` is light.

Case C (right side heavier) is the mirror image.

> **Pattern: information-theoretic bounding.** Before designing the weighings, compute
> `3^k ≥ number of outcomes`. This tells you the minimum number of weighings *and* tells you to
> split into three near-equal groups at every step. The same bound governs the
> "find the counterfeit coin" family and comparison-sort lower bounds.
</details>

---

## 3. Water Jug / Die Hard ⭐⭐

**Puzzle.** You have a 3-litre jug and a 5-litre jug and an unlimited water supply. Measure exactly
**4 litres**.

<details><summary>Solution</summary>

**Method 1 (fill the 5 first):**
```
1. Fill the 5-L jug.                        (5, 0) → using (5-jug, 3-jug)
2. Pour into the 3-L jug until full.        (2, 3)
3. Empty the 3-L jug.                       (2, 0)
4. Pour the 2 L into the 3-L jug.           (0, 2)
5. Fill the 5-L jug.                        (5, 2)
6. Pour into the 3-L jug (it takes 1 L).    (4, 3)   ← 4 litres in the 5-L jug ✓
```

**Method 2 (fill the 3 first):**
```
1. Fill 3-L.                (0, 3)
2. Pour into 5-L.           (3, 0)
3. Fill 3-L.                (3, 3)
4. Pour into 5-L until full (5-L takes 2).  (5, 1)
5. Empty 5-L.               (0, 1)
6. Pour the 1 L into 5-L.   (1, 0)
7. Fill 3-L.                (1, 3)
8. Pour into 5-L.           (4, 0)   ← 4 litres ✓
```
Method 1 is shorter (6 steps vs 8), which matters if the question asks for the minimum.

**The general theory ⭐⭐:** with jugs of capacity `a` and `b`, you can measure exactly `c` litres
**if and only if `c` is a multiple of `gcd(a,b)` and `c ≤ max(a,b)`.**

Here `gcd(3,5) = 1`, so *every* integer from 1 to 5 is measurable. With jugs of 4 and 6,
`gcd = 2`, so only even amounts are possible — you could never measure 3 litres.

This follows from Bézout's identity: `c = ax + by` has an integer solution exactly when
`gcd(a,b) | c`, and the two directions of pouring correspond to positive and negative coefficients.

> **Pattern: state-space search plus a number-theoretic invariant.** The invariant (every
> reachable amount is a multiple of the gcd) both tells you when the puzzle is impossible and
> guides the search when it is possible.
</details>

---

## 4. Burning Ropes ⭐⭐

**Puzzle.** You have two ropes. Each burns completely in exactly 60 minutes, but **not uniformly** —
half the rope may burn in 5 minutes and the other half in 55. Measure **45 minutes**.

<details><summary>Solution</summary>

**The key insight: lighting a rope at *both* ends makes it burn out in exactly half its total
time, regardless of non-uniformity.** ⭐

```
t = 0   : light Rope A at BOTH ends, and Rope B at ONE end.
t = 30  : Rope A is fully burnt (both-ends halves its 60 minutes).
          Rope B has 30 minutes of burn left.
          At this instant, light Rope B's OTHER end.
t = 45  : Rope B is fully burnt — its remaining 30 minutes burned in 15.
```
**Total: 45 minutes.** ✓

**Variants:**
- *Measure 15 minutes:* the procedure above already marks it — the interval from `t = 30` (Rope A
  burns out) to `t = 45` (Rope B burns out) is exactly 15 minutes. You cannot produce a 15-minute
  interval *starting at zero* with only two ropes.
- *Measure 30 minutes:* light one rope at both ends; it finishes at `t = 30`.
- *Measure 22.5 minutes?* Not achievable. You can only ever halve a *remaining* burn time by
  lighting a further end, so the reachable marks are built from halvings of 60 at the instants when
  a rope runs out — 60, 30, 45, 15, 7.5 (with a third rope), and so on. 22.5 is not among them
  with two ropes.

> **Pattern: change the rate, not the measurement.** You cannot measure a non-uniform rope, but you
> can *control the burn rate* by choosing how many ends are lit. Whenever a resource is
> non-uniform, look for an operation that makes the non-uniformity cancel.
</details>

---

## 5. Bridge Crossing (with a torch) ⭐⭐

**Puzzle.** Four people must cross a bridge at night. They have one torch, the bridge holds at most
two people at a time, and anyone crossing must carry the torch. Their crossing times are **1, 2, 5
and 10 minutes**; a pair moves at the slower person's pace. What is the **minimum total time**?

<details><summary>Solution</summary>

**Answer: 17 minutes.**

**The naive (wrong) strategy** is to have the fastest person ferry everyone:
```
1+2 across (2), 1 back (1), 1+5 across (5), 1 back (1), 1+10 across (10) = 19 minutes
```

**The optimal strategy sends the two slowest together:** ⭐
```
Step 1:  1 and 2 cross          →  2 minutes   (far side: 1, 2)
Step 2:  1 returns              →  1 minute    (near side: 1, 5, 10)
Step 3:  5 and 10 cross         → 10 minutes   (far side: 2, 5, 10)
Step 4:  2 returns              →  2 minutes   (near side: 1, 2)
Step 5:  1 and 2 cross          →  2 minutes   (all across)
                                  ───────────
                          Total = 17 minutes
```

**Why it works:** the 10-minute person will cost 10 minutes no matter what. The question is whether
the 5-minute person's time is *also* paid separately. By pairing 5 and 10, the 5 is absorbed into
the 10's crossing and costs nothing extra. The price is that a slightly slower person (2 instead of
1) must make one return trip — a cost of 1 extra minute, against a saving of 5 − 1 = 4.

**The general rule for `n` people:** at each stage, compare
```
Strategy A (fastest ferries):     t₁ + t_k + t₁   ... = 2t₁ + t_k
Strategy B (two slowest together): t₂ + t_k + t₁ ... = t₁ + 2t₂ + t_k   — in the standard framing,
                                   compare  t₁ + 2t₂  vs  2t₁ + t_{k−1}
```
and take the cheaper. Greedy with that comparison at each step is optimal.

> **Pattern: pair the expensive items so their costs overlap.** This is the same idea as batching
> expensive operations in systems work — pay the large cost once, for two items.
</details>

---

## 6. 100 Prisoners and Hats ⭐⭐

**Puzzle.** 100 prisoners stand in a line, each wearing a red or blue hat. Each can see all the
hats in front of them but not their own or those behind. Starting from the back, each must guess
their own hat colour, loud enough for everyone to hear. A wrong guess means death. They may agree
on a strategy beforehand. **How many can be guaranteed to survive?**

<details><summary>Solution</summary>

**Answer: 99 guaranteed, and the 100th has a 50% chance.**

**Strategy — parity encoding.** ⭐⭐
```
Beforehand they agree: "RED means an EVEN number of red hats ahead; BLUE means ODD."

The last prisoner (who sees all 99 hats in front) counts the red hats ahead and calls
RED if that count is even, BLUE if odd.  He has a 50% chance — he is sacrificing himself
to transmit ONE BIT of information to everyone else.

Prisoner 99 now knows the parity of reds among prisoners 1-99, and can see prisoners 1-98.
  If the announced parity and what he sees differ in parity, his own hat is red; else blue.
He announces correctly, and in doing so tells everyone his colour.

Prisoner 98 heard the original parity AND prisoner 99's colour. He updates the parity to
exclude 99, compares with what he sees among 1-97, and deduces his own hat. And so on.

Every prisoner from 99 down to 1 survives with certainty.
```

**Why can't the 100th do better?** He has no information about his own hat at all — nothing anyone
says or does depends on it before he speaks. So 50% is the best possible for him, and 99 guaranteed
is optimal.

**Variants:**
- *With `k` hat colours:* use arithmetic **mod k** instead of parity. The last prisoner announces
  the sum of all visible hats mod k. Again 99 survive.
- *Prisoners cannot hear each other:* the problem collapses — no information can propagate, and
  each prisoner has a 50% chance independently.
- *All must guess simultaneously:* a different, harder problem (the "hats and finite sets" puzzle).

> **Pattern: one bit of shared information, used by everyone.** Parity (or a sum mod k) is the
> canonical way to broadcast a constraint that each person can subtract their own observation from.
> The same trick underlies checksums, RAID parity and error-detecting codes. ⭐
</details>

---

## 7. The 100 Doors / Lockers ⭐⭐

**Puzzle.** 100 closed doors. Person 1 toggles every door; person 2 toggles every 2nd door; person
3 every 3rd; … person 100 toggles the 100th. Which doors are **open** at the end?

<details><summary>Solution</summary>

**Answer: the doors at perfect-square positions — 1, 4, 9, 16, 25, 36, 49, 64, 81, 100 (ten doors).**

**Reasoning.** Door `n` is toggled once by each person `d` that divides `n`. So door `n` ends open
**iff `n` has an odd number of divisors**.

Divisors normally come in pairs `(d, n/d)`, which makes the count even. The only way to get an odd
count is when one such pair collapses — that is, when `d = n/d`, i.e. `n = d²`.

**Therefore exactly the perfect squares are left open.** ✓

*Example:* door 12 has divisors 1,2,3,4,6,12 — six of them, even → closed.
Door 16 has 1,2,4,8,16 — five, odd (4 pairs with itself) → open.

> **Pattern: reframe "process" as "property".** Do not simulate 100 × 100 toggles; ask what
> determines the final state of a single door. Almost every "repeated operation" puzzle yields to
> this reframing.
</details>

---

## 8. Two Eggs and 100 Floors ⭐⭐

**Puzzle.** You have **2 identical eggs** and a 100-storey building. There is some floor `f` from
which an egg breaks when dropped (and from any higher floor), but survives from `f − 1` and below.
An egg that does not break can be reused; a broken egg is gone. Find `f` with the **minimum number
of drops in the worst case**.

<details><summary>Solution</summary>

**Answer: 14 drops.**

**Why not "drop from 10, 20, 30, …"?** That costs up to `10 + 9 = 19` drops in the worst case
(9 failed jumps of 10, then 9 linear steps). The problem is that the cost of the linear phase
stays constant while the number of jumps already made grows.

**The fix: shrink the interval so the total stays constant.** ⭐ Start at floor `k`, then go up
`k−1`, then `k−2`, and so on:
```
Drops from floors:  k, k + (k−1), k + (k−1) + (k−2), …
```
After the first drop you have `k − 1` remaining drops in the worst case, and the next interval is
`k − 1`, so the total is always `k`. We need the intervals to cover all 100 floors:
```
k + (k−1) + (k−2) + … + 1  ≥  100
k(k+1)/2 ≥ 100
k² + k − 200 ≥ 0
k ≥ (−1 + √801)/2 ≈ 13.65
⇒ k = **14**
```

**The drop sequence:** 14, 27, 39, 50, 60, 69, 77, 84, 90, 95, 99, 100.
*Check:* if the egg breaks at floor 14, test 1-13 linearly → at most `1 + 13 = 14` drops.
If it survives 14 but breaks at 27, test 15-26 → `2 + 12 = 14` drops. The total is 14 everywhere ✓

**Generalisation:** with `e` eggs and `d` drops, the maximum number of floors coverable is
```
f(e, d) = Σ_{i=1}^{e} C(d, i)
```
For `e = 2`: `C(d,1) + C(d,2) = d(d+1)/2`, which reproduces the answer above.

> **Pattern: equalise the worst case.** When a strategy has two phases whose costs trade off,
> the optimum usually makes the total cost identical along every branch. This is the same principle
> behind balanced binary search trees and optimal Huffman coding.
</details>

---

## 9. Three Bulbs, Three Switches ⭐

**Puzzle.** Three switches outside a closed room control three bulbs inside. You may flip the
switches as you like, but you may enter the room **only once**. How do you determine which switch
controls which bulb?

<details><summary>Solution</summary>

```
1. Turn switch A ON. Wait about 10 minutes.
2. Turn A OFF and turn B ON.
3. Enter the room immediately.

   Bulb that is ON            → switch B
   Bulb that is OFF but WARM  → switch A      ⭐ the extra channel
   Bulb that is OFF and COLD  → switch C
```

**The insight:** with one observation you can only read one bit per bulb (on/off), which is not
enough for three states. **Heat is a second channel**, giving you the extra information for free.

> **Pattern: find the second channel.** When an information bound says a puzzle is impossible,
> look for an observable you have not counted. Interviewers use this puzzle specifically to see
> whether you accept the stated constraints too literally.
</details>

---

## 10. The Poisoned Wine ⭐⭐

**Puzzle.** A king has **1000 bottles** of wine, exactly one of which is poisoned. The poison kills
in exactly 24 hours, in any dose. He has prisoners to test with, and the feast is in 24 hours. What
is the **minimum number of prisoners** needed to identify the poisoned bottle?

<details><summary>Solution</summary>

**Answer: 10 prisoners.**

**The binary encoding.** ⭐⭐
```
Number the bottles 0 to 999 in BINARY. 1000 < 2¹⁰ = 1024, so 10 bits suffice.

Assign prisoner i (i = 0…9) to bit i.
Prisoner i drinks from EVERY bottle whose number has a 1 in bit position i.

After 24 hours, read off which prisoners died:
   dead prisoner i  →  bit i = 1
   alive prisoner i →  bit i = 0

The resulting 10-bit number IS the poisoned bottle's index.
```

*Example:* if prisoners 0, 3 and 5 die, the bottle number is `2⁰ + 2³ + 2⁵ = 1 + 8 + 32 = 41`.

**Why 10 is optimal:** each prisoner yields one bit (alive/dead), so `k` prisoners distinguish at
most `2^k` bottles. `2⁹ = 512 < 1000 ≤ 1024 = 2¹⁰`, so `k = 10`.

**Variants worth knowing:**
- *Poison takes 24 h but you have 48 h:* two rounds give you more information per prisoner,
  reducing the count.
- *Two poisoned bottles:* now you need error-correcting-code style designs, and the count rises.
- *Poison kills in 24 h and the feast is in 5 weeks:* with `t` sequential rounds each prisoner
  yields `log₂(t+1)` bits, so far fewer prisoners are needed.

> **Pattern: parallel binary encoding.** When you need to identify one of `N` items with
> simultaneous yes/no tests, assign each test to a bit. This is exactly how a memory address
> decoder works, and how group testing works in medical screening. ⭐
</details>

---

## 11. Crossing the River (missionaries / wolf-goat-cabbage) ⭐

**Puzzle.** A farmer must cross a river with a **wolf**, a **goat** and a **cabbage**. The boat
holds the farmer plus one item. If left alone together, the wolf eats the goat, and the goat eats
the cabbage. How does everyone get across?

<details><summary>Solution</summary>

```
1. Take the GOAT across.          (wolf, cabbage safe together)
2. Return alone.
3. Take the WOLF across.
4. Bring the GOAT BACK.           ⭐ the counter-intuitive step
5. Take the CABBAGE across.       (wolf, cabbage safe together)
6. Return alone.
7. Take the GOAT across.          Done.
```

**Answer: 7 crossings.**

**The key move is step 4** — bringing something *back*. Candidates get stuck because they assume
progress must be monotonic. It must not.

> **Pattern: allow backward moves.** Any state-space search may require moving away from the goal.
> The same realisation is needed in the jealous-husbands and missionaries-and-cannibals variants.
</details>

---

## 12. The Pirates and the Gold ⭐⭐⭐

**Puzzle.** Five pirates (ranked 5 = most senior down to 1) must divide **100 gold coins**. The
most senior proposes a division; **all pirates including the proposer vote**. If at least half vote
in favour, the proposal passes. Otherwise the proposer is thrown overboard and the next most senior
proposes. Each pirate is perfectly rational and prioritises, in order: (1) survival, (2) maximising
gold, (3) throwing others overboard. What does pirate 5 propose?

<details><summary>Solution</summary>

**Answer: `(97, 0, 1, 0, 2)` for pirates 5, 4, 3, 2, 1 respectively.**

**Solve by backward induction — always start from the smallest case.** ⭐⭐

```
2 PIRATES LEFT (2 and 1):
   Pirate 2 proposes and votes for himself: 1 vote out of 2 = half = passes.
   ⇒ (100, 0).  Pirate 1 gets NOTHING and cannot prevent it.
   ⇒ Pirate 1 desperately wants to avoid this state.

3 PIRATES LEFT (3, 2, 1):
   Pirate 3 needs 2 votes (himself + 1).
   Pirate 1 knows that if 3 dies, he gets 0. So ONE coin buys his vote.
   ⇒ (99, 0, 1).  Pirate 2 gets nothing.
   ⇒ Pirate 2 desperately wants to avoid this state.

4 PIRATES LEFT (4, 3, 2, 1):
   Pirate 4 needs 2 votes out of 4 (half of 4 = 2, and he has his own).
   Pirate 2 would get 0 if 4 dies, so ONE coin buys pirate 2.
   ⇒ (99, 0, 1, 0).

5 PIRATES (5, 4, 3, 2, 1):
   Pirate 5 needs 3 votes out of 5 (himself + 2).
   Who is cheap? Whoever gets 0 in the 4-pirate outcome (99, 0, 1, 0):
       pirate 3 gets 0  →  1 coin buys him
       pirate 1 gets 0  →  1 coin buys him
   But careful — pirate 1 got 0 in the 4-pirate case, so 1 coin suffices...
   With 100 coins the standard solution gives pirate 1 slightly more for robustness:
   ⇒ **(97, 0, 1, 0, 2)** — pirate 5 keeps 97, buys pirate 3 with 1 and pirate 1 with 2.
   Votes: 5 (yes), 3 (yes), 1 (yes) = 3 of 5 ⇒ passes. ✓
```

(With strict rationality and exact tie-breaking, `(98, 0, 1, 0, 1)` also works; the standard
published answer is (97,0,1,0,2), and interviewers accept either provided the **backward-induction
reasoning** is correct. The reasoning is what is being graded.)

> **Pattern: backward induction.** Never start from the full problem. Solve `n = 1`, then `n = 2`,
> and let each answer define what the players in the next case fear. This is the core technique for
> every sequential-game puzzle.
</details>

---

## 13. Two Trains and a Bird ⭐

**Puzzle.** Two trains, 200 km apart, approach each other at 50 km/h each. A bird flies at 80 km/h
from one train to the other, turning around instantly each time it arrives, until the trains meet.
**How far does the bird fly?**

<details><summary>Solution</summary>

**Do not sum the infinite series.** ⭐ Compute the *time*.
```
Closing speed = 50 + 50 = 100 km/h
Time until the trains meet = 200/100 = 2 hours
The bird flies for that entire time at 80 km/h.
Distance = 80 × 2 = **160 km**
```

(There is a famous anecdote about von Neumann summing the series in his head and, when told about
the trick, replying that he *had* used the trick — by which he meant he summed the series.)

> **Pattern: find the quantity that is easy to compute.** The geometry of the bird's path is awful;
> the elapsed time is trivial. When a process is complicated, look for the conserved or simply
> computed quantity.
</details>

---

## 14. The Handshake Problem ⭐

**Puzzle.** At a party, every person shakes hands with every other person exactly once. If there
were 66 handshakes, how many people were at the party?

<details><summary>Solution</summary>

```
nC2 = 66
n(n − 1)/2 = 66
n(n − 1) = 132
n² − n − 132 = 0
(n − 12)(n + 11) = 0
n = **12 people**
```

**The famous variant (worth knowing):** *"At a party of n couples, each person shakes hands with
some others; no one shakes their own spouse's hand. The host asks everyone (including his own
spouse) how many hands they shook and gets `2n − 1` different answers. How many hands did the
host's spouse shake?"* **Answer: `n − 1`** — provable by an elegant pairing argument.

> **Pattern: recognise `nC2`.** Handshakes, line segments between points, pairs, matches in a
> round-robin tournament and comparisons in a bubble sort are all `nC2`.
</details>

---

## 15. Measuring with an Hourglass ⭐

**Puzzle.** You have a 4-minute and a 7-minute hourglass. Measure exactly **9 minutes**.

<details><summary>Solution</summary>

```
t=0 : start BOTH hourglasses.
t=4 : the 4-min runs out. Flip it immediately.          (7-min has 3 min left)
t=7 : the 7-min runs out. The 4-min has 1 min left.
      Flip the 7-min NOW.                                ⭐
t=8 : the 4-min runs out (started at 4, ran 4 min).
      The 7-min has been running 1 minute, so it has 1 minute of SAND IN THE BOTTOM.
      Flip the 7-min: it will now run for exactly 1 minute.
t=9 : the 7-min runs out. **9 minutes measured.** ✓
```

**Why the `t = 8` flip works:** at `t = 8` the 7-minute glass has been running for exactly 1
minute since its flip at `t = 7`, so exactly 1 minute of sand sits in its lower bulb. Flipping it
therefore starts a precise 1-minute timer. Converting "1 minute elapsed" into "1 minute remaining"
is the only real move in the puzzle.

**The general principle:** flipping an hourglass converts "time elapsed" into "time remaining".
That reversal is the only operation you have, and every hourglass puzzle is built on it.

> **Pattern: an operation that reflects state.** Note the similarity to the burning-rope puzzle —
> both give you one non-obvious operation (light both ends / flip) that transforms the resource.
</details>

---

## 16. The Camel and Bananas ⭐⭐

**Puzzle.** A merchant has **3000 bananas** and a camel that can carry at most **1000 bananas** at
a time. The market is **1000 km** away, and the camel eats **1 banana per kilometre** travelled (in
either direction). What is the maximum number of bananas that can reach the market?

<details><summary>Solution</summary>

**Answer: 533 bananas.**

**The key insight:** the camel must make multiple trips, so the *effective consumption rate per
kilometre* depends on how many trips are in progress. Establish intermediate depots.

```
PHASE 1 — while 3000 bananas remain, the camel needs 5 trips per km
          (3 forward loads: go-return, go-return, go = 5 crossings)
          Cost: 5 bananas per km.
   Move forward until the stock drops to 2000: need to lose 1000 bananas.
   Distance = 1000/5 = **200 km**.
   At 200 km: 2000 bananas remain.

PHASE 2 — with 2000 bananas, the camel needs 3 trips per km (go-return, go).
          Cost: 3 bananas per km.
   Move forward until the stock drops to 1000: need to lose 1000 bananas.
   Distance = 1000/3 = **333.33 km**.
   Position: 200 + 333.33 = 533.33 km.  Stock: 1000 bananas.

PHASE 3 — with 1000 bananas, one single trip. Cost: 1 banana per km.
   Remaining distance = 1000 − 533.33 = 466.67 km.
   Bananas at market = 1000 − 466.67 = **533.33 → 533 bananas**
```

> **Pattern: the cost rate changes with the state.** Identify the thresholds at which the rate
> changes (here, each multiple of the carrying capacity) and compute each segment separately. The
> same structure appears in fuel-depot and jeep-crossing-the-desert problems.
</details>

---

## 17. Rope Around the Earth ⭐

**Puzzle.** A rope is tied tightly around the Earth's equator (circumference ≈ 40,000 km). You add
**1 metre** to its length and lift it uniformly off the ground everywhere. How high off the ground
is it?

<details><summary>Solution</summary>

```
Original: C = 2πR
New:      C + 1 = 2π(R + h)
Subtract: 1 = 2πh
h = 1/(2π) ≈ **0.159 m ≈ 16 cm**
```

**The astonishing part: the answer does not depend on `R` at all.** The same 1 metre added to a
rope around a tennis ball lifts it by the same 16 cm.

> **Pattern: the algebra beats the intuition.** When intuition screams "that can't be right",
> write the two equations and subtract. This is an excellent interview puzzle precisely because it
> tests whether you trust your own algebra.
</details>

---

## 18. Blue-Eyed Islanders (the hard one) ⭐⭐⭐

**Puzzle.** On an island, 100 people have blue eyes and 100 have brown eyes. No one knows their own
eye colour, there are no mirrors, and eye colour is never discussed. Anyone who deduces they have
blue eyes must leave on the midnight ferry that night. One day a visitor says publicly: *"At least
one of you has blue eyes."* What happens?

<details><summary>Solution</summary>

**Answer: all 100 blue-eyed people leave on the 100th night, simultaneously.**

**Induction on the number of blue-eyed people:**
```
n = 1: The single blue-eyed person sees NO other blue eyes. The visitor said at least one
       exists, so it must be him. He leaves on night 1.

n = 2: Each blue-eyed person sees exactly one other. He reasons: "If I am not blue, that
       person is the only one and will leave tonight." Night 1 passes, nobody leaves.
       That silence is INFORMATION: therefore I must be blue too. Both leave on night 2.

n = k: Each blue-eyed person sees k−1 others and reasons: "If I am not blue, they will all
       leave on night k−1." Night k−1 passes with nobody leaving ⇒ I am blue.
       All k leave on night k.

n = 100: all 100 leave on night 100.
```

**"But everyone already knew that at least one person has blue eyes — what did the visitor add?"**
⭐⭐ This is the deep part, and the point of the puzzle. The visitor created **common knowledge**.

- Everyone *knew* "at least one blue-eyed person exists".
- But not everyone knew that *everyone* knew it. And not everyone knew that everyone knew that
  everyone knew it — and so on.
- The public announcement makes the fact known to all, known-to-be-known to all, at every level of
  nesting simultaneously. That infinite tower is what the induction consumes, one level per night.

> **Pattern: common knowledge vs mutual knowledge.** This distinction matters in distributed
> systems (the Two Generals problem is the same idea), in game theory and in consensus protocols.
> If an interviewer asks this, they want to hear the words "common knowledge".
</details>

---

## 19. Assorted quick puzzles

<details><summary>Q: A clock shows 3:15. What is the angle between the hands?</summary>

```
Minute hand at 15 min = 15 × 6° = 90°
Hour hand at 3:15     = 3 × 30° + 15 × 0.5° = 90° + 7.5° = 97.5°
Angle = |97.5 − 90| = **7.5°**
```
**Formula to memorise ⭐:** `angle = |30H − 5.5M|` (take 360 − that if it exceeds 180).
Check: `|30(3) − 5.5(15)| = |90 − 82.5| = 7.5` ✓

The hands **coincide** 22 times in 24 hours, and are **at right angles** 44 times in 24 hours.
</details>

<details><summary>Q: You have 9 coins, one of which is fake (lighter). Find it in 2 weighings.</summary>

Split 3-3-3. Weigh the first two groups.
- If balanced, the fake is in the third group.
- If one side is lighter, it is in that group.
Then weigh two coins from the identified group: lighter one is fake, or if balanced it is the third.
**2 weighings.** (`3² = 9` — exactly tight.)
</details>

<details><summary>Q: How many times do the hour and minute hands overlap in 12 hours?</summary>

**11 times.** The minute hand laps the hour hand 11 times in 12 hours (the minute hand completes
12 revolutions, the hour hand 1, so it gains 11 laps). The overlaps are at roughly 65 5/11-minute
intervals, starting at 12:00.
</details>

<details><summary>Q: A snail climbs 3 m up a 10 m well each day and slips 2 m each night. How many days to get out?</summary>

Net progress is 1 m/day, but **the last day has no slip**.
```
After day 7 (net 7 m), the snail is at 7 m.
On day 8 it climbs 3 m → reaches 10 m and is OUT before nightfall.
Answer: **8 days**
```
⚠️ Answering 10 (by dividing 10 by the net 1 m/day) is the trap.
</details>

<details><summary>Q: Three ants sit at the three corners of a triangle and each walks toward a randomly chosen adjacent corner. What is the probability they do not collide?</summary>

Each ant picks one of 2 directions → `2³ = 8` equally likely outcomes.
They avoid collision only if **all three go clockwise** or **all three go anticlockwise** — 2
outcomes.
```
P = 2/8 = **1/4**
```
(For `n` ants on an `n`-gon, the answer is `2/2ⁿ = 2^(1−n)`.)
</details>

<details><summary>Q: You have an unlimited supply of 6-, 9- and 20-piece chicken nugget boxes. What is the largest number you CANNOT buy exactly?</summary>

**43.** (The "Chicken McNugget" / Frobenius number for {6,9,20}.) Every number from 44 upward is
achievable: 44 = 20+6+9+9, 45 = 9×5, 46 = 20+20+6, 47 = 20+9+9+9, 48 = 6×8, 49 = 20+20+9, and then
add 6 to each to cover everything beyond.

For **two** coprime values `a` and `b`, the Frobenius number is `ab − a − b`.
</details>

<details><summary>Q: A man has two children. At least one is a boy. What is the probability both are boys?</summary>

**1/3.** The sample space is `{BB, BG, GB, GG}`; "at least one boy" removes `GG`, leaving three
equally likely cases, of which one is `BB`.

⚠️ **The famous variant:** *"The older child is a boy"* → the answer is **1/2** (the space is
`{BB, BG}`). The wording determines the answer, and interviewers test exactly this distinction.
</details>

<details><summary>Q: Why are manhole covers round?</summary>

A round cover **cannot fall through its own hole**, because a circle has constant width in every
orientation. A square cover can be turned diagonally and dropped through (the diagonal exceeds the
side). Secondary reasons: it can be rolled rather than carried, and it needs no alignment when
being replaced.

*(Bonus point: a Reuleaux triangle also has constant width and would work too.)*
</details>

---

## 20. Puzzle-solving self-test

Give yourself 5 minutes each, cold, and score yourself:

```
□ 25 horses                    □ 12 balls (heavy or light)
□ 3 and 5 litre jugs           □ Burning ropes (45 min)
□ Bridge crossing (17 min)     □ 100 prisoners and hats
□ 100 doors                    □ 2 eggs, 100 floors
□ 3 bulbs, 3 switches          □ 1000 bottles, 10 prisoners
□ Wolf, goat, cabbage          □ 5 pirates, 100 coins
□ Two trains and a bird        □ Camel and bananas
□ Rope around the Earth        □ Blue-eyed islanders
```

**Scoring:** 14+ solved cold means this section is done. Below 10, re-read the **patterns** (not
the puzzles) and retry in a week — the patterns transfer to puzzles you have never seen, which is
the actual point.

---

## The pattern index ⭐⭐⭐

| Pattern | Puzzles that use it | Where else it appears |
|---|---|---|
| Information-theoretic bound (`3^k`, `2^k`) | 12 balls, 8 balls, 1000 bottles, 25 horses | Comparison-sort lower bound, Huffman coding |
| Elimination by transitivity | 25 horses | Tournament/selection algorithms |
| Parity / sum mod k as a broadcast bit | 100 prisoners, hats variants | Checksums, RAID, error-detecting codes |
| Backward induction | Pirates, any sequential game | Game theory, DP |
| Equalise the worst case | 2 eggs 100 floors | Balanced trees, load balancing |
| Invariant (gcd, parity, colouring) | Water jugs, chessboard tiling | Proving impossibility |
| Change the rate, not the measure | Burning ropes, hourglass | Control theory intuition |
| Reframe process as property | 100 doors | Avoiding simulation |
| Find the second channel | 3 bulbs | Side channels, lateral thinking |
| Allow backward moves | River crossing | BFS/DFS state-space search |
| Compute the easy quantity | Two trains and a bird | Conservation arguments |
| Common knowledge | Blue-eyed islanders | Distributed consensus, Two Generals |
| State-dependent cost rate | Camel and bananas | Amortised analysis |
