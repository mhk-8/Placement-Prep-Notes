# Compilers & Theory of Computation — Concepts

## 1. Core idea in 3 lines
A compiler turns source text into machine code through a pipeline of increasingly abstract representations, and each phase corresponds to a class in the Chomsky hierarchy: lexing is regular, parsing is context-free, semantics needs more. Theory of computation asks what *can* be computed at all, and where the boundary of "efficiently" sits. This is the most academic folder here — **P3** for most software roles, but it appears in GATE-style OA sections and in core-CS panels.

---

## 2. Compiler phases

```
source → LEXICAL → SYNTAX → SEMANTIC → IR GEN → OPTIMISE → CODE GEN → target
         tokens     parse     type       three-   machine-   assembly
                    tree      checked    address  independent
                              tree       code     + dependent
```

| Phase | Input → output | Errors it catches |
|---|---|---|
| **Lexical analysis** (scanner) | characters → tokens | illegal character, malformed literal |
| **Syntax analysis** (parser) | tokens → parse tree | missing semicolon, unbalanced brackets |
| **Semantic analysis** | parse tree → annotated tree | type mismatch, undeclared variable, wrong argument count |
| **Intermediate code generation** | → three-address code / SSA | — |
| **Optimisation** | IR → better IR | — |
| **Code generation** | IR → assembly | register allocation |

**The symbol table** and **error handling** span every phase rather than sitting in one.

**Front end vs back end:** the front end (lexing through IR) is source-language dependent and target independent; the back end is the reverse. This split is why LLVM can support many languages and many targets with one optimiser in the middle.

**Three-address code** is the canonical IR: each instruction has at most one operator and three operands, e.g. `t1 = a + b`. It makes optimisation and register allocation tractable.

**Optimisations worth naming:** constant folding, constant propagation, common subexpression elimination, dead code elimination, loop-invariant code motion, strength reduction (replacing multiplication with addition in a loop), and inlining.

---

## 3. The Chomsky hierarchy

| Type | Grammar | Recogniser | Example language |
|---|---|---|---|
| 3 | Regular | Finite automaton (DFA/NFA) | `a*b*` |
| 2 | Context-free | Pushdown automaton | `aⁿbⁿ` |
| 1 | Context-sensitive | Linear bounded automaton | `aⁿbⁿcⁿ` |
| 0 | Unrestricted | Turing machine | recursively enumerable |

Each class is strictly contained in the next. The two boundaries that get tested: **`aⁿbⁿ` is context-free but not regular** (a finite automaton cannot count unboundedly), and **`aⁿbⁿcⁿ` is not context-free** (a single stack cannot match three counts).

---

## 4. Regular languages and finite automata

**DFA:** exactly one transition per (state, symbol). **NFA:** zero or more, plus ε-moves. They are **equally powerful** — every NFA has an equivalent DFA — but the subset construction can produce up to **2ⁿ** states from an n-state NFA.

**Regular expressions, regular grammars, DFAs and NFAs all describe exactly the regular languages.** Conversions: regex → NFA (Thompson's construction), NFA → DFA (subset construction), DFA → minimal DFA (Hopcroft's or the table-filling algorithm), DFA → regex (state elimination).

**Closure properties.** Regular languages are closed under union, concatenation, Kleene star, complement, intersection, difference and reversal. Context-free languages are closed under union, concatenation and star, **but not** under intersection or complement — which is why `aⁿbⁿcⁿ` (the intersection of two CFLs) is not context-free.

**Minimal DFA state counts** — the pattern behind most OA questions:

| Language over {0,1} | Minimal states |
|---|---|
| Number of `a`s divisible by k | k |
| Binary value divisible by n | n |
| Ends with a fixed string of length k | k + 1 |
| k-th symbol **from the right** is fixed | **2ᵏ** |
| Contains a fixed substring of length k | k + 1 |
| Even number of 0s **and** even number of 1s | 4 |

The `2ᵏ` case is the notable one: you must remember the last k symbols, and every combination is a distinguishable state.

**The pumping lemma for regular languages:** if L is regular, there is a pumping length p such that every string `s ∈ L` with `|s| ≥ p` can be split as `s = xyz` with `|xy| ≤ p`, `|y| ≥ 1`, and `xyⁱz ∈ L` for all `i ≥ 0`. It is used *only* to prove a language is **not** regular — you pick an adversarial string, and show some `i` breaks membership. It can never prove a language *is* regular.

**Myhill–Nerode** gives the other direction: L is regular iff its number of distinguishability classes is finite, and that number is exactly the minimal DFA's state count.

---

## 5. Context-free grammars and parsing

A CFG is a set of productions `A → α` where A is a single non-terminal.

**Ambiguity:** a grammar is ambiguous if some string has more than one parse tree (equivalently, more than one leftmost derivation). The classic case is `E → E + E | E * E | id`, which cannot decide precedence. Fixing it means layering the grammar by precedence level, which is why the standard expression grammar has separate `E`, `T` and `F` levels. Some languages are **inherently ambiguous** — no unambiguous grammar exists — and ambiguity is undecidable in general.

**Left recursion** (`A → Aα`) makes top-down parsing loop forever and must be eliminated; **left factoring** removes a common prefix so the parser can choose on one lookahead token.

**Parsing strategies**

| | Top-down | Bottom-up |
|---|---|---|
| Builds | root → leaves | leaves → root |
| Derivation | leftmost | **reverse** rightmost |
| Methods | recursive descent, LL(1) | LR(0), SLR(1), LALR(1), CLR(1) |
| Power | weaker | stronger |
| Handles left recursion | no | yes |

**Power ordering:** `LL(1) ⊂ SLR(1) ⊂ LALR(1) ⊂ CLR(1)`. LALR(1) is what `yacc` and `bison` generate, because it has nearly CLR power with far fewer states.

**FIRST and FOLLOW** drive LL(1) table construction:
- `FIRST(α)` = the terminals that can begin a string derived from α (plus ε if α can vanish)
- `FOLLOW(A)` = the terminals that can appear immediately after A in some derivation; `$` is in `FOLLOW(start)`

A grammar is **LL(1)** iff, for every pair of productions `A → α | β`, `FIRST(α) ∩ FIRST(β) = ∅`, and if β can derive ε then `FIRST(α) ∩ FOLLOW(A) = ∅`. Equivalently: no cell of the parsing table holds two entries.

**An ambiguous or left-recursive grammar is never LL(1)** — a fast elimination step in MCQs.

---

## 6. Runtime and semantics

**Activation record (stack frame):** return address, saved registers, parameters, local variables, and the dynamic/static links. Static scoping resolves names by the program's lexical nesting; dynamic scoping resolves by the call chain, and is essentially extinct.

**Parameter passing:** call by value (copies), call by reference (aliases), call by value-result (copy in, copy out), call by name (textual substitution, giving the classic Jensen's device).

**Static vs dynamic typing:** static types are checked at compile time (C++, Java), dynamic at run time (Python). **Strong vs weak** is a separate axis about how much implicit coercion is allowed — Python is dynamically *and* strongly typed.

---

## 7. Computability and complexity

**The Church–Turing thesis:** anything effectively computable is computable by a Turing machine.

**Decidable** (recursive): a TM always halts with yes or no. **Semi-decidable** (recursively enumerable): a TM halts on "yes" but may loop forever on "no". **Undecidable:** no such TM exists.

**The halting problem is undecidable** — no program can decide, for every program and input, whether it halts. The proof is diagonalisation: assume `H(p, x)` exists, build `D(p) = if H(p,p) then loop else halt`, and ask what `D(D)` does. **Rice's theorem** generalises this: every non-trivial semantic property of programs is undecidable.

**Complexity classes:**
- **P** — decidable in polynomial time
- **NP** — a proposed solution is *verifiable* in polynomial time
- **NP-hard** — at least as hard as everything in NP (need not be in NP itself)
- **NP-complete** — in NP **and** NP-hard

**SAT was the first proved NP-complete** (Cook–Levin). Others worth recognising: 3-SAT, clique, vertex cover, subset sum, Hamiltonian cycle, TSP (decision version), graph colouring, knapsack (0/1 decision version).

**P vs NP is open.** P ⊆ NP is obvious; whether the containment is strict is the central open problem.

**Why this matters practically:** if you recognise a problem as NP-complete, you stop looking for an exact polynomial algorithm and start discussing approximation, heuristics, or exploiting structure in the inputs. Saying that in an interview is worth far more than reciting the definitions.

---

## 8. Recall questions

1. List the compiler phases and one error each catches.
2. What separates the front end from the back end, and why does the split matter?
3. Give the Chomsky hierarchy with a recogniser and an example language per level.
4. Why is `aⁿbⁿ` not regular, and `aⁿbⁿcⁿ` not context-free?
5. How many DFA states can an n-state NFA require, and why?
6. Give the minimal DFA size for "the k-th symbol from the right is 1", and the reason.
7. State the pumping lemma and what it can and cannot prove.
8. Define ambiguity, and give the classic ambiguous grammar plus its fix.
9. Give the LL(1) condition in terms of FIRST and FOLLOW.
10. Order LL(1), SLR(1), LALR(1) and CLR(1) by power.
11. Sketch the undecidability proof of the halting problem.
12. Define P, NP, NP-hard and NP-complete, and name the first NP-complete problem.
