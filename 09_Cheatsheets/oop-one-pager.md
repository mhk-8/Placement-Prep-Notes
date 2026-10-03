
# OOP and Design Principles — One Pager

> **Use:** every interview with a "design" or "fundamentals" segment, and mandatory for the
> **machine-coding rounds** at Flipkart/Walmart and Sprinklr. ⭐ See
> `../08_Company_Wise/Flipkart_Walmart/01-prep-and-questions.md` for the timed builds.

---

## 1. The four pillars ⭐⭐⭐

| Pillar | One-line definition | Mechanism |
|---|---|---|
| **Encapsulation** | Bundle data with the methods that operate on it; hide internal state | private fields + accessors |
| **Abstraction** | Expose *what*, hide *how* | interfaces, abstract classes |
| **Inheritance** | Derive a type from another, reusing and specialising | `extends` / `: public` |
| **Polymorphism** | One interface, many implementations | virtual dispatch, overriding |

> ⚠️ **Encapsulation vs abstraction** is the most-failed follow-up. Encapsulation is about
> *bundling and restricting access* (an implementation technique); abstraction is about
> *exposing a simplified model* (a design intent). A class with private fields and public
> getters is encapsulated; an interface that hides whether storage is a file or a database
> is an abstraction. ⭐

### Polymorphism kinds
```
Compile-time (static)  : overloading, templates/generics  → resolved by signature
Run-time (dynamic)     : overriding via virtual dispatch  → resolved by actual object type
```

| | Overloading | Overriding |
|---|---|---|
| Signature | Different parameters | Identical |
| Resolution | Compile time | Run time |
| Scope | Same class (or inherited) | Base → derived |
| Return type | May differ | Same or covariant |

---

## 2. SOLID ⭐⭐⭐ (name them and give one concrete example each)

| Principle | Statement | Smell it fixes |
|---|---|---|
| **S** Single Responsibility | A class should have one reason to change | A class that parses, validates *and* persists |
| **O** Open/Closed | Open for extension, closed for modification | A `switch` on type that grows with every new type ⭐ |
| **L** Liskov Substitution | A subtype must be usable wherever the base is | `Square extends Rectangle` with independent setters ⚠️ the classic violation |
| **I** Interface Segregation | Many small interfaces over one fat one | An interface whose implementers throw `UnsupportedOperation` |
| **D** Dependency Inversion | Depend on abstractions, not concretions | `new MySQLRepo()` inside business logic → inject a `Repository` |

**Others worth naming:** DRY · KISS · YAGNI · **composition over inheritance** ⭐ ·
Law of Demeter (talk only to immediate collaborators) · separation of concerns.

> ⭐ **"Composition over inheritance", concretely:** inheritance couples you to the base class's
> implementation and is fixed at compile time; composition lets you swap behaviour at run time
> and avoids the fragile-base-class and diamond problems. A `Car` *has an* `Engine`; it is not
> *a kind of* `Engine`.

---

## 3. Relationships

```
Association  : uses-a           Student — Course
Aggregation  : has-a, weak      Department ◇— Professor   (professor outlives the department)
Composition  : has-a, strong    House ◆— Room             (rooms die with the house) ⭐
Inheritance  : is-a             Dog —▷ Animal
Dependency   : needs-a          Service - - -> Logger
```

**Inheritance types:** single · multilevel · hierarchical · multiple (C++, interfaces in Java) ·
hybrid. ⚠️ **Diamond problem:** `D` inherits `B` and `C`, both from `A` → ambiguous `A`.
Resolved by `virtual` inheritance in C++, by interfaces + explicit `super` in Java, and by
the **MRO (C3 linearisation)** in Python. ⭐

---

## 4. Design patterns ⭐⭐ (know the ones marked — they appear in machine-coding rounds)

### Creational
| Pattern | Use | Note |
|---|---|---|
| **Singleton** ⭐ | One instance, global access | Thread safety: enum / static holder / DCL with `volatile`. ⚠️ hard to test — a known anti-pattern smell |
| **Factory Method** ⭐ | Subclass decides which concrete type | Removes `new` from client code |
| **Abstract Factory** | Families of related products | |
| **Builder** ⭐ | Many optional parameters, immutable result | Replaces telescoping constructors |
| Prototype | Clone an existing instance | |
| Object pool | Reuse expensive objects | Connection pools |

### Structural
| Pattern | Use |
|---|---|
| **Adapter** ⭐ | Make an incompatible interface fit |
| **Decorator** ⭐ | Add behaviour without subclassing (`BufferedReader(FileReader)`) |
| Facade | One simple entry point over a complex subsystem |
| Proxy | Stand-in: lazy loading, access control, remoting |
| Composite | Treat a tree of objects uniformly (file system) |
| Bridge | Separate abstraction from implementation |
| Flyweight | Share immutable intrinsic state across many objects |

### Behavioural
| Pattern | Use |
|---|---|
| **Strategy** ⭐ | Interchangeable algorithms chosen at run time — the OCP workhorse |
| **Observer** ⭐ | Publish-subscribe; listeners notified of state change |
| **State** ⭐ | Behaviour varies by state; replaces large conditionals (vending machine, elevator) |
| Command | Encapsulate a request as an object → undo, queue, log |
| Template Method | Fixed skeleton, overridable steps |
| Chain of Responsibility | Pass a request along handlers (middleware) |
| Iterator · Mediator · Memento · Visitor ⭐ | Traversal · decoupled communication · snapshot · operations over a type hierarchy |

> ⭐ **Visitor is worth a sentence for you specifically** — AST traversal in a compiler is the
> canonical use, and you have actually written one. That is a far better answer than a textbook
> example.

---

## 5. Machine-coding / LLD playbook ⭐⭐⭐

> Flipkart, Walmart, Sprinklr and Arcesium run 60-120 minute "build a working system" rounds.
> They are scored on **structure and extensibility**, not on finishing every feature.

```
MINUTE 0-10   : Clarify. List entities, the 3-4 core operations, and ONE extension they might
                ask for mid-round. Say your assumptions out loud and write them in a comment.
MINUTE 10-20  : Classes and interfaces. Enums for fixed sets. Interfaces at every point where a
                rule could change (pricing, matching, notification). ⭐
MINUTE 20-80  : Implement the happy path end to end, THEN edge cases. A working subset beats a
                half-built complete design ⚠️
LAST 15 MIN   : A runnable main()/demo, a few assertions, and a short README of design choices.
```

**The reflexes they are looking for:**
```
□ Interfaces for varying behaviour → Strategy (pricing, discount, allocation)
□ Enums + a state machine for lifecycles → State (order, booking, elevator)
□ A factory where object creation branches on type
□ Observer for notifications
□ No business logic in main(); no god class; no public mutable fields
□ Thread safety mentioned even if not implemented ("I'd guard the inventory map with a
  ConcurrentHashMap and make the decrement a CAS") ⭐
□ In-memory repository behind an interface → DIP, and it makes testing trivial
```

**The standard problem set — practise these four, timed:**
```
1. Parking lot          (pricing strategy, slot allocation, ticketing)
2. Splitwise            (expense splitting strategies, balance settlement)
3. Elevator / lift bank (state machine, scheduling strategy)
4. Snake & ladder / BookMyShow / rate limiter / in-memory cache with TTL + LRU eviction
```

---

## 6. Common follow-ups ⭐

| Question | Answer |
|---|---|
| Abstract class vs interface | State + partial implementation + single inheritance vs pure contract + multiple. Choose abstract for shared code, interface for capability |
| Can a constructor be virtual? | No — the object's type is not yet established. Destructors should be virtual |
| Why no multiple inheritance in Java? | Diamond ambiguity; interfaces with `default` methods give most of the benefit |
| Interface with a `default` method | Java 8+; allows evolving an interface without breaking implementers |
| Static vs dynamic binding | Non-virtual/overloaded at compile time; virtual/overridden at run time |
| `final` class | Cannot be subclassed — `String` is final for security and hashcode caching |
| Immutable object | No setters, final fields, defensive copies in and out. Thread-safe by construction ⭐ |
| Shallow vs deep copy | One level vs recursive. Matters whenever a field is a mutable reference ⚠️ |
| `equals`/`hashCode` contract | Equal objects must have equal hash codes, or hash containers break |
| Cohesion vs coupling | High cohesion inside a class, low coupling between classes ⭐ |
| Is-a vs has-a | Inheritance vs composition; prefer has-a |
| Method hiding | A `static` method is hidden, not overridden ⚠️ |
| Constructor chaining | Base constructor runs first; `super()` / member-init list |
| Why is Singleton criticised? | Hidden global state, hard to test, often disguises a missing dependency injection |

---

## Recall questions
1. Distinguish encapsulation from abstraction in one sentence each.
2. State all five SOLID principles and give a violation for each.
3. Why does `Square extends Rectangle` break Liskov?
4. Composition over inheritance — give the concrete reason, not the slogan.
5. Which pattern would you use for an order lifecycle, and which for interchangeable pricing?
6. How do C++, Java and Python each resolve the diamond problem?
7. In a 90-minute machine-coding round, what do you deliberately leave unfinished and why?
