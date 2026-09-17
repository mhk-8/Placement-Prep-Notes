# Object-Oriented Programming — Concepts

## 1. Core idea in 3 lines
OOP organises a program around objects that bundle state with the operations on that state, so that callers depend on *what* something does rather than *how*. Its real payoff is substitutability: code written against an abstraction keeps working when you supply a different implementation. In interviews it is tested three ways — definitions, output prediction on inheritance puzzles, and live class design in the LLD round.

---

## 2. The four pillars — with examples that are not the textbook ones

**Encapsulation** — bundling data with the methods that operate on it, and controlling access so invariants cannot be broken from outside. The test is not "are the fields private"; it is "can an outsider put this object into an invalid state?" A `BankAccount` that exposes `setBalance()` is not encapsulated, whatever its access modifiers say; one that exposes `deposit()` and `withdraw()` and rejects an overdraft is.

**Abstraction** — exposing a minimal interface and hiding the mechanism. `List.add()` is the same call whether the implementation is an array or a linked list. Encapsulation hides *data*; abstraction hides *complexity*. They are related but not the same, and being able to separate them is a common discriminator.

**Inheritance** — an "is-a" relationship where a subclass acquires and extends a superclass's behaviour. Its purpose is *substitutability*, not code reuse; when you want reuse without substitutability, use composition.

**Polymorphism** — one interface, many implementations.
- **Compile-time (static)**: method overloading and operator overloading. Resolved by the compiler from the declared types.
- **Runtime (dynamic)**: method overriding. Resolved at execution from the actual object's type, via a virtual dispatch table.

---

## 3. Overloading vs overriding — the distinction that decides half the MCQs

| | Overloading | Overriding |
|---|---|---|
| Where | same class (or inherited) | subclass redefines a superclass method |
| Signature | must **differ** in parameters | must be **identical** |
| Return type | may differ | must be the same or covariant |
| Binding | **compile time**, from the declared type | **runtime**, from the actual object |
| Access modifier | free | cannot be more restrictive |
| Static methods | can be overloaded | **cannot be overridden** — they are *hidden* |
| Private/final methods | — | cannot be overridden |

**The three things that are never polymorphic**, and are the source of nearly every output-prediction trap:
1. **Static methods** — resolved from the *declared* type, so `A a = new B(); a.staticMethod()` calls A's.
2. **Fields** — also resolved from the declared type: `A a = new B(); a.x` reads A's `x`, even if B declares its own.
3. **Default parameter values in C++** — the *function* dispatches dynamically, but the default argument is substituted statically from the declared type.

---

## 4. Abstract class vs interface

| | Abstract class | Interface |
|---|---|---|
| Instantiable | no | no |
| State (fields) | yes | constants only (Java) |
| Constructors | yes | no |
| Multiple inheritance | one only (Java/C#) | many |
| Method bodies | yes | default/static methods (Java 8+) |
| Access modifiers | any | public (implicitly) |
| Models | an **is-a** hierarchy with shared state | a **can-do** capability |

**When to choose which:** an abstract class when the subtypes genuinely share state and a common partial implementation (`AbstractList`); an interface when unrelated types share a capability (`Comparable`, `Serializable`, `Runnable`). A good rule of thumb: if you find yourself wanting to inherit from two abstract classes, one of them should have been an interface.

C++ has no `interface` keyword — the equivalent is a class with only pure virtual functions (`= 0`) and a virtual destructor.

---

## 5. Composition over inheritance

Inheritance is the tightest coupling a language offers: the subclass depends on the superclass's implementation details, and a superclass change can break it silently (the "fragile base class" problem). It also fixes behaviour at compile time.

**Use inheritance only when "is-a" is genuinely true and substitution is safe.** A `Square` inheriting `Rectangle` fails this, because `setWidth` on a Square cannot preserve the Rectangle contract — the classic Liskov violation.

**Use composition** when you want "has-a" or "uses-a" — a `Car` *has an* `Engine`. Composition permits swapping behaviour at runtime, which is exactly what the Strategy pattern is.

**The diamond problem:** if D inherits from B and C, both of which inherit from A, which A does D get? Java forbids it for classes (only interfaces may be multiply inherited, and conflicting defaults must be resolved explicitly). C++ solves it with **virtual inheritance**, which keeps a single shared A subobject. Python resolves it with **C3 linearisation** (the MRO), which produces a deterministic, monotonic ordering visible via `Class.__mro__`.

---

## 6. SOLID — a violation and a fix for each

**S — Single Responsibility.** A class should have one reason to change.
*Violation:* `Report` that computes totals, formats HTML and emails itself — a change to the mail server forces a change to reporting logic. *Fix:* `ReportCalculator`, `ReportRenderer`, `ReportMailer`.

**O — Open/Closed.** Open for extension, closed for modification.
*Violation:* `if (shape.type == CIRCLE) … else if (SQUARE) …` — adding a shape edits existing code. *Fix:* a `Shape` interface with `area()`; adding a shape adds a class.

**L — Liskov Substitution.** A subtype must be usable anywhere its supertype is.
*Violation:* `Square extends Rectangle` where `setWidth(5)` silently changes the height. *Fix:* make them siblings under a `Shape` abstraction, or make them immutable.

**I — Interface Segregation.** No client should depend on methods it does not use.
*Violation:* a fat `Worker` interface with `work()` and `eat()`, forcing a `RobotWorker` to implement `eat()` as a no-op or a throw. *Fix:* separate `Workable` and `Feedable`.

**D — Dependency Inversion.** Depend on abstractions, not concretions.
*Violation:* `OrderService` constructing `new MySQLOrderRepository()` internally — untestable and un-swappable. *Fix:* inject an `OrderRepository` interface.

Reciting the acronym is worth nothing; being able to give a violation and its fix on demand is worth a lot.

---

## 7. Language mechanics that get tested

**Constructors.** Run in order superclass → subclass. Java inserts an implicit `super()` unless you call another constructor explicitly. **Never call an overridable method from a constructor** — the subclass override runs before the subclass's fields are initialised, so it sees nulls and zeros.

**Virtual dispatch.** A class with virtual methods has a **vtable** — an array of function pointers — and every object carries a hidden vptr to it. A virtual call is an indirect call through that table, which is why it is slightly slower than a direct call and cannot be inlined as easily.

**Virtual destructors (C++).** Deleting a derived object through a base pointer is **undefined behaviour** unless the base destructor is virtual. This is the single most-asked C++ OOP question.

**Object slicing (C++).** Assigning or passing a derived object *by value* to a base parameter copies only the base part and discards the derived state — polymorphism is lost silently. Pass by reference or pointer.

**RAII (C++).** Resources are acquired in the constructor and released in the destructor, so cleanup happens automatically on scope exit, including during exception unwinding. It is why C++ has no `finally`.

**Garbage collection (Java/Python).** Unreachable objects are reclaimed automatically. Java uses generational collection on the heap; Python uses reference counting plus a cycle collector, which is why reference cycles in Python need the `gc` module.

**`equals` and `hashCode` (Java).** Equal objects must have equal hash codes. Overriding one without the other breaks every hash-based collection — a lookup for an equal key lands in a different bucket and fails silently.

**Shallow vs deep copy.** A shallow copy duplicates the object but shares its referenced objects; a deep copy duplicates the whole graph. Mutating a shared inner object through one copy is visible through the other.

**Static vs instance.** Static members belong to the class, are initialised once at class-load time, and cannot access instance state. Static initialisation order: static fields and static blocks in source order at class load, then instance initialisers and the constructor at each instantiation.

---

## 8. Recall questions

1. Define encapsulation and abstraction so the difference is clear.
2. Give the full overloading/overriding comparison table.
3. Name the three things that are never polymorphic.
4. When do you choose an abstract class over an interface?
5. Why is `Square extends Rectangle` a Liskov violation?
6. Give a violation and a fix for each SOLID principle.
7. What is the diamond problem, and how do Java, C++ and Python each resolve it?
8. Why must a base-class destructor be virtual in C++?
9. What is object slicing and how do you avoid it?
10. Why must you never call an overridable method from a constructor?
11. State the `equals`/`hashCode` contract and what breaks if you violate it.
12. What is a vtable, and what does it cost?
