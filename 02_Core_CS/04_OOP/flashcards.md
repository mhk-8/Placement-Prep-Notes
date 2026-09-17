# OOP — Flashcards

## Questions

1. Define encapsulation and abstraction so the difference is unambiguous.
2. Give the overloading vs overriding table: signature, binding, resolution basis.
3. Name the three things that are never polymorphic.
4. What happens when you write a same-signature static method in a subclass?
5. `A a = new B(); a.x` where both declare `x` — which field is read, and why?
6. When do you choose an abstract class over an interface?
7. Why is `Square extends Rectangle` a Liskov violation, and what is the fix?
8. Give a violation and a fix for each of the five SOLID principles.
9. What is the fragile base class problem?
10. How do Java, C++ and Python each resolve the diamond problem?
11. Why must a C++ base destructor be virtual, and what is the output without it?
12. What is object slicing and how do you prevent it?
13. What is a vtable and what does it cost per object and per call?
14. What is printed when a constructor calls an overridable method, and why?
15. State the equals/hashCode contract and the symptom of violating it.
16. What is Java's Integer cache range, and what bug does it cause?
17. What happens when `finally` contains a `return`?
18. Shallow vs deep copy?
19. What is RAII, and why does C++ have no `finally`?
20. Give the Java initialisation order for statics and for each instantiation.

---

## Answers

1. Encapsulation bundles data with the methods that protect its invariants, so no outsider can create an invalid state. Abstraction exposes a minimal interface and hides the mechanism behind it — data hiding versus complexity hiding.
2. Overloading: different parameters, same class, **compile-time** binding from declared types. Overriding: identical signature in a subclass, **runtime** binding from the actual object, and the access modifier may not be narrowed.
3. Static methods, fields, and C++ default arguments — all resolve from the declared type.
4. It **hides** rather than overrides. The call resolves from the declared type, so `A a = new B(); a.f()` runs A's version.
5. `A.x`, because field access is resolved at compile time from the reference's declared type. The object contains both fields.
6. When the subtypes genuinely share state and a partial implementation. An interface when unrelated types share a capability, or when a type needs several such capabilities.
7. Rectangle promises that setting the width leaves the height alone; Square cannot honour that, so substitution breaks caller assumptions. Fix: sibling types under a Shape abstraction, or make both immutable.
8. SRP — a class that computes, formats and mails a report; split it. OCP — a type-switch over shapes; use a Shape interface. LSP — Square/Rectangle; restructure. ISP — a fat Worker interface forcing no-op methods; split into Workable and Feedable. DIP — a service constructing its own repository; inject the interface.
9. A subclass depends on base-class internals, so a change in the base silently breaks it. It is the main practical argument for composition.
10. Java forbids multiple class inheritance (and requires explicit resolution of conflicting interface defaults). C++ uses virtual inheritance for a single shared base subobject. Python uses C3 linearisation, inspectable via `__mro__`.
11. Otherwise `delete` through a base pointer calls only the base destructor — undefined behaviour and a leak of the derived state. Without `virtual` the output is just the base destructor's; with it, derived then base.
12. Copying a derived object into a base-typed variable or by-value parameter keeps only the base subobject, discarding derived state and the vptr. Pass by reference or pointer.
13. A per-class array of function pointers, with a hidden vptr in every polymorphic object. Cost: one pointer per object and one indirection per call, which also inhibits inlining.
14. The subclass override runs before the subclass's field initialisers, so it observes default values (`0` / `null`). That is why overridable methods must not be called from constructors.
15. Equal objects must have equal hash codes. Violating it makes hash-based lookups fail silently for objects that compare equal.
16. −128 to 127. Boxed values in that range are shared, so `==` is true; outside it `==` is false, which silently breaks comparisons written with `==`.
17. It replaces the pending return value, and will even discard a pending exception. Never return or throw from `finally`.
18. Shallow duplicates the object but shares its referenced objects; deep duplicates the entire graph. Java's `clone()` is shallow by default.
19. Resource Acquisition Is Initialisation: acquire in the constructor, release in the destructor, so cleanup is deterministic on scope exit including during exception unwinding — which removes the need for `finally`.
20. At class load, once: static fields and static blocks in source order. At each `new`: field defaults, then the full superclass chain, then instance initialisers and field assignments in source order, then the constructor body.
