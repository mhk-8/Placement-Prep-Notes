# Design Patterns — Flashcards

## Questions

**Creational**
1. Give four reasons Singleton is an anti-pattern, and the mature alternative.
2. Describe double-checked locking and why Java needs `volatile`.
3. What Open/Closed violation does Factory Method fix, and how does the registry variant improve it?
4. Factory Method vs Abstract Factory — the difference, and the invariant Abstract Factory protects.
5. What is Abstract Factory's weakness?
6. What problem does Builder solve, and what three benefits does it give?
7. Why is Builder less necessary in Python?
8. What is the critical decision in Prototype?
9. Why does a connection pool exist? Give the numbers and three pitfalls.

**Structural**
10. Adapter vs Decorator, in one sentence.
11. Facade vs Adapter.
12. Decorator vs Proxy, given identical structure.
13. Why does Decorator give n classes where subclassing gives 2ⁿ?
14. Name the four kinds of Proxy with an example each.
15. What tension does Composite create with Interface Segregation?
16. What explosion does Bridge solve, and what is the resulting class count?
17. Distinguish intrinsic and extrinsic state; give the Java example and the bug it explains.

**Behavioural**
18. What requirement wording signals Strategy?
19. Strategy vs State — who decides, and do implementations know each other?
20. Give three Observer pitfalls and say which one a queue solves.
21. What conditional problem does State fix?
22. What four capabilities does Command provide, and which system-design pattern is it?
23. Where does Chain of Responsibility appear in every web framework, and what is its failure mode?
24. Template Method vs Strategy — coupling, binding time, and which is preferred.
25. What happens when you mutate a collection while iterating it, in Java and in Python?
26. What is Mediator's risk?
27. Why does Visitor make adding operations easy and adding types hard?

**Applying them**
28. Which pattern is an API gateway? A service-mesh sidecar? Event sourcing? A circuit breaker?
29. Give five reasons **not** to add a pattern.
30. Give the sentence you would say about when you would *not* extract an interface.
31. Name four anti-patterns and their fixes.

---

## Answers

1. Global mutable state; hidden dependencies (the constructor does not declare it); destroyed testability (no substitution, state leaks between tests); hard to make thread-safe. The alternative: create one instance and **inject** it.
2. Check the field, take the lock only if null, check again inside the lock. Java needs `volatile` because instruction reordering can otherwise publish a partially-constructed object to another thread.
3. A `switch` on a type code that must be edited for every new type. The registry variant lets types register themselves, so the factory is itself closed for modification.
4. Factory Method creates **one** product; Abstract Factory creates a **consistent family**. The invariant: you cannot accidentally mix a Mac button with a Windows checkbox.
5. Adding a new **product type** to the family requires changing the abstract interface and every implementation. It is open to new families, closed to new product types.
6. The telescoping constructor. Benefits: readable call sites (each argument labelled), validation in `build()` before the object exists, and immutability.
7. Keyword arguments and default values already give readable, self-documenting call sites.
8. Shallow versus deep copy — a shallow clone shares nested objects, so mutating one clone is visible through the others.
9. Opening a connection costs a TCP handshake plus authentication, and each connection consumes server memory; 100 instances × 50 connections = 5,000 connections to the database. Pitfalls: objects must be reset before reuse or state leaks; too small a pool becomes a bottleneck; a leaked object shrinks the pool until everything blocks.
10. Adapter **changes the interface** and keeps behaviour; Decorator **keeps the interface** and adds behaviour.
11. Facade simplifies **many** interfaces into one convenient one; Adapter converts **one** interface into another specific one.
12. Intent. Decorator adds behaviour the caller wanted; Proxy controls access the caller may not know about (lazy, remote, cached, permissioned).
13. Because each decorator *is* the interface it wraps, so n options compose in any order and depth as n classes, rather than requiring a class per combination.
14. Virtual (lazy-loading a large image), protection (permission check before delegating), remote (an RPC stub), caching (memoisation, an HTTP cache).
15. If the composite interface declares `add`/`remove`, leaves must implement them meaninglessly. Declaring them only on Composite means clients sometimes have to know the difference, weakening the uniformity that was the point.
16. m × n classes when two dimensions vary independently (shapes × renderers). Bridge gives **m + n**.
17. Intrinsic is shared and immutable (a glyph shape, a tree texture); extrinsic is per-instance (position, colour). Java caches `Integer` −128…127, which is why `==` on boxed integers is true in that range and false outside it.
18. "different", "based on", "configurable", "types of", "policy".
19. Strategy: the **client** chooses, it rarely changes during the object's life, and strategies do not know about each other. State: the **object** transitions itself, and states know their successors.
20. Lapsed listener (a memory leak from never unsubscribing); undefined notification order; synchronous notification letting one slow observer block the subject — which a **queue** solves.
21. A growing `if/elif` chain on a state field, edited by every new state and with transitions scattered through it.
22. Undo/redo, queuing, logging and replay, and macro commands. It is **event sourcing** at system scale.
23. The middleware pipeline — auth, rate limit, logging, routing. Its failure mode is a request falling off the end unhandled, fixed by terminating the chain with a default handler.
24. Template Method uses **inheritance**, fixed at compile time, with fragile-base-class coupling. Strategy uses **composition**, swappable at runtime and independently testable. Prefer Strategy unless the skeleton's ordering is an invariant subclasses must not break.
25. Java throws `ConcurrentModificationException`; Python silently skips elements, which is worse because it fails quietly.
26. It accumulates coordination logic and becomes a god object.
27. A new operation is a new visitor class touching nothing existing; a new node type requires a new method on **every** visitor. It suits stable hierarchies with growing operation sets, like a compiler's AST.
28. API gateway = Facade. Sidecar = Proxy (plus Decorator). Event sourcing = Command. Circuit breaker = State.
29. Only one implementation and no prospect of a second; the requirements have not suggested that axis will vary; the simple version is more readable; you are in a timed round where finishing matters most; the pattern is being used as vocabulary rather than to solve a problem.
30. "I'd put pricing behind a Strategy because the requirements already mention weekday and weekend rates. I wouldn't abstract the payment gateway yet — there's only one, and I'd extract the interface when a second appears."
31. God object → SRP. Anaemic domain model → Information Expert. Singleton abuse → inject one instance. Speculative generality → YAGNI, extract when the second case arrives. (Also: pattern soup, poltergeist classes, distributed monolith.)
