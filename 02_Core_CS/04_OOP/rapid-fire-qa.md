# OOP — Rapid Fire Q&A

> Three sentences or fewer, in your own words. Expect three to four follow-ups on any of these — and expect to be asked to connect them to your own code.

**1. What is OOP and why use it?** Organising a program around objects that own their state and expose behaviour, so callers depend on an interface rather than an implementation. The payoff is substitutability: you can swap implementations without touching the calling code.

**2. Explain encapsulation with a real example.** Bundling data with the operations that maintain its invariants, so nothing outside can corrupt it. A `BankAccount` exposing `deposit` and `withdraw` is encapsulated; one exposing `setBalance` is not, whatever its access modifiers say.

**3. Encapsulation vs abstraction?** Encapsulation hides *data* and protects invariants; abstraction hides *complexity* behind a minimal interface. `List.add()` is abstraction; the private array behind it is encapsulation.

**4. Why does inheritance exist, if not for code reuse?** For substitutability — so code written against the base works unchanged with any derived type. When you only want reuse, composition is the safer tool.

**5. Overloading vs overriding?** Overloading is several methods with the same name and different parameters, resolved at compile time from declared types. Overriding is a subclass replacing a superclass method with the same signature, resolved at runtime from the actual object.

**6. What is never polymorphic?** Static methods, fields, and C++ default arguments — all three resolve from the declared type. Only overridden instance methods dispatch on the object.

**7. Abstract class or interface — how do you choose?** An abstract class when the subtypes genuinely share state and partial implementation; an interface when unrelated types share a capability. If you want to inherit from two abstract classes, one of them should have been an interface.

**8. Explain SOLID briefly.** One reason to change per class; extend without modifying; subtypes must be substitutable; no client depends on methods it does not use; depend on abstractions rather than concrete classes.

**9. Give a Liskov violation you have actually seen.** `Square extends Rectangle`: setting the width silently changes the height, so any code relying on the rectangle contract breaks. The fix is sibling types under a shape abstraction, or immutability.

**10. What is the fragile base class problem?** A subclass depends on the base class's internal behaviour, so an innocuous change in the base silently breaks the subclass. It is the strongest practical argument for preferring composition.

**11. What is the diamond problem and how is it solved?** Two parents sharing a grandparent leave ambiguity about which grandparent subobject the child gets. C++ uses virtual inheritance, Java forbids multiple class inheritance, Python uses C3 linearisation.

**12. Why must a C++ base destructor be virtual?** Because deleting a derived object through a base pointer otherwise calls only the base destructor, leaking everything the derived class owned. Any class meant to be used polymorphically needs one.

**13. What is a vtable?** A per-class array of function pointers; every polymorphic object carries a hidden pointer to it. A virtual call is one indirection through that table, which is why it cannot always be inlined.

**14. What is object slicing?** Copying a derived object into a base-typed variable keeps only the base part and discards the derived state and vptr, so polymorphism is silently lost. Pass by reference or pointer instead.

**15. What is RAII?** Tying a resource's lifetime to an object's scope — acquire in the constructor, release in the destructor — so cleanup is automatic even when an exception unwinds. It is why C++ needs no `finally`.

**16. How does garbage collection work?** Unreachable objects are reclaimed automatically. Java uses generational collection on the assumption that most objects die young; Python uses reference counting plus a cycle detector.

**17. Why is `finalize()` discouraged in Java?** It runs at an unpredictable time or not at all, it can resurrect objects, and it slows collection. Use try-with-resources or an explicit close method.

**18. State the equals/hashCode contract.** Equal objects must have equal hash codes. Break it and hash-based collections fail silently, because a lookup for an equal key hashes to a different bucket.

**19. Shallow vs deep copy?** Shallow duplicates the object but shares its references; deep duplicates the whole object graph. Java's `clone()` is shallow by default, which is the usual source of the bug.

**20. Why should you not call an overridable method from a constructor?** The subclass override runs before the subclass's fields are initialised, so it sees nulls and zeros. Mark such methods `final` or `private`.

**21. What are access modifiers for?** They express the intended contract, not just visibility — `public` is a promise you must keep. Default to the most restrictive that works, because widening later is easy and narrowing is a breaking change.

**22. Static vs instance members?** Static belongs to the class, is initialised once at class load, and cannot touch instance state. Instance members exist per object.

**23. What is an inner class, and when is it useful?** A class defined inside another, useful when it is only meaningful in that context — a `Node` inside a `LinkedList`. Non-static inner classes hold a reference to the outer instance, which can keep it alive unexpectedly.

**24. What is dependency injection, and why does it help testing?** Passing collaborators in rather than constructing them internally. It lets a test substitute a fake repository or clock without touching the class under test.

**25. Which design patterns do you actually use?** *(Answer from real usage. Strategy for swappable algorithms, Factory for construction you want to centralise, Observer for event notification, Singleton — with the caveat that it is global state and hurts testability.)*

**26. Is Singleton an anti-pattern?** It is global mutable state with a friendly name: it hides dependencies, complicates testing and is easy to get wrong under concurrency. Use it for genuinely single resources, and prefer injecting a single instance instead.

**27. What is the Strategy pattern, in one sentence?** Encapsulate a family of interchangeable algorithms behind an interface and let the client pick one at runtime — it is composition used to replace an inheritance hierarchy.

**28. How do you decide a class has too many responsibilities?** When you struggle to name it without "and", or when two unrelated kinds of change both force you to edit it.

**29. How does OOP interact with testability?** Programming to interfaces lets you substitute test doubles; hidden construction and static state prevent it. Most "untestable" code is really "hard-coded dependencies" code.

**30. Walk me through a class design from your own project.** *(Have one ready: the entities, why each relationship is inheritance or composition, and one thing you would change now. This question comes up in every LLD round.)*
