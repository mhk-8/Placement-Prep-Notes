# Compilers & TOC — Flashcards

## Questions

1. List the compiler phases in order, with one error each catches.
2. What distinguishes the front end from the back end?
3. Give the Chomsky hierarchy: grammar, recogniser and an example language per level.
4. Why is `aⁿbⁿ` not regular? Why is `aⁿbⁿcⁿ` not context-free?
5. Are NFAs more powerful than DFAs, and what is the state blow-up on conversion?
6. Minimal DFA size for "divisible by n"? For "k-th symbol from the right"? For "contains a length-k substring"?
7. State the pumping lemma and what it can and cannot prove.
8. What does Myhill–Nerode say?
9. Which closure properties do regular languages have that CFLs do not?
10. Define grammar ambiguity and give the classic example plus its fix.
11. Why must left recursion be removed for top-down parsing?
12. Give the three FOLLOW computation rules.
13. State the LL(1) condition.
14. Order LL(1), SLR(1), LALR(1), CLR(1) by power, and say which one yacc uses.
15. What derivation does bottom-up parsing produce?
16. Name four compiler optimisations.
17. Sketch the halting-problem proof.
18. What does Rice's theorem generalise?
19. Define P, NP, NP-hard, NP-complete, and name the first NP-complete problem.
20. Is every NP-hard problem in NP?

---

## Answers

1. Lexical (illegal character) → syntax (missing semicolon) → semantic (type mismatch) → IR generation → optimisation → code generation. The symbol table and error handler span all phases.
2. The front end is source-language dependent and target independent (lexing through IR); the back end is target dependent (optimisation and code generation). The split lets one optimiser serve many languages and targets.
3. Type 3 regular / finite automaton / `a*b*`. Type 2 context-free / pushdown automaton / `aⁿbⁿ`. Type 1 context-sensitive / linear bounded automaton / `aⁿbⁿcⁿ`. Type 0 unrestricted / Turing machine.
4. A finite automaton cannot count unboundedly, so it cannot match the number of b's to the number of a's. A pushdown automaton has one stack, which can match two counts but not three.
5. No — they recognise exactly the same class. Subset construction can produce up to 2ⁿ DFA states from an n-state NFA.
6. Divisible by n → **n** states. k-th from the right → **2ᵏ**. Contains (or ends with) a fixed length-k string → **k + 1**.
7. Every regular L has a pumping length p such that any `s ∈ L` with `|s| ≥ p` splits as `xyz` with `|xy| ≤ p`, `|y| ≥ 1`, and `xyⁱz ∈ L` for all i ≥ 0. It can prove a language is **not** regular; it can never prove one is.
8. A language is regular iff it has finitely many distinguishability classes, and that count equals the minimal DFA's state count.
9. Regular languages are closed under intersection and complement; context-free languages are not. `{aⁿbⁿcᵐ} ∩ {aⁿbᵐcᵐ} = {aⁿbⁿcⁿ}` is the standard witness.
10. A string with more than one parse tree. `E → E + E | E * E | id` cannot fix precedence for `id + id * id`; the fix is to layer the grammar into E, T and F by precedence level.
11. `A → Aα` makes a recursive-descent parser recurse without consuming input, looping forever. Bottom-up parsers handle it naturally.
12. `$ ∈ FOLLOW(start)`. For `A → αBβ`, add `FIRST(β) − {ε}` to `FOLLOW(B)`. For `A → αB`, or when β can derive ε, add `FOLLOW(A)` to `FOLLOW(B)`.
13. For every pair of alternatives `A → α | β`: `FIRST(α) ∩ FIRST(β) = ∅`, and if β derives ε then `FIRST(α) ∩ FOLLOW(A) = ∅`. Equivalently, no parsing-table cell holds two entries.
14. LL(1) ⊂ SLR(1) ⊂ LALR(1) ⊂ CLR(1). yacc and bison generate **LALR(1)**.
15. A **reverse** rightmost derivation — the reductions read backwards give a rightmost derivation.
16. Constant folding, common subexpression elimination, dead code elimination, loop-invariant code motion (also strength reduction and inlining).
17. Assume `H(p,x)` decides halting. Define `D(p)` = loop if `H(p,p)` says halt, else halt. Ask what `D(D)` does — both branches contradict. Therefore H cannot exist.
18. It generalises undecidability from halting to **every non-trivial semantic property** of programs.
19. P: solvable in polynomial time. NP: verifiable in polynomial time. NP-hard: at least as hard as every problem in NP. NP-complete: in NP and NP-hard. The first was **SAT** (Cook–Levin).
20. No. The halting problem is NP-hard but undecidable, so it is not in NP at all.
