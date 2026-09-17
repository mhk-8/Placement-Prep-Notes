# Compilers & TOC — Worked Problems

---

## N1. Minimal DFA — divisibility

**Q.** Minimal DFA over {0,1} accepting binary strings whose value is divisible by 3.

**Reasoning.** Read the string most-significant bit first. If the value so far is `v`, appending bit `b` gives `2v + b`. Only `v mod 3` matters, so the states are exactly the three residues.

```
States: q0 (≡0), q1 (≡1), q2 (≡2)   —   start and accept: q0

  from  on 0 → (2v) mod 3      on 1 → (2v+1) mod 3
  q0        q0                       q1
  q1        q2                       q0
  q2        q1                       q2
```
**Answer: 3 states.**

**Generalisation:** divisibility by n needs exactly **n** states. Likewise "number of a's ≡ 0 mod k" needs k states.

---

## N2. Minimal DFA — k-th symbol from the right

**Q.** Minimal DFA over {0,1} accepting strings whose **3rd symbol from the right** is 1.

**Reasoning.** You cannot know which symbol is third-from-last until the input ends, so the automaton must remember the **last three symbols**. Every one of the 8 combinations is distinguishable (Myhill–Nerode: for any two distinct 3-bit histories there is a suffix separating them).

**Answer: 2³ = 8 states.**

**Contrast:** "3rd symbol from the **left** is 1" needs only about 5 states, because after the third symbol nothing more must be remembered. The left/right swap is the trap in this question family.

---

## N3. NFA → DFA subset construction

**Q.** An NFA has 4 states. What is the maximum number of states in the equivalent DFA?

```
Each DFA state is a SUBSET of NFA states → 2^4 = 16
```
**Answer: 16** (including the empty set, which is the dead state).

The bound is tight — there are families of NFAs whose minimal equivalent DFA really does need 2ⁿ states — but for most concrete NFAs the reachable subsets are far fewer.

---

## N4. Pumping lemma — proving `aⁿbⁿ` is not regular

**Claim.** `L = { aⁿbⁿ : n ≥ 0 }` is not regular.

**Proof.**
1. Assume L is regular with pumping length p.
2. Choose `s = aᵖbᵖ`. Then `s ∈ L` and `|s| = 2p ≥ p`. ✔
3. The lemma gives `s = xyz` with `|xy| ≤ p` and `|y| ≥ 1`.
4. Since `|xy| ≤ p`, **x and y consist only of a's**, so `y = a^j` for some `j ≥ 1`.
5. Pump with `i = 2`: `xy²z = a^(p+j) b^p`.
6. This has more a's than b's, so `xy²z ∉ L` — contradicting the lemma.
∴ L is not regular. ∎

**The template to reuse:** assume regular → pick an adversarial string of length ≥ p → use `|xy| ≤ p` to force y into a known region → pump to break the counting constraint.

---

## N5. FIRST and FOLLOW

**Q.** Compute FIRST and FOLLOW for the standard expression grammar.

```
E  → T E'
E' → + T E' | ε
T  → F T'
T' → * F T' | ε
F  → ( E ) | id
```

**FIRST** (what can start a derivation):
```
FIRST(F)  = { ( , id }
FIRST(T') = { * , ε }
FIRST(T)  = FIRST(F)  = { ( , id }
FIRST(E') = { + , ε }
FIRST(E)  = FIRST(T)  = { ( , id }
```

**FOLLOW** (what can come immediately after):
```
FOLLOW(E)  = { $ , ) }                       $ because E is the start symbol;
                                              ) from the production F → ( E )
FOLLOW(E') = FOLLOW(E)          = { $ , ) }   E' is last in E → T E'
FOLLOW(T)  = (FIRST(E') − ε) ∪ FOLLOW(E)
           = { + } ∪ { $ , ) } = { + , $ , ) }
FOLLOW(T') = FOLLOW(T)          = { + , $ , ) }
FOLLOW(F)  = (FIRST(T') − ε) ∪ FOLLOW(T)
           = { * } ∪ { + , $ , ) } = { * , + , $ , ) }
```

**Is it LL(1)?** For each pair of alternatives the FIRST sets are disjoint, and for the ε-productions `FIRST(+TE') ∩ FOLLOW(E') = {+} ∩ {$, )} = ∅` and `FIRST(*FT') ∩ FOLLOW(T') = {*} ∩ {+, $, )} = ∅`. **Yes, LL(1)** — which is exactly why this grammar is the textbook one.

**The three FOLLOW rules:**
1. `$ ∈ FOLLOW(start)`
2. For `A → αBβ`: add `FIRST(β) − {ε}` to `FOLLOW(B)`
3. For `A → αB`, or `A → αBβ` where β ⇒* ε: add `FOLLOW(A)` to `FOLLOW(B)`

---

## N6. Detecting ambiguity

**Q.** Show that `E → E + E | E * E | id` is ambiguous.

`id + id * id` has two distinct parse trees:
```
      E                        E
    ╱ │ ╲                    ╱ │ ╲
   E  +  E                  E  *  E
   │    ╱│╲                ╱│╲    │
  id   E * E              E + E  id
       │   │              │   │
      id  id             id  id

  (precedence: * binds tighter)   (precedence: + binds tighter)
```
Two parse trees for one string ⇒ **ambiguous**.

**The fix** is to layer the grammar by precedence, which produces exactly the N5 grammar: `E` handles `+`, `T` handles `*`, `F` handles atoms and parentheses. Left recursion then encodes left associativity.

---

## N7. Counting states after minimisation

**Q.** A DFA over {a,b} accepts strings containing `abb` as a substring. How many states in the minimal DFA?

**Reasoning.** Track how much of the pattern has been matched: nothing, `a`, `ab`, `abb` (accepting, and absorbing).
**Answer: 4 states = pattern length + 1.**

**Generalisation:** "contains a fixed substring of length k" needs **k + 1** states; so does "ends with a fixed string of length k". This is the KMP automaton in disguise.

---

## N8. Closure properties

**Q.** `L1 = { aⁿbⁿcᵐ }` and `L2 = { aⁿbᵐcᵐ }` are both context-free. Is `L1 ∩ L2` context-free?

```
L1 ∩ L2 = { aⁿbⁿcⁿ }
```
which is **not context-free** (provable with the CFL pumping lemma).

**Therefore CFLs are not closed under intersection** — and since they are closed under union, they cannot be closed under complement either, by De Morgan. Regular languages, by contrast, are closed under all of these.

---

## N9. NP-completeness reasoning

**Q.** You must decide whether a graph has a vertex cover of size ≤ k. What is the right response in an interview?

1. **Recognise it:** vertex cover is NP-complete.
2. **Say what that means:** no polynomial-time exact algorithm is known, and finding one would resolve P vs NP.
3. **Give the practical routes:** exact exponential search with pruning for small k (it is fixed-parameter tractable in k, at `O(2ᵏ · n)`); a 2-approximation by repeatedly taking both endpoints of an uncovered edge; or an ILP solver for real instances.

**That structure — identify, state the consequence, offer the practical alternative — is what the question is actually testing.** Reciting the definition of NP-complete is worth far less.

---

## Practice set

1. Minimal DFA states for "number of a's is divisible by 4" over {a,b}?
2. Minimal DFA states for "2nd symbol from the right is 0" over {0,1}?
3. An NFA has 5 states — maximum DFA states after subset construction?
4. Compute FIRST and FOLLOW for `S → aSb | ε`.
5. Is `{ ww : w ∈ {a,b}* }` context-free?
6. Is an ambiguous grammar ever LL(1)?

<details><summary>Answers</summary>

1. **4** — one state per residue class of the a-count.
2. **4** = 2², since the last two symbols must be remembered.
3. **2⁵ = 32**.
4. `FIRST(S) = { a, ε }`; `FOLLOW(S) = { b, $ }`. It is LL(1), since `FIRST(aSb) ∩ FOLLOW(S) = {a} ∩ {b,$} = ∅`.
5. **No.** `{ ww }` is not context-free (a single stack reverses its contents, so it can match `w wᴿ` but not `w w`). Note the contrast: `{ w wᴿ }` **is** context-free.
6. **Never.** Ambiguity forces at least two entries into some parsing-table cell, which is exactly a violation of the LL(1) condition. Left recursion likewise disqualifies a grammar.

</details>
