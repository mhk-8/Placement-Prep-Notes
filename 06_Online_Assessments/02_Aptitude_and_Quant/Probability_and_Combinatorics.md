
# Permutations, Combinations and Probability

> **Why it matters.** 1-3 questions in a standard aptitude paper, but **the dominant topic for
> quant/HFT firms** (Optiver, Jane Street, DE Shaw, Quadeye) where it can be the whole test. It is
> also the topic where candidates most often get a *plausible-looking wrong answer*, because the
> arithmetic is easy and the modelling is hard.

---

## 1. The fundamental counting principles

```
MULTIPLICATION (AND):  if a task has stages with m and n choices, total = m × n
ADDITION (OR):         if a task can be done in m OR n mutually exclusive ways, total = m + n
```

> **Translation rule ⭐:** "and" → multiply, "or" → add. Read the problem and mark every "and"
> and "or" before computing.

---

## 2. Permutations and combinations

| Concept | Formula | When |
|---|---|---|
| Permutation (order matters) | `nPr = n!/(n−r)!` | Arrangements, rankings, passwords, seating |
| Combination (order does not) | `nCr = n!/(r!(n−r)!)` | Selections, committees, handshakes, subsets |
| Relation | `nPr = nCr × r!` | |
| All arrangements of `n` distinct | `n!` | |
| Arrangements with repetition of letters | `n!/(p! q! r!)` ⭐ | MISSISSIPPI-type |
| Arrangements of `n` items, `r` at a time, **repetition allowed** | `nʳ` | PINs, number plates |
| Circular arrangements | `(n−1)!` ⭐ | Round table |
| Circular, if rotations AND reflections are the same | `(n−1)!/2` | Necklace, garland ⚠️ |
| Selecting at least one from `n` | `2ⁿ − 1` | |
| Total subsets of `n` | `2ⁿ` | |
| Distribute `n` identical items into `r` groups (each ≥ 0) | `C(n+r−1, r−1)` ⭐ stars and bars |
| Distribute `n` identical into `r` groups (each ≥ 1) | `C(n−1, r−1)` | |
| Number of diagonals of an `n`-gon | `nC2 − n = n(n−3)/2` | |
| Handshakes among `n` people | `nC2` | |
| Derangements (no item in its own place) | `D(n) = n!(1 − 1/1! + 1/2! − … ± 1/n!)`; `D(1)=0, D(2)=1, D(3)=2, D(4)=9, D(5)=44` ⭐ | |

**Useful identities:**
```
nC0 = nCn = 1        nC1 = n         nCr = nC(n−r)     ⭐ use to simplify: 50C48 = 50C2 = 1225
nCr + nC(r−1) = (n+1)Cr                 (Pascal's rule)
nC0 + nC1 + … + nCn = 2ⁿ
```

### The "group together" and "never together" patterns ⭐⭐
```
TOGETHER    : treat the group as one block. n items with k glued → (n − k + 1)! × k!
NEVER TOGETHER : total − (together)
NO TWO TOGETHER (gap method) ⭐ : arrange the others first, then place the restricted items
                 into the gaps.  m others create (m+1) gaps → choose k gaps → C(m+1, k) × k!
```

---

## 3. Probability

```
P(E) = favourable outcomes / total outcomes          0 ≤ P(E) ≤ 1
P(not E) = 1 − P(E)                                  ⭐ often much easier
```

| Rule | Formula |
|---|---|
| Addition | `P(A ∪ B) = P(A) + P(B) − P(A ∩ B)` |
| Mutually exclusive | `P(A ∩ B) = 0` ⇒ `P(A ∪ B) = P(A) + P(B)` |
| Multiplication (independent) | `P(A ∩ B) = P(A)P(B)` |
| Conditional | `P(A\|B) = P(A ∩ B)/P(B)` |
| **Bayes** | `P(A\|B) = P(B\|A)P(A) / [P(B\|A)P(A) + P(B\|A')P(A')]` ⭐⭐ |
| Complement trick | `P(at least one) = 1 − P(none)` ⭐⭐⭐ |
| **Binomial** | `P(exactly r successes in n trials) = nCr pʳ (1−p)^(n−r)` ⭐ |
| Expected value | `E[X] = Σ xᵢ P(xᵢ)` |
| **Linearity of expectation** | `E[X + Y] = E[X] + E[Y]`, **even if X and Y are dependent** ⭐⭐⭐ |
| Odds in favour `a : b` | `P = a/(a+b)` |

**Standard sample-space sizes to have memorised:**
```
One die: 6         Two dice: 36        Three dice: 216
Deck of cards: 52  = 4 suits × 13 ranks
                   = 26 red + 26 black
                   = 12 face cards (J, Q, K), 4 aces
Coin tossed n times: 2ⁿ
```

---

## 4. Shortcuts and traps

```
⭐⭐⭐ "AT LEAST ONE" → use the complement. P(at least one six in 3 rolls) = 1 − (5/6)³.
⭐⭐⭐ LINEARITY OF EXPECTATION works even for dependent events. It is the single most powerful
       tool in expected-value questions. Write X as a sum of indicator variables.
⚠️ Order matters or not? "Committee of 3 from 10" → combination. "First, second, third prize from
   10" → permutation. Read for a word implying rank or position.
⚠️ WITH or WITHOUT replacement changes the denominator on the second draw.
⚠️ Circular arrangement is (n−1)!, not n!. Fixing one person removes the rotational symmetry.
   A garland/necklace additionally halves it (flipping it over).
⭐ nCr = nC(n−r) — always compute the smaller one. 100C97 = 100C3 = 161700.
⚠️ Independent ≠ mutually exclusive. Two mutually exclusive events with non-zero probability are
   NEVER independent (if one happens, the other cannot).
⭐ For dice-sum questions, list the favourable pairs systematically rather than trying a formula.
```

---

## 5. Fully solved examples

### Example 1 — Arrangements with repeated letters and a constraint ⭐
**For the word ARRANGEMENT: (a) how many distinct arrangements are there? (b) in how many are the
two E's together? (c) in how many are the two E's never together?**

The word ARRANGEMENT has 11 letters: A(2), R(2), N(2), E(2), G(1), M(1), T(1).

**(a)**
```
Total = 11! / (2! 2! 2! 2!)
      = 39,916,800 / 16
      = **2,494,800**
```

**(b) Both E's together:** glue the two E's into one block. We now arrange 10 objects:
`[EE], A, A, R, R, N, N, G, M, T` — with A(2), R(2), N(2) still repeated.
```
= 10! / (2! 2! 2!)
= 3,628,800 / 8
= **453,600**
```
(The two E's inside the block are identical, so there is no extra `2!` factor.)

**(c) The two E's never together** `= 2,494,800 − 453,600 = **2,041,200**`.

---

### Example 2 — The gap method ⭐⭐
**In how many ways can 5 boys and 3 girls be seated in a row so that no two girls sit together?**

**Step 1 — arrange the unrestricted items first.** The 5 boys: `5! = 120` ways.

**Step 2 — identify the gaps.** Five boys create 6 gaps (including the two ends):
```
_ B _ B _ B _ B _ B _
```

**Step 3 — place the girls into distinct gaps.** Choose 3 of the 6 gaps and arrange the 3 girls:
```
6P3 = 6 × 5 × 4 = 120
```

**Total** `= 120 × 120 = **14,400**`.

⚠️ The wrong approach — "total minus all-girls-together" — undercounts, because it does not remove
the arrangements where exactly *two* girls are adjacent. The gap method handles all cases at once.

---

### Example 3 — Circular arrangement with a condition
**In how many ways can 8 people sit around a circular table if two particular people must always
sit together? And if they must never sit together?**

```
Total circular arrangements of 8 = (8 − 1)! = 7! = 5040

TOGETHER: glue the pair into one block → 7 objects around a circle = (7 − 1)! = 6! = 720
          The pair can be internally arranged in 2! = 2 ways
          ⇒ 720 × 2 = **1440**

NEVER TOGETHER = 5040 − 1440 = **3600**
```

---

### Example 4 — Probability with the complement ⭐⭐
**A bag contains 5 red, 4 blue and 3 green balls. Three balls are drawn at random without
replacement. Find the probability that (a) all three are red, (b) at least one is red.**

Total ways to draw 3 from 12 = `12C3 = (12 × 11 × 10)/6 = 220`.

**(a) All red:** `5C3 = 10`.
```
P = 10/220 = **1/22 ≈ 0.0455**
```

**(b) At least one red — use the complement.** P(no red) means all three from the 7 non-red balls:
```
7C3 = 35
P(no red) = 35/220 = 7/44
P(at least one red) = 1 − 7/44 = **37/44 ≈ 0.841**
```

⚠️ Computing "at least one" directly (exactly one + exactly two + exactly three) gives the same
answer but takes three times as long and is three times as easy to get wrong.

---

### Example 5 — Bayes' theorem ⭐⭐⭐
**Factory A produces 60% of the items and Factory B produces 40%. 2% of A's items and 5% of B's
items are defective. An item picked at random is found to be defective. What is the probability it
came from Factory B?**

```
P(A) = 0.60    P(D|A) = 0.02
P(B) = 0.40    P(D|B) = 0.05

P(D) = P(D|A)P(A) + P(D|B)P(B)
     = 0.02 × 0.60 + 0.05 × 0.40
     = 0.012 + 0.020
     = 0.032

P(B|D) = P(D|B)P(B) / P(D) = 0.020/0.032 = **0.625 = 5/8**
```

**Natural-frequency version (say this in an interview — it is far more convincing):**
```
Take 1000 items.
  600 from A → 12 defective
  400 from B → 20 defective
  Total defective = 32, of which 20 are from B  →  20/32 = 0.625 ✓
```

---

### Example 6 — Binomial and "at least"
**A fair coin is tossed 6 times. Find the probability of (a) exactly 4 heads, (b) at least 4 heads.**

```
(a) P(exactly 4) = 6C4 (1/2)⁴ (1/2)² = 15/64 ≈ 0.234

(b) P(at least 4) = P(4) + P(5) + P(6)
    = [6C4 + 6C5 + 6C6] / 2⁶
    = [15 + 6 + 1] / 64
    = **22/64 = 11/32 ≈ 0.344**
```

---

### Example 7 — Expected value with linearity ⭐⭐⭐
**Five letters are placed at random into five addressed envelopes, one per envelope. What is the
expected number of letters in their correct envelope? And what is the probability that *no* letter
is correct?**

**Expected value — the elegant way.** Let `Xᵢ = 1` if letter `i` is in the right envelope, else 0.
```
X = X₁ + X₂ + X₃ + X₄ + X₅
E[Xᵢ] = P(letter i correct) = 1/5
E[X] = 5 × (1/5) = **1**
```
⭐ Note that the `Xᵢ` are **strongly dependent** (if four are correct, the fifth must be too), yet
linearity of expectation does not care. The answer is 1 for any `n` — a beautiful and frequently
asked result.

**Probability that none is correct = derangements.**
```
D(5) = 44,   total arrangements = 5! = 120
P = 44/120 = **11/30 ≈ 0.3667**
```
(As `n → ∞` this tends to `1/e ≈ 0.3679` — worth knowing.)

---

### Example 8 — Dice with a sum condition
**Two fair dice are rolled. Find P(sum is 8), P(sum is 8 given that the first die shows 3), and
P(at least one die shows a 6).**

```
Total outcomes = 36

Sum = 8: (2,6)(3,5)(4,4)(5,3)(6,2) → 5 outcomes
P(sum 8) = **5/36**

Given the first die is 3: the second must be 5 → 1 favourable of 6
P(sum 8 | first = 3) = **1/6**

At least one 6: complement — neither is a 6 → 5 × 5 = 25
P = 1 − 25/36 = **11/36**
```

---

### Example 9 — Stars and bars ⭐
**In how many ways can 10 identical chocolates be distributed among 4 children (a) if a child may
get none, (b) if every child must get at least one?**

```
(a) C(n + r − 1, r − 1) = C(10 + 4 − 1, 3) = C(13, 3) = (13 × 12 × 11)/6 = **286**

(b) C(n − 1, r − 1) = C(9, 3) = (9 × 8 × 7)/6 = **84**
```
> **Intuition for (b):** give one chocolate to each child first (that is forced), then distribute
> the remaining 6 freely: `C(6 + 4 − 1, 3) = C(9,3) = 84` ✓ — same answer, two routes.

---

## 6. Practice set

1. How many 4-digit numbers can be formed from the digits 1-7 with no repetition?
2. How many 4-digit even numbers can be formed from 0,1,2,3,4,5 without repetition?
3. In how many ways can a committee of 3 men and 2 women be chosen from 7 men and 5 women?
4. Arrangements of the letters of MISSISSIPPI.
5. A card is drawn from a pack. P(it is a king or a heart)?
6. Two cards are drawn without replacement. P(both aces)?
7. Three coins are tossed. P(at least two heads)?
8. In how many ways can 6 people sit around a circular table?
9. P(a leap year has 53 Sundays)?
10. From 4 red and 6 blue balls, 3 are picked. P(exactly 2 red)?

<details><summary>Answers</summary>

1. `7P4 = 7×6×5×4 = **840**`.
2. Units digit must be 0, 2 or 4. If units = 0: `5×4×3 = 60`. If units = 2 or 4 (2 choices): the
   leading digit cannot be 0, so `4 × 4 × 3 = 48` each → `96`. Total **156**.
3. `7C3 × 5C2 = 35 × 10 = **350**`.
4. `11!/(4! 4! 2!) = 39916800/1152 = **34,650**`.
5. `4/52 + 13/52 − 1/52 = **16/52 = 4/13**`.
6. `(4/52)(3/51) = 12/2652 = **1/221**`.
7. `(3C2 + 3C3)/8 = 4/8 = **1/2**`.
8. `(6−1)! = **120**`.
9. 366 days = 52 weeks + 2 extra days; the 7 possible pairs include Sunday in 2 → **2/7**.
10. `(4C2 × 6C1)/10C3 = (6 × 6)/120 = **3/10**`.
</details>

---

## Recall questions

1. When do you use `nPr` versus `nCr`? Give the word that signals each.
2. Give the formula for arrangements with repeated letters and apply it to BALLOON.
3. Explain the gap method and why "total minus together" fails for "no two together".
4. Why is a circular arrangement `(n−1)!`, and when is it `(n−1)!/2`?
5. State the complement trick and give the situation where it is essential.
6. State Bayes' theorem and give the natural-frequency version of a worked example.
7. State linearity of expectation and explain why dependence does not matter.
8. Give both stars-and-bars formulas and the intuition connecting them.
