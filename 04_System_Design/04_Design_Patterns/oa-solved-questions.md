# Design Patterns — Solved OA Questions

> 18 questions in OA/MCQ style. Design-pattern questions appear in core-CS MCQ sections and constantly as follow-ups in LLD rounds. Every option explained.

---

**Q1.** Which pattern defines a family of interchangeable algorithms and makes them swappable at runtime?
(a) State  (b) **Strategy**  (c) Template Method  (d) Command

<details><summary>Answer</summary>

**(b) Strategy.**

The trigger words in a requirement are "different", "based on", "configurable" and "types of".

(a) **State** is structurally identical but differs in intent: the *object* transitions itself and states know their successors, whereas with Strategy the *client* chooses. (c) Template Method fixes the skeleton via inheritance rather than composition. (d) Command encapsulates a *request*, not an algorithm.
</details>

---

**Q2.** The key difference between Strategy and State is
(a) State uses inheritance, Strategy uses composition
(b) **In Strategy the client chooses; in State the object transitions itself and states know the next state**
(c) Strategy is creational
(d) There is no difference

<details><summary>Answer</summary>

**(b).**

They are structurally the same — an interface with implementations, held by a context object. The distinction is **who drives the change** and whether the implementations know about each other.

"Weekday versus weekend pricing" is Strategy. "Ticket: issued → paid → exited" is State, because each state decides what comes next.
</details>

---

**Q3.** Adapter differs from Decorator in that
(a) Adapter adds behaviour; Decorator changes the interface
(b) **Adapter changes the interface and keeps behaviour; Decorator keeps the interface and adds behaviour**
(c) They are the same
(d) Adapter is behavioural

<details><summary>Answer</summary>

**(b).**

An adapter exists because two interfaces do not match — it translates. A decorator exists because you want extra behaviour — it wraps something and presents **the same** interface, which is precisely what allows decorators to nest arbitrarily.

(a) is the statement reversed, and is the intended distractor.
</details>

---

**Q4.** `new BufferedReader(new InputStreamReader(new FileInputStream(f)))` is an example of
(a) Adapter  (b) **Decorator**  (c) Facade  (d) Proxy

<details><summary>Answer</summary>

**(b) Decorator** — the canonical example in the Java standard library.

Each wrapper is itself a `Reader` and adds behaviour (buffering, character decoding) while preserving the interface, which is why they compose in any order and depth.

**There is a nuance worth knowing:** `InputStreamReader` converts bytes to characters, so it is arguably an **Adapter** by intent. Java's I/O library is usually cited as Decorator overall, and noticing the nuance is a plus rather than a trap.
</details>

---

**Q5.** Decorator and Proxy have identical structure. The difference is
(a) the number of wrappers  (b) **intent: Decorator adds behaviour the caller wants; Proxy controls access**  (c) Proxy is creational  (d) Decorator cannot nest

<details><summary>Answer</summary>

**(b) intent.**

A decorator's added behaviour is the point, and the caller chose it. A proxy's control is often invisible: the caller may not know the object is lazily loaded, remote, cached or permission-checked.

**This pair is the clearest example of the general truth that patterns are distinguished by intent, not by class diagram.**
</details>

---

**Q6.** An API gateway that fans one client call out to five microservices and aggregates the results is
(a) Adapter  (b) Proxy  (c) **Facade**  (d) Mediator

<details><summary>Answer</summary>

**(c) Facade.**

It provides one simplified entry point over a complex subsystem, so the client depends on one interface instead of five.

(b) is close and partly true — a gateway also proxies — but the *aggregating and simplifying* role is Facade. (d) Mediator coordinates peers who would otherwise know each other; the services here do not.
</details>

---

**Q7.** Which is **not** a kind of Proxy?
(a) Virtual  (b) Protection  (c) Remote  (d) **Abstract**

<details><summary>Answer</summary>

**(d).**

The four are **virtual** (delay expensive creation), **protection** (access control), **remote** (represent an object elsewhere — RPC stubs) and **caching** (return a stored result).

"Abstract Factory" is a different pattern; "Abstract Proxy" is not a thing.
</details>

---

**Q8.** Factory Method differs from Abstract Factory in that
(a) Factory Method is structural
(b) **Factory Method creates one product; Abstract Factory creates a consistent family**
(c) Abstract Factory cannot be subclassed
(d) They are identical

<details><summary>Answer</summary>

**(b).**

Abstract Factory's value is the **invariant it protects**: you cannot accidentally combine a Mac button with a Windows checkbox, because the family is chosen once at the factory.

**Its weakness, worth adding:** adding a new *product type* (a `Slider`) requires changing the abstract interface and every implementation. It is open to new families and closed to new product types.
</details>

---

**Q9.** The Builder pattern primarily solves
(a) creating one instance
(b) **the telescoping constructor: many optional parameters, unreadable call sites, no validation before construction**
(c) cloning objects
(d) interface mismatch

<details><summary>Answer</summary>

**(b).**

`new Pizza(12, "thin", true, false, true, false, true, 3)` is unreadable and un-extendable. Builder names each argument at the call site, validates in `build()`, and lets the resulting object be immutable.

**Worth noting:** Builder is far less necessary in Python, where keyword arguments and defaults already give readable call sites.
</details>

---

**Q10.** Singleton is often called an anti-pattern because
(a) it is slow
(b) **it is global mutable state that hides dependencies and destroys testability**
(c) it cannot be implemented safely
(d) it uses too much memory

<details><summary>Answer</summary>

**(b).**

Three concrete harms: a class using a singleton does not declare that dependency, so its constructor lies about what it needs; tests cannot substitute a double, and state leaks between tests; and naive lazy initialisation is racy.

**The mature answer:** if you need exactly one instance, create one and **inject** it. You keep the single-instance property and lose every disadvantage.
</details>

---

**Q11.** Naive lazy Singleton initialisation is thread-unsafe. The standard fix is
(a) a bigger lock  (b) **double-checked locking, with a volatile field in Java**  (c) eager initialisation only  (d) it cannot be fixed

<details><summary>Answer</summary>

**(b).**

Check, lock, check again — so the lock is taken only on the first call.

**The `volatile` detail is what the question is really testing:** without it, instruction reordering can publish a partially-constructed object, and another thread sees a non-null reference to an object whose constructor has not finished.

(c) eager initialisation is also correct and simpler, at the cost of constructing even when unused. In Python, a module-level instance is idiomatic and already thread-safe.
</details>

---

**Q12.** In Observer, the "lapsed listener" problem is
(a) observers being notified twice
(b) **an observer never unsubscribing, so the subject's reference keeps it alive — a memory leak**
(c) notification order being undefined
(d) the subject failing

<details><summary>Answer</summary>

**(b).**

The subject holds strong references to its observers, so an observer that is never unsubscribed is never collected — and it keeps being notified.

Fixes: weak references, explicit and reliable unsubscription, or lifecycle-scoped subscriptions.

(c) is a **different** real pitfall — notification order is undefined, so observers must not depend on ordering. The third is that synchronous notification lets one slow observer block the subject, which is what moving to a queue solves.
</details>

---

**Q13.** Middleware — auth, then rate limiting, then validation, then the handler — is an instance of
(a) Decorator  (b) **Chain of Responsibility**  (c) Mediator  (d) Template Method

<details><summary>Answer</summary>

**(b) Chain of Responsibility.**

Each handler either handles the request or passes it on, and handlers are added or reordered without touching each other.

(a) Decorator is genuinely close — middleware is sometimes implemented as nested decorators — but the defining feature here is that a handler may **terminate** the chain (auth rejecting a request), which is Chain of Responsibility's characteristic behaviour.

**Its failure mode:** a request falling off the end unhandled. Terminate with a default handler.
</details>

---

**Q14.** Command enables undo because
(a) it stores the object's state
(b) **each request is an object with `execute` and `undo`, so a stack of them is an undo history**
(c) it uses reflection
(d) it clones the receiver

<details><summary>Answer</summary>

**(b).**

(a) describes **Memento**, which captures state rather than the operation — and the two are often used together, with Command storing a Memento to make `undo` easy for operations that are not cleanly invertible.

**The system-design connection:** Command is **event sourcing**. Store the operations rather than the resulting state, and current state is a fold over the log — giving audit, replay and time travel for free.
</details>

---

**Q15.** Java's caching of `Integer` values from −128 to 127 is an application of
(a) Singleton  (b) **Flyweight**  (c) Prototype  (d) Proxy

<details><summary>Answer</summary>

**(b) Flyweight.**

Many logical objects share one instance because the intrinsic state (the value) is immutable and shared, while nothing is extrinsic.

**And this is exactly why `==` on boxed integers is a trap:** inside the cache range two boxed 127s are the same object and `==` is true; outside it they are distinct and `==` is false. The pattern explains the bug.
</details>

---

**Q16.** Bridge solves
(a) interface mismatch
(b) **a class explosion when two dimensions vary independently, reducing m × n classes to m + n**
(c) object creation cost
(d) tree traversal

<details><summary>Answer</summary>

**(b).**

Shapes × renderers by inheritance gives 9 classes for 3 × 3, and 12 when a fourth shape appears. Composing the abstraction with an implementation interface gives 3 + 3, and each axis extends independently.

**Bridge versus Adapter:** Bridge is designed **in advance** to keep two dimensions independent; Adapter is applied **after the fact** to reconcile interfaces you did not control.
</details>

---

**Q17.** Template Method is generally less preferred than Strategy because
(a) it is slower
(b) **it uses inheritance, which is tighter coupling and fixes the choice at compile time**
(c) it cannot be tested
(d) it is deprecated

<details><summary>Answer</summary>

**(b).**

Strategy uses composition, so the behaviour is injected and swappable at runtime, and it is testable in isolation. Template Method binds the subclass to the base class's structure and its protected methods — the fragile base class problem.

**Template Method is still right** when the skeleton's ordering is an invariant that subclasses must not be able to break. That is the case it wins.
</details>

---

**Q18.** Which statement about applying design patterns is most accurate?
(a) More patterns means better design
(b) Every class should implement an interface
(c) **A pattern is justified when the requirements indicate that axis will actually vary; otherwise it is speculative generality**
(d) Patterns should be chosen before requirements are gathered

<details><summary>Answer</summary>

**(c).**

An interface with exactly one implementation and no prospect of a second adds indirection, a file and cognitive load for no benefit. In a timed machine-coding round it also costs minutes you need for finishing.

**The interview-ready phrasing:** "I'd put pricing behind a Strategy because the requirements already mention weekday and weekend rates, so that axis clearly varies. I wouldn't abstract the payment gateway yet — there's only one, and I'd extract the interface when a second appears."

That answer shows you understand patterns **and** their cost, which is a stronger signal than either alone.
</details>

---

## Scoring

| Score /18 | Reading |
|---|---|
| 16+ | Patterns are solid |
| 12–15 | Re-read `pattern-selection-guide.md` §2, the distinctions table |
| 8–11 | Work through all three category files |
| < 8 | Start with `behavioural.md` — it carries the most LLD weight |

**Diagnostic:** misses on Q2, Q3, Q5, Q8 are all *distinction* questions. Those four pairs — Strategy/State, Adapter/Decorator, Decorator/Proxy, Factory/Abstract Factory — account for most pattern MCQs, and they are pure recall once seen side by side.
