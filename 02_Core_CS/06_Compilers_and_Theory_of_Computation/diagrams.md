# Compilers & TOC — Diagrams

---

## 1. Compiler pipeline

```
   source code
       │
  ┌────▼─────────┐
  │ LEXICAL      │  chars → tokens          illegal character
  ├──────────────┤
  │ SYNTAX       │  tokens → parse tree     missing ';', unbalanced ()
  ├──────────────┤   ┌───────────────┐
  │ SEMANTIC     │   │ SYMBOL TABLE  │      type mismatch, undeclared var
  ├──────────────┤   │      +        │
  │ IR GEN       │   │ ERROR HANDLER │      three-address code
  ├──────────────┤   │               │
  │ OPTIMISE     │   │  (span every  │      folding, CSE, dead code, LICM
  ├──────────────┤   │    phase)     │
  │ CODE GEN     │   └───────────────┘      register allocation
  └────┬─────────┘
       ▼
   target code

   FRONT END: lexical → IR     (source-dependent, target-independent)
   BACK END:  optimise → code  (target-dependent)
```

## 2. Chomsky hierarchy

```
   ┌──────────────────────────────────────────────────────┐
   │ TYPE 0  Unrestricted        Turing machine           │
   │ ┌──────────────────────────────────────────────────┐ │
   │ │ TYPE 1  Context-sensitive  Linear bounded automaton│ │  aⁿbⁿcⁿ
   │ │ ┌──────────────────────────────────────────────┐ │ │
   │ │ │ TYPE 2  Context-free      Pushdown automaton │ │ │  aⁿbⁿ
   │ │ │ ┌──────────────────────────────────────────┐ │ │ │
   │ │ │ │ TYPE 3  Regular          Finite automaton│ │ │ │  a*b*
   │ │ │ └──────────────────────────────────────────┘ │ │ │
   │ │ └──────────────────────────────────────────────┘ │ │
   │ └──────────────────────────────────────────────────┘ │
   └──────────────────────────────────────────────────────┘

   each level needs strictly more memory than the one inside it:
   none → one stack → bounded tape → unbounded tape
```

## 3. DFA for "binary value divisible by 3"

```
              0                 0
           ┌─────┐           ┌─────┐
           │     ▼           │     ▼
        ┌──────┐   1      ┌──────┐   1   ┌──────┐
   ──►  │ q0 ✓ │ ───────► │  q1  │ ─────►│  q2  │
        └──────┘          └──────┘       └──────┘
           ▲   ▲              │              │  │
           │   └──────1───────┘              │  │
           └─────────────0──────────────────-┘  │
                         ▲                      │
                         └───────1──────────────┘

   state = value mod 3;  on bit b:  v → (2v + b) mod 3
   q0 is both start and the only accepting state
```

## 4. Language classes and their closure properties

```
                     ∪   ·   *   ∩   complement   reversal
   Regular           ✔   ✔   ✔   ✔       ✔            ✔
   Context-free      ✔   ✔   ✔   ✘       ✘            ✔
   Recursive         ✔   ✔   ✔   ✔       ✔            ✔
   Recursively enum. ✔   ✔   ✔   ✔       ✘            ✔

   The CFL gaps are the exam content:
     {aⁿbⁿcᵐ} ∩ {aⁿbᵐcᵐ} = {aⁿbⁿcⁿ}   ← not context-free
```

## 5. Parser power hierarchy

```
   ┌────────────────────────────────────────────────┐
   │              CLR(1)   (canonical LR)           │
   │ ┌────────────────────────────────────────────┐ │
   │ │        LALR(1)   ← yacc / bison            │ │
   │ │ ┌────────────────────────────────────────┐ │ │
   │ │ │          SLR(1)                        │ │ │
   │ │ │ ┌────────────────────────────────────┐ │ │ │
   │ │ │ │  LL(1)  ← recursive descent        │ │ │ │
   │ │ │ └────────────────────────────────────┘ │ │ │
   │ │ └────────────────────────────────────────┘ │ │
   │ └────────────────────────────────────────────┘ │
   └────────────────────────────────────────────────┘

   TOP-DOWN  (LL)   leftmost derivation, cannot handle left recursion
   BOTTOM-UP (LR)   REVERSE rightmost derivation, handles left recursion
```

## 6. Complexity classes (assuming P ≠ NP)

```
   ┌──────────────────────────────────────────────┐
   │              NP-hard                         │
   │   ┌──────────────────────┐                   │
   │   │   NP-complete        │   halting problem │
   │   │   SAT, 3-SAT,        │   (NP-hard but    │
   │   │   clique, TSP,       │    UNDECIDABLE)   │
   │   │   vertex cover       │                   │
   │   └──────────────────────┘                   │
   └────────────┬─────────────────────────────────┘
                │
   ┌────────────▼──────────────┐
   │            NP             │  verifiable in poly time
   │   ┌──────────────────┐    │
   │   │        P         │    │  solvable in poly time
   │   └──────────────────┘    │
   └───────────────────────────┘

   NP-complete = in NP AND NP-hard
   NP-hard alone does NOT imply membership in NP
```
