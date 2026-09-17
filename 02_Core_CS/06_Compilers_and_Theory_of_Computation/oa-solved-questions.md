# Compilers & TOC — Solved OA Questions

> 15 questions in GATE/OA style. This subject is **P3** for most software roles, but it appears in core-CS MCQ sections and the question bank is small and repetitive — an hour here converts directly into marks.

---

**Q1.** Which compiler phase detects a **type mismatch**?
(a) Lexical analysis  (b) Syntax analysis  (c) Semantic analysis  (d) Code generation

<details><summary>Answer</summary>

**(c) Semantic analysis.**

Lexing catches illegal characters; parsing catches structural errors like a missing semicolon; **semantic analysis** catches everything that is well-formed but meaningless — type mismatches, undeclared variables, wrong argument counts.

`int x = "hello";` is lexically fine and syntactically fine. Only the type checker rejects it.
</details>

---

**Q2.** The minimal DFA over {0,1} accepting binary strings divisible by 5 has
(a) 2 states  (b) 5 states  (c) 10 states  (d) 32 states

<details><summary>Answer</summary>

**(b) 5 states.**

Reading most-significant-bit first, appending bit `b` maps value `v` to `2v + b`, so only `v mod 5` matters — five residue classes, five states.

**Divisibility by n always needs exactly n states.** Recognise the family and answer in five seconds.
</details>

---

**Q3.** The minimal DFA over {0,1} accepting strings whose **4th symbol from the right** is 1 has
(a) 4 states  (b) 5 states  (c) 8 states  (d) 16 states

<details><summary>Answer</summary>

**(d) 16 states.**

You cannot know which symbol is fourth-from-last until the input ends, so the automaton must remember the last four symbols — and all 2⁴ histories are pairwise distinguishable.

**"k-th from the right" → 2ᵏ. "k-th from the left" → roughly k + 2.** The distractors here are exactly the left-hand answers.
</details>

---

**Q4.** An NFA with n states converted to a DFA has at most
(a) n states  (b) n² states  (c) 2ⁿ states  (d) n! states

<details><summary>Answer</summary>

**(c) 2ⁿ.**

Each DFA state corresponds to a *subset* of NFA states, and there are 2ⁿ subsets. The bound is tight — families of NFAs exist whose minimal DFA genuinely needs 2ⁿ states — although typical NFAs reach far fewer reachable subsets.
</details>

---

**Q5.** Which language is **regular**?
(a) `{ aⁿbⁿ : n ≥ 0 }`  (b) `{ aⁿbᵐ : n, m ≥ 0 }`  (c) `{ ww : w ∈ {a,b}* }`  (d) `{ aⁿbⁿcⁿ }`

<details><summary>Answer</summary>

**(b).**

`{aⁿbᵐ}` is just `a*b*` — no counting relationship between n and m, so a finite automaton suffices.

(a) is context-free but not regular (requires unbounded counting). (c) is not even context-free — a stack reverses, so it handles `wwᴿ` but not `ww`. (d) is context-sensitive.

**The decisive question:** does the language require *remembering an unbounded count*? If yes, it is not regular.
</details>

---

**Q6.** The pumping lemma can be used to prove that a language
(a) is regular  (b) is **not** regular  (c) is context-free  (d) is decidable

<details><summary>Answer</summary>

**(b).**

It states a property that *every* regular language has, so exhibiting a violation proves non-regularity. Satisfying the lemma proves nothing — there are non-regular languages that satisfy it. To prove a language *is* regular, construct a DFA, a regex, or use Myhill–Nerode.
</details>

---

**Q7.** Which of the following is **not** closed under intersection?
(a) Regular languages  (b) Context-free languages  (c) Recursive languages  (d) Both (b) and (c)

<details><summary>Answer</summary>

**(b) Context-free languages.**

`{aⁿbⁿcᵐ} ∩ {aⁿbᵐcᵐ} = {aⁿbⁿcⁿ}`, which is not context-free. Since CFLs *are* closed under union, De Morgan then shows they are not closed under complement either.

Regular and recursive languages are closed under union, intersection and complement.
</details>

---

**Q8.** An **ambiguous** grammar
(a) can always be made LL(1)
(b) can never be LL(1)
(c) is always left-recursive
(d) has no parse tree

<details><summary>Answer</summary>

**(b).**

Ambiguity means some string has two leftmost derivations, which forces two entries into one LL(1) parsing-table cell — the definition of not being LL(1).

(a) is wrong because some languages are *inherently* ambiguous. (c) is unrelated — left recursion also disqualifies LL(1), but it is a separate property. (d) is backwards: ambiguity means *more than one* parse tree.
</details>

---

**Q9.** Order these parsers from least to most powerful.
(a) LL(1) < SLR(1) < LALR(1) < CLR(1)
(b) CLR(1) < LALR(1) < SLR(1) < LL(1)
(c) LL(1) < LALR(1) < SLR(1) < CLR(1)
(d) They are all equally powerful

<details><summary>Answer</summary>

**(a).**

LL(1) ⊂ SLR(1) ⊂ LALR(1) ⊂ CLR(1). **LALR(1)** is what `yacc` and `bison` produce: nearly CLR's power with the state count of SLR, which is the practical sweet spot.
</details>

---

**Q10.** For the grammar `S → aSb | ε`, FOLLOW(S) is
(a) { a }  (b) { b }  (c) { b, $ }  (d) { a, b, $ }

<details><summary>Answer</summary>

**(c) { b, $ }.**

`$` because S is the start symbol. `b` because in `S → aSb`, the symbol immediately following S is `b`.

Apply the three FOLLOW rules mechanically — `$` in FOLLOW(start); `FIRST(β) − ε` for what follows; FOLLOW(A) when the non-terminal is last or the tail can vanish — and this family becomes routine.
</details>

---

**Q11.** Bottom-up parsing constructs
(a) a leftmost derivation
(b) a **reverse** rightmost derivation
(c) a rightmost derivation
(d) no derivation

<details><summary>Answer</summary>

**(b).**

Shift-reduce parsing reduces handles from the leaves upward, and the sequence of reductions read backwards is a rightmost derivation. Top-down parsing produces a leftmost derivation directly.

The word **reverse** is what the question is testing — (c) without it is the distractor.
</details>

---

**Q12.** The halting problem is
(a) decidable  (b) undecidable  (c) NP-complete  (d) in P

<details><summary>Answer</summary>

**(b) undecidable.**

Suppose `H(p, x)` decides halting. Build `D(p)`: if `H(p, p)` says p halts on p, loop forever; otherwise halt. Now ask what `D(D)` does — either answer contradicts itself. Therefore H cannot exist.

(c) confuses undecidability with intractability: NP-complete problems *are* decidable, just not known to be efficiently solvable. That distinction is the real content of this question.
</details>

---

**Q13.** A problem is **NP-complete** if it
(a) is in NP  (b) is NP-hard  (c) is in NP **and** NP-hard  (d) is in P

<details><summary>Answer</summary>

**(c).**

NP-hard alone does not require membership in NP — the halting problem is NP-hard but not in NP, because it is not even decidable. NP-complete requires both: verifiable in polynomial time, and at least as hard as everything in NP.
</details>

---

**Q14.** Which was the **first** problem proved NP-complete?
(a) TSP  (b) SAT  (c) Vertex cover  (d) Clique

<details><summary>Answer</summary>

**(b) SAT** — the Cook–Levin theorem.

Every other NP-completeness proof descends from it by reduction, which is why SAT is the one to name.
</details>

---

**Q15.** Which optimisation replaces a multiplication inside a loop with repeated addition?
(a) Constant folding  (b) Dead code elimination  (c) **Strength reduction**  (d) Loop unrolling

<details><summary>Answer</summary>

**(c) strength reduction.**

Replacing an expensive operation with a cheaper equivalent — `i * 4` becoming a running addition, or `x * 2` becoming `x << 1`.

(a) evaluates constant expressions at compile time. (b) removes unreachable or unused code. (d) replicates the loop body to cut loop-control overhead.
</details>

---

## Scoring

| Score /15 | Reading |
|---|---|
| 12+ | Enough for any OA that includes this material |
| 8–11 | Drill the DFA state-count families and the parser hierarchy |
| < 8 | Given this is P3, spend the time on OS, DBMS or OOP instead unless your target companies test GATE-style material |

**Honest prioritisation:** if your `00_Start_Here/syllabus-checklist.md` still has P1 rows below 4, close those first. This folder is worth an hour, not a week.
