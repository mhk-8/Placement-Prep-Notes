# Compilers & TOC — Rapid Fire Q&A

**1. What are the compiler phases?** Lexical, syntax, semantic, IR generation, optimisation, code generation — with the symbol table and error handler spanning all of them.

**2. Front end vs back end?** The front end depends on the source language and not the target; the back end is the reverse. That split is why LLVM supports many languages and many targets through one shared optimiser.

**3. What does each phase catch?** Lexing catches illegal characters, parsing catches structural errors like a missing bracket, semantic analysis catches type mismatches and undeclared names.

**4. Why is `aⁿbⁿ` not regular?** A finite automaton has finitely many states and cannot count arbitrarily far, so it cannot remember how many a's it saw. The pumping lemma formalises exactly that.

**5. Are NFAs more powerful than DFAs?** No — they recognise the same class. An NFA is often exponentially smaller, since subset construction can produce up to 2ⁿ DFA states.

**6. What is the pumping lemma for?** Proving a language is *not* regular. Satisfying it proves nothing, because non-regular languages can also satisfy it.

**7. What does Myhill–Nerode say?** A language is regular exactly when it has finitely many distinguishability classes, and that count is the minimal DFA's state number. It is the constructive counterpart to the pumping lemma.

**8. What makes a grammar ambiguous, and why does it matter?** Some string has two parse trees, so the meaning is undetermined — `id + id * id` under a flat expression grammar. It matters because a parser must pick one, and ambiguity means no deterministic parser exists.

**9. How do you remove ambiguity from an expression grammar?** Layer it by precedence — separate non-terminals for additive, multiplicative and atomic levels — and use left recursion to encode left associativity.

**10. Why must left recursion be eliminated for top-down parsing?** A recursive-descent parser for `A → Aα` calls itself with no input consumed and loops forever. Bottom-up parsers handle left recursion naturally.

**11. What are FIRST and FOLLOW for?** FIRST tells the parser which production a lookahead token can begin; FOLLOW tells it when an ε-production is valid. Together they fill the LL(1) table.

**12. When is a grammar LL(1)?** When the alternatives for every non-terminal have disjoint FIRST sets, and any ε-alternative's FOLLOW set is disjoint from the others' FIRST sets — equivalently, no table cell holds two entries.

**13. Order the parsers by power.** LL(1) ⊂ SLR(1) ⊂ LALR(1) ⊂ CLR(1). LALR(1) is what real parser generators emit, because it is nearly as powerful as CLR with far fewer states.

**14. What is three-address code?** An intermediate form where each instruction has one operator and at most three operands, like `t1 = a + b`. It is simple enough for systematic optimisation and register allocation.

**15. Name some optimisations.** Constant folding and propagation, common subexpression elimination, dead code elimination, loop-invariant code motion, strength reduction, inlining.

**16. What is in an activation record?** Return address, saved registers, parameters, locals, and links to the caller's frame. Recursion works because each call gets a fresh one.

**17. Static vs dynamic scoping?** Static resolves a name by the program's lexical nesting, visible in the source; dynamic resolves by the runtime call chain. Essentially every modern language uses static scoping because it is predictable.

**18. Static vs dynamic typing, and strong vs weak?** Static means types are checked at compile time; dynamic at run time. Strong versus weak is a separate axis about implicit coercion — Python is dynamically typed and strongly typed.

**19. What is the halting problem and why does it matter?** No program can decide for every program and input whether it halts, proved by diagonalisation. It is why static analysers are necessarily conservative and give false positives.

**20. What is Rice's theorem?** Every non-trivial semantic property of programs is undecidable. It generalises the halting result and explains why "does this program ever do X" is unanswerable in general.

**21. Define P, NP, NP-hard and NP-complete.** P is solvable in polynomial time; NP is verifiable in polynomial time; NP-hard is at least as hard as everything in NP; NP-complete is both in NP and NP-hard.

**22. Is undecidable the same as intractable?** No. NP-complete problems are decidable but believed to require exponential time; undecidable problems have no algorithm at all, at any cost.

**23. What do you do when you recognise an NP-complete problem?** Stop seeking an exact polynomial algorithm and discuss approximation, heuristics, fixed-parameter tractability, or exploiting structure in real inputs. Saying this is worth far more than the definition.

**24. Where has any of this shown up in your own work?** *(Regex engines, a parser or DSL you wrote, a state machine in a project, or recognising a scheduling problem as NP-hard and choosing a heuristic. One concrete instance is enough.)*
