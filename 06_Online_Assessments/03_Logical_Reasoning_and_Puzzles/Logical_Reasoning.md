
# Logical Reasoning — Methodologies

> **Why it matters.** Reasoning is 20-30% of a typical mass-recruiter OA and appears in every
> product-company aptitude section. Unlike quant, it needs almost **no formulas** — it needs
> **notation and method**. A candidate who draws the right diagram solves a seating arrangement in
> 90 seconds; a candidate who tries to hold it in their head takes six minutes and gets it wrong.

---

## The universal method ⭐⭐⭐

```
1. READ ALL THE CLUES FIRST. Never start placing anything after clue 1.
2. RANK THE CLUES by how much they fix. Absolute clues ("A sits at the extreme left")
   come first; relative clues ("B is somewhere to the right of C") come last.
3. DRAW THE FRAME — a row of blanks, a circle, a grid, a family tree.
4. PLACE THE MOST CONSTRAINED INFORMATION FIRST.
5. USE A GRID for "who has what" — tick and cross, never memory.
6. WHEN STUCK, TAKE THE SMALLEST BRANCH: pick the clue with exactly 2 possibilities,
   try one, and propagate until a contradiction appears.
7. VERIFY against every clue before answering. ⭐
```

---

# Part 1 — Seating Arrangements

## 1.1 Linear arrangements

**Frame:** draw the row with numbered positions before doing anything else.
```
Position:   1    2    3    4    5    6
           ___  ___  ___  ___  ___  ___
```

**Vocabulary that must be read precisely ⚠️:**

| Phrase | Meaning |
|---|---|
| "immediately to the left of B" | directly adjacent, one seat |
| "to the left of B" | anywhere left, not necessarily adjacent |
| "between A and C" | strictly between, order unspecified unless stated |
| "third to the left of B" | exactly 3 positions left |
| "extreme end" | position 1 or position n |
| "facing north" | your left is the reader's left ⭐ |
| "facing south" | your left is the reader's **right** ⚠️ — mirror everything |

⚠️ **The facing-direction trap is the most common error in the topic.** If people face south,
"to the right of A" in the puzzle's language means to the *left* on your paper. Mark the facing
direction with arrows on your diagram before placing anyone.

### Worked example - linear, 7 people (fully solved) ⭐

**Seven friends P, Q, R, S, T, U, V sit in a row of seven seats, all facing north.**
```
(i)   S sits third to the right of P.
(ii)  V sits at one of the extreme ends.
(iii) Exactly two people sit between V and T.
(iv)  Q sits second to the left of R.
(v)   U sits immediately to the left of P.
```

**Step 1 - draw the frame.**
```
 1     2     3     4     5     6     7        (facing north: left-to-right as drawn)
___   ___   ___   ___   ___   ___   ___
```

**Step 2 - start with the clue that fixes the most.** Clues (ii) and (iii) work together:
```
V = 1  =>  two people between V and T  =>  T = 4
V = 7  =>  two people between V and T  =>  T = 4
```
Either way, **T = 4**. That is a strong opening deduction, and it came from combining two clues
before placing anything. Always look for such a pair first.

**Step 3 - branch on V.**

*Case A: V = 1, T = 4.*
Clue (i) needs `S = P + 3`, with 1 and 4 already occupied:
```
A1: P = 2, S = 5.  Clue (v) puts U immediately left of P, i.e. U = 1. But V = 1.   CONTRADICTION
A2: P = 3, S = 6.  Clue (v) puts U = 2.
    Remaining seats: 5 and 7, for Q and R.
    Clue (iv) needs R = Q + 2:  Q = 5, R = 7.   VALID
```

*Case B: V = 7, T = 4.*
```
B1: P = 2, S = 5.  U = 1.  Remaining seats 3 and 6 for Q, R.
    R = Q + 2 ?  Q=3 -> R=5 (taken);  Q=6 -> R=8 (off the row).   CONTRADICTION
B2: P = 3, S = 6.  U = 2.  Remaining seats 1 and 5 for Q, R.
    R = Q + 2 ?  Q=1 -> R=3 (taken);  Q=5 -> R=7 (V).             CONTRADICTION
```
Case B dies entirely.

**Step 4 - the unique arrangement.**
```
 1     2     3     4     5     6     7
 V     U     P     T     Q     S     R
```

**Step 5 - verify against every clue before answering.** ⭐
```
(i)   S(6) = P(3) + 3                       OK
(ii)  V is at seat 1, an extreme end        OK
(iii) Between V(1) and T(4): seats 2, 3 = exactly two people   OK
(iv)  R(7) = Q(5) + 2                       OK
(v)   U(2) is immediately left of P(3)      OK
```

Typical questions on this set, now trivial to answer:
```
Who sits at the extreme right?                 -> R
How many people sit between U and S?           -> seats 3, 4, 5 = three people
Who is immediately to the right of T?          -> Q
If P and S swap seats, who is third from left? -> S
```

> **What to take from this:** the whole solution was four deductions and a verification. The work
> was in **step 2** - finding the pair of clues that fixes a position outright - and in **step 3**,
> branching on a variable with only two possibilities and propagating until a contradiction. If
> both branches die, you have misread a clue; "between" and "immediately" are the usual culprits.

---

### Double-row (facing each other) ⚠️
```
Row 1 (facing SOUTH):   A    B    C    D
Row 2 (facing NORTH):   P    Q    R    S
```
**The rule:** A faces P, B faces Q, and so on — but because the two rows face opposite ways,
**"to the right of A" and "to the right of P" point in opposite directions on your paper.** Draw
arrows on each row and write "R→" above the row's actual right.

---

## 1.2 Circular arrangements

**Frame:** draw a circle with the positions marked, and **note whether people face the centre or
away**.
```
                    1
             8            2
           7                3
             6            4
                    5
```

| Facing | "To the right of X" means |
|---|---|
| **Facing the centre** (inward) | **anticlockwise** on your paper ⚠️ |
| **Facing outward** | **clockwise** on your paper |

⚠️ This inversion is the single biggest source of circular-arrangement errors. Write the rule in
the corner of your rough sheet before you start.

**Method:**
```
1. Fix ONE person at the top position (rotations are equivalent, so this is free) ⭐
2. Place the person with an absolute relationship to them
3. Use "immediately left/right" clues to build chains
4. Leave "somewhere to the left" clues until the end
```

**Worked example — circular, 8 people facing the centre.**
```
(i)   A sits third to the left of B.
(ii)  C sits second to the right of A.
(iii) D is an immediate neighbour of B.
(iv)  E sits opposite C.
```
```
Step 1: fix B at position 1 (free choice).
Step 2: facing the centre ⇒ "left" is CLOCKWISE on paper.
        A is third to the left of B ⇒ A is 3 clockwise from B ⇒ A = position 4.
Step 3: C is second to the right of A ⇒ right = anticlockwise ⇒ C = position 2.
Step 4: E is opposite C ⇒ in an 8-seat circle, opposite means +4 ⇒ E = position 6.
Step 5: D is adjacent to B(1) ⇒ D = 2 or 8. Position 2 is C ⇒ **D = 8**.
```
Remaining positions 3, 5, 7 go to the remaining people by the remaining clues.

⭐ Note how fixing B at the top and immediately writing "left = clockwise" made every subsequent
step mechanical.

---

## 1.3 Floor / box / stack puzzles

Same as linear, drawn vertically. **Convention: draw the highest floor at the top.**
```
Floor 7 : ___
Floor 6 : ___
Floor 5 : ___
Floor 4 : ___
Floor 3 : ___
Floor 2 : ___
Floor 1 : ___
```
"Above" = higher number; "immediately above" = exactly +1. These are the easiest arrangement type
because there is no facing direction to invert.

---

# Part 2 — Blood Relations

## 2.1 Notation ⭐⭐⭐

Use symbols, never words. Words are where the errors come from.
```
   +  male        −  female       ?  gender unknown
   ══  married (horizontal double line)
   |   parent-child (vertical line)
   ──  sibling (horizontal single line)
```

**Example diagram:**
```
        A(+) ══ B(−)
               |
       ┌───────┼───────┐
      C(+)    D(−)    E(+)
       ║
      F(−)
       |
      G(?)
```
Read: A and B are married with three children C, D, E. C is married to F, and they have a child G.

## 2.2 The method for "pointing at a photograph" problems ⭐⭐

These are the hardest form. **Always work from the innermost phrase outward.**

**Worked example.** *"Pointing to a man, a woman said: 'His mother is the only daughter of my
mother.' How is the woman related to the man?"*

```
Work OUTWARD from the innermost clause:

  "my mother"                     → the woman's mother, call her M
  "the only daughter of my mother" → M has exactly one daughter.
                                     The speaker is female, and is M's daughter.
                                     Since M has ONLY ONE daughter, that daughter is the
                                     speaker HERSELF.                            ⭐ key step
  "His mother is [the speaker]"   → the woman is the man's mother.

Answer: **she is his mother.**
```

**Second worked example.** *"Pointing to a photograph, a man said: 'I have no brother or sister but
that man's father is my father's son.' Whose photograph is it?"*
```
  "my father's son"   → the speaker has no brothers, so his father's only son is HIMSELF.
  "that man's father is [the speaker]" → the man in the photo is the speaker's SON.

Answer: **his son.**
```

**Third worked example (three levels).** *"Introducing a woman, a man said: 'She is the only
daughter of the mother of my wife's brother's son.'"*
```
  "my wife's brother"          → call him X
  "X's son"                    → call him Y
  "the mother of Y"            → X's wife, call her W
  "the only daughter of W"     → W's daughter, i.e. Y's sister

Answer: **the woman is the man's wife's brother's daughter, i.e. his niece by marriage.**
```

> **The rule: parse from the inside out, assign a letter to each relation as you resolve it, and
> draw the diagram as you go.** Never try to hold three levels in your head.

## 2.3 Coded blood relations
Some questions use symbols: `A + B` means A is the father of B, `A − B` means A is the wife of B,
and so on.
```
Method: write the legend at the top of your rough sheet, then translate the expression
LEFT TO RIGHT into a diagram, one symbol at a time. Do not attempt it mentally.
```

---

# Part 3 — Syllogisms

## 3.1 The method ⭐⭐⭐

```
1. DRAW VENN DIAGRAMS. Always. There is no reliable verbal shortcut.
2. Consider ALL possible diagrams consistent with the premises.
3. A conclusion FOLLOWS only if it is true in EVERY possible diagram. ⭐
4. Assume the premises are true even if they contradict the real world. ⚠️
```

## 3.2 The four statement types

| Type | Form | Venn | Converse valid? |
|---|---|---|---|
| **A** (universal affirmative) | All A are B | A inside B | ⚠️ NO — "All B are A" does not follow |
| **E** (universal negative) | No A is B | Disjoint circles | ✅ YES — "No B is A" follows |
| **I** (particular affirmative) | Some A are B | Overlapping | ✅ YES — "Some B are A" follows |
| **O** (particular negative) | Some A are not B | Partial overlap | ⚠️ NO |

## 3.3 The rules that decide most questions ⭐⭐

```
1. Two particular premises ("Some…", "Some…")  →  NO definite conclusion ⭐
2. Two negative premises                        →  NO definite conclusion
3. A negative premise                           →  the conclusion must be negative
4. A particular premise                         →  the conclusion must be particular
5. "All A are B" + "All B are C"  ⇒  All A are C            ✅ valid
6. "All A are B" + "Some B are C" ⇒  NOTHING definite       ⚠️ the classic trap
7. "Some A are B" + "All B are C" ⇒  Some A are C           ✅ valid
8. "All A are B" + "No B is C"    ⇒  No A is C              ✅ valid
```

## 3.4 "Either-or" conclusions ⭐

When two conclusions individually do not follow, but **together they cover all possibilities** and
**cannot both be true**, the answer is "either I or II follows".

The tell: the two conclusions are complementary — e.g. "Some A are B" and "No A is B", or
"All A are B" and "Some A are not B" — with the **same subject and predicate**.

**Worked example.**
```
Statements:  Some pens are books.  All books are papers.
Conclusions: I. Some pens are papers.
             II. All papers are pens.
```
```
Diagram: pens ∩ books ≠ ∅;  books ⊆ papers.
  ⇒ the pens that are books are necessarily papers  ⇒ Conclusion I FOLLOWS ✓
  ⇒ nothing forces papers to be pens                ⇒ Conclusion II does NOT follow ✗

Answer: only I follows.
```

**A trap example.**
```
Statements:  All cats are dogs.  Some dogs are rats.
Conclusion:  Some cats are rats.
```
Draw it: the "some dogs that are rats" region may lie entirely outside the cats circle.
**Does not follow.** ⚠️ This is rule 6, and it is the most-failed syllogism in every paper.

---

# Part 4 — Coding-Decoding

## 4.1 The families

| Type | Example | Method |
|---|---|---|
| **Letter shift** | `CAT → DBU` | Each letter +1. Find the constant shift |
| **Reverse shift** | `CAT → XZG` | Each letter → its mirror (A↔Z, B↔Y): `27 − position` ⭐ |
| **Position value** | `CAT → 3 1 20` | Alphabet positions |
| **Word pattern** | `TEACHER → REHCAET` | Reversal |
| **Substitution** | "sky is blue" = "ma pa ta" | Compare sentences with shared words ⭐ |
| **Mixed / conditional** | Rules applied based on the first/last character | Read the rule table carefully |

**Memorise the alphabet positions ⭐⭐** — the single best investment in this topic:
```
A  B  C  D  E  F  G  H  I  J  K  L  M
1  2  3  4  5  6  7  8  9 10 11 12 13
N  O  P  Q  R  S  T  U  V  W  X  Y  Z
14 15 16 17 18 19 20 21 22 23 24 25 26

EJOTY mnemonic ⭐ :  E=5, J=10, O=15, T=20, Y=25
  — count forward or backward from the nearest of these five anchors.
Mirror rule: letter + mirror = 27.  A↔Z, B↔Y, C↔X, D↔W, E↔V, …, M↔N
```

## 4.2 Substitution coding method ⭐

*"In a code, 'ri sa me' means 'Sun is bright', 'me pa to' means 'bright red flower' and 'sa ti no'
means 'Sun rises daily'. What is the code for 'bright'?"*
```
Sentence 1: ri sa me  = Sun, is, bright
Sentence 2: me pa to  = bright, red, flower
Sentence 3: sa ti no  = Sun, rises, daily

COMMON WORD between 1 and 2 = "bright";  COMMON CODE between 1 and 2 = "me"
⇒ bright = **me**

(Confirm: common word between 1 and 3 = "Sun"; common code = "sa" ⇒ Sun = sa ✓)
```
> **Method: intersect the sentences.** Find the pair sharing exactly one word; the shared code is
> that word. Then propagate.

---

# Part 5 — Direction Sense

## 5.1 Method
```
Always draw. Always mark North at the top of your rough sheet.

                N
                ↑
        W ←─────┼─────→ E
                ↓
                S

Left turn  = 90° anticlockwise
Right turn = 90° clockwise
```
**Track the final displacement with Pythagoras:** if the net movement is `a` east and `b` north,
the straight-line distance is `√(a² + b²)`.

## 5.2 Worked example
**A man walks 10 m north, turns right and walks 15 m, turns right and walks 10 m, then turns left
and walks 5 m. How far and in which direction is he from the start?**
```
Start at origin, facing implied north.
  10 m north      → (0, 10)      facing N
  turn right → E, 15 m → (15, 10)  facing E
  turn right → S, 10 m → (15, 0)   facing S
  turn left  → E,  5 m → (20, 0)   facing E

Net: 20 m east, 0 m north.
Distance = **20 m**, direction = **due East** of the start.
```

⚠️ **The shadow trap.** "In the morning, a man's shadow falls to his left — which way is he
facing?" The sun rises in the **east**, so shadows point **west** in the morning. If west is on his
left, he faces **north**. (In the evening the sun is in the west, so shadows point east.)

---

# Part 6 — Series

## 6.1 Number series — the checklist ⭐⭐⭐

When you see a series, run down this list in order:
```
1. DIFFERENCES between consecutive terms. Constant? → arithmetic.
2. SECOND differences. Constant? → quadratic pattern.
3. RATIOS. Constant? → geometric.
4. Is each term a SQUARE, CUBE, or ±1 from one?           ⭐ check this early
5. PRIME numbers, or primes ±1?
6. ALTERNATING pattern — two interleaved series?          ⭐ very common
7. Each term = f(previous): ×2+1, ×3−2, +n then ×n, etc.
8. Sum/difference of the two preceding terms (Fibonacci-like)?
9. DIGIT operations — digit sum, reversal, digit product?
```

**Worked examples:**
```
(a) 2, 6, 12, 20, 30, ?
    Differences: 4, 6, 8, 10 → next difference 12 → **42**
    (Also: n(n+1) → 1×2, 2×3, 3×4, 4×5, 5×6, 6×7 = 42)

(b) 3, 6, 18, 72, ?
    Ratios: ×2, ×3, ×4 → next ×5 → **360**

(c) 1, 4, 9, 16, 25, ?
    Perfect squares → **36**

(d) 2, 5, 11, 23, 47, ?
    Each = previous × 2 + 1 → 47 × 2 + 1 = **95**

(e) 5, 11, 24, 51, 106, ?
    ×2 + 1, ×2 + 2, ×2 + 3, ×2 + 4 → 106 × 2 + 5 = **217**

(f) 7, 10, 8, 11, 9, 12, ?
    Alternating: (7, 8, 9, …) and (10, 11, 12, …) → **10**

(g) 4, 9, 25, 49, 121, ?
    Squares of primes: 2², 3², 5², 7², 11² → 13² = **169**
```

## 6.2 Letter series
Convert to positions first, **always**.
```
A, C, F, J, O, ?
positions: 1, 3, 6, 10, 15 → differences 2, 3, 4, 5 → next +6 = 21 = **U**
```

## 6.3 Odd one out
Find the property that **all but one** share: parity, primality, being a perfect square,
divisibility, digit sum, or a structural property (all are `n²+1` except one).

---

# Part 7 — Data Sufficiency ⭐⭐

## 7.1 The standard answer options
```
(a) Statement I alone is sufficient, but II alone is not.
(b) Statement II alone is sufficient, but I alone is not.
(c) BOTH together are sufficient, but neither alone is.
(d) EACH alone is sufficient.
(e) Both together are NOT sufficient.
```

## 7.2 The method ⭐⭐⭐
```
1. DO NOT SOLVE. Ask only "could I solve it?" — you lose marks to time here, not to difficulty.
2. Evaluate statement I ALONE. Cover statement II with your hand. ⚠️ literally.
3. Evaluate statement II ALONE, having FORGOTTEN statement I.
4. Only if both fail individually, combine them.
5. "Sufficient" means it yields a UNIQUE answer. Two possible answers = insufficient. ⭐
```

⚠️ **The contamination trap:** after reading statement I, it is extremely easy to carry its
information into your evaluation of statement II. Physically cover the other statement.

**Worked example.**
```
Question: What is the value of x?
  I.  x² = 16
  II. x > 0

I alone: x = 4 or x = −4 → two values → NOT sufficient.
II alone: infinitely many positive numbers → NOT sufficient.
Together: x = 4 uniquely → sufficient.
Answer: **(c)**
```

---

# Part 8 — Statement and Assumption / Conclusion / Course of Action

| Type | The test |
|---|---|
| **Assumption** | Something **taken for granted** and necessary for the statement to make sense. Apply the **negation test ⭐**: negate the assumption — if the statement collapses, it was an assumption |
| **Conclusion** | Must follow **necessarily** from the statement alone, with no outside knowledge |
| **Inference** | Probably true given the statement; weaker than a conclusion |
| **Course of action** | Must be **practical, directly relevant and address the problem**; reject the extreme, the impractical and the merely punitive |
| **Argument (strong/weak)** | Strong = directly relevant and substantial; Weak = trivial, ambiguous, based on an individual case, or restates the question |

⚠️ **The cardinal rule for all of these: use ONLY the information in the statement.** Outside
knowledge, however correct, is not admissible. This is the opposite of how you think in every other
part of life, which is why the topic feels awkward.

---

## Practice protocol

```
Week 1: Learn the notation — seating frames, blood-relation symbols, Venn diagrams,
        alphabet positions with EJOTY. Do 10 questions per type, untimed.
Week 2: 20 mixed questions per day at 60 seconds each.
Week 3: Full reasoning sections, timed, section-locked, logged.
Week 4: Only the types you get wrong; re-derive the method each time.
```

**Track the CAUSE of each error** in `../06_Timed_Mock_Logs/Mock_Test_Template.md`:
misread clue / wrong facing direction / did not draw / ran out of time / genuine gap.

---

## Recall questions

1. Give the seven-step universal method.
2. For people facing the centre of a circle, which paper direction is "to their right"?
3. How do you parse "the only daughter of my mother"?
4. State the four syllogism statement types and which converses are valid.
5. Which syllogism rule kills "All A are B + Some B are C"?
6. What is the EJOTY mnemonic for?
7. Give the mirror rule for letters.
8. In data sufficiency, what does "sufficient" actually require?
9. Describe the negation test for assumptions.
10. In the morning, a man's shadow falls to his right. Which way is he facing?
