# OOP — Solved OA Questions

> 20 questions in OA style with every option explained. OOP MCQs are about half definitions and half output prediction; the definitional half is pure recall, so it should be automatic.
> **30 seconds** for definitions, **90 seconds** for code traces.

---

## Set A — Concepts

**Q1.** Which of these is resolved at **compile time**?
(a) Method overriding  (b) Method overloading  (c) Virtual function calls  (d) Dynamic binding

<details><summary>Answer</summary>

**(b) Method overloading.**

The compiler picks the overload from the **declared** types of the arguments. Overriding, virtual calls and dynamic binding are all names for the same runtime mechanism.

**The one-line version to remember:** overloading = compile time = declared type; overriding = runtime = actual object.
</details>

---

**Q2.** Which cannot be overridden in Java?
(a) A public method  (b) A protected method  (c) A static method  (d) A method returning void

<details><summary>Answer</summary>

**(c) A static method.**

Static methods belong to the class, so a same-signature static method in a subclass **hides** rather than overrides — and the call resolves from the declared type. `private` and `final` methods also cannot be overridden: private methods are invisible to the subclass, and `final` forbids it explicitly.

(d) the return type being `void` is irrelevant.
</details>

---

**Q3.** An abstract class differs from an interface in that it
(a) cannot be instantiated  (b) can have constructors and instance fields  (c) cannot have methods  (d) supports multiple inheritance

<details><summary>Answer</summary>

**(b).**

(a) is true of *both*, so it cannot be the difference. (c) is false — an abstract class can have fully concrete methods, and since Java 8 interfaces can have default and static ones. (d) is backwards: interfaces support multiple inheritance, classes do not.

**The design distinction:** an abstract class models an is-a hierarchy with shared state; an interface models a can-do capability across unrelated types.
</details>

---

**Q4.** `Square extends Rectangle` violates which SOLID principle?
(a) Single Responsibility  (b) Open/Closed  (c) **Liskov Substitution**  (d) Interface Segregation

<details><summary>Answer</summary>

**(c) Liskov Substitution.**

A `Rectangle` promises that `setWidth(5)` changes only the width. A `Square` cannot honour that — it must also change the height — so code written against `Rectangle` breaks when handed a `Square`. The subtype is not substitutable.

**The fix:** make them siblings under a `Shape` abstraction, or make both immutable so the mutator problem never arises.
</details>

---

**Q5.** Composition is preferred over inheritance mainly because
(a) it is faster at runtime
(b) it gives looser coupling and allows behaviour to change at runtime
(c) it uses less memory
(d) inheritance is deprecated

<details><summary>Answer</summary>

**(b).**

Inheritance is the tightest coupling a language offers — the subclass depends on base-class internals, so a base change can silently break it (the fragile base class problem) — and the relationship is fixed at compile time. Composition lets you swap the collaborator at runtime, which is exactly the Strategy pattern.

(a) and (c) are marginal and usually false (an extra indirection). (d) is nonsense — inheritance is correct when "is-a" genuinely holds.
</details>

---

**Q6.** In C++, a class with at least one pure virtual function is
(a) a concrete class  (b) an abstract class that cannot be instantiated  (c) a friend class  (d) a template

<details><summary>Answer</summary>

**(b).**

`virtual void f() = 0;` makes the class abstract. A derived class that does not override *every* pure virtual remains abstract too — a common follow-up.

A class with **only** pure virtual functions and a virtual destructor is C++'s equivalent of a Java interface.
</details>

---

## Set B — Output prediction

**Q7.** What does this print?
```java
class A { void f() { System.out.print("A"); } }
class B extends A { void f() { System.out.print("B"); } }
A obj = new B();
obj.f();
```
(a) A  (b) B  (c) AB  (d) Compile error

<details><summary>Answer</summary>

**(b) B.**

An overridden instance method dispatches on the actual object. This is the baseline; every trap below is a departure from it.
</details>

---

**Q8.** What does this print?
```java
class A { int x = 10; }
class B extends A { int x = 20; }
A a = new B();
System.out.print(a.x);
```
(a) 10  (b) 20  (c) 30  (d) Compile error

<details><summary>Answer</summary>

**(a) 10.**

**Fields are not polymorphic.** The `B` object holds both `A.x` and `B.x`; which you see depends on the *reference* type. Through an `A` reference you see `A.x`.

This is field hiding, and it is a strong argument against public fields in a hierarchy.
</details>

---

**Q9.** What does this print?
```cpp
class Base    { public: ~Base()    { cout << "1"; } };
class Derived : public Base { public: ~Derived() { cout << "2"; } };
Base* p = new Derived();
delete p;
```
(a) 12  (b) 21  (c) 1  (d) 2

<details><summary>Answer</summary>

**(c) 1.**

The base destructor is **not virtual**, so `delete` through a base pointer calls only `~Base` — formally undefined behaviour, practically a leak of everything `Derived` owned.

With `virtual ~Base()` the answer becomes **(b) 21**: destructors run derived-first, the reverse of construction order. Both variants appear in OAs, so read whether `virtual` is present.
</details>

---

**Q10.** What does this print?
```java
Integer a = 127, b = 127, c = 128, d = 128;
System.out.print((a == b) + "," + (c == d));
```
(a) true,true  (b) true,false  (c) false,false  (d) false,true

<details><summary>Answer</summary>

**(b) true,false.**

Java caches boxed `Integer` values from **−128 to 127**, so `a` and `b` reference the same cached object. Above the cache, each autoboxing creates a new object, so `==` compares distinct references.

Always use `.equals()` for boxed types — this is a genuine production bug pattern, not merely an exam curiosity.
</details>

---

**Q11.** What does this print?
```java
class A {
    A() { f(); }
    void f() { System.out.print("A"); }
}
class B extends A {
    int x = 5;
    void f() { System.out.print(x); }
}
new B();
```
(a) A  (b) 5  (c) 0  (d) Compile error

<details><summary>Answer</summary>

**(c) 0.**

`new B()` runs `super()` first. `A`'s constructor calls `f()`, which is overridden, so **B's version runs** — but B's field initialisers have not executed yet, so `x` still holds its default `0`.

**The rule:** never call an overridable method from a constructor.
</details>

---

**Q12.** What does this print?
```java
class A {}
class B extends A {}
static void f(A a) { System.out.print("A"); }
static void f(B b) { System.out.print("B"); }
A obj = new B();
f(obj);
```
(a) A  (b) B  (c) Ambiguous — compile error  (d) Runtime error

<details><summary>Answer</summary>

**(a) A.**

Overload resolution is a **compile-time** decision made from the declared type of the argument, which is `A`. Runtime polymorphism applies only to overriding.

If the reference were declared `B obj = new B()`, the answer would be `B` — which is the clean way to demonstrate that the binding is static.
</details>

---

**Q13.** What does this print?
```java
static int f() {
    try { return 1; }
    finally { return 2; }
}
System.out.print(f());
```
(a) 1  (b) 2  (c) 12  (d) Compile error

<details><summary>Answer</summary>

**(b) 2.**

`finally` executes before the method returns, and a `return` inside it replaces the pending return value — it will even discard a pending exception.

Never return or throw from `finally`; compilers warn about it for this reason.
</details>

---

**Q14.** In Python, what is printed?
```python
class A:
    def who(self): return "A"
class B(A):
    def who(self): return "B"
class C(A):
    def who(self): return "C"
class D(B, C): pass
print(D().who())
```
(a) A  (b) B  (c) C  (d) TypeError

<details><summary>Answer</summary>

**(b) B.**

Python's C3 linearisation gives the MRO `D → B → C → A → object`, so `who` is found on `B`. Writing `class D(C, B)` instead would print `C`.

`D.__mro__` prints the resolution order, which is the practical way to answer these rather than reasoning about the diamond.
</details>

---

## Set C — Mechanics

**Q15.** In Java, if you override `equals()` but not `hashCode()`
(a) the code does not compile
(b) hash-based collections may fail to find equal objects
(c) nothing changes
(d) `equals()` stops working

<details><summary>Answer</summary>

**(b).**

Equal objects inherit identity-based hash codes, so they land in different buckets; `HashSet.contains` never reaches the `equals` comparison and returns false for an object that *is* equal.

It compiles and runs — the failure is silent, which is what makes it dangerous and a favourite question.
</details>

---

**Q16.** Object slicing in C++ occurs when
(a) a derived object is assigned to a base object **by value**
(b) a derived object is passed by reference
(c) a base pointer points to a derived object
(d) a virtual function is called

<details><summary>Answer</summary>

**(a).**

Copying into a base-typed variable or parameter keeps only the base subobject; the derived state and the vptr are discarded, so virtual dispatch silently reverts to the base implementation.

(b) and (c) preserve the full object — which is why polymorphic types are always passed by reference or pointer.
</details>

---

**Q17.** What does `super()` do in a Java constructor?
(a) Calls the superclass constructor; implicitly inserted if absent
(b) Creates a superclass object
(c) Is optional and has no effect
(d) Must be the last statement

<details><summary>Answer</summary>

**(a).**

The compiler inserts an implicit no-argument `super()` unless you explicitly call `super(...)` or `this(...)`. It must be the **first** statement, which is why (d) is wrong, and it initialises the superclass portion of the *same* object — no separate object is created, so (b) is wrong too.
</details>

---

**Q18.** RAII in C++ means
(a) resources are acquired in the constructor and released in the destructor
(b) all allocation uses `new`
(c) reference counting is automatic
(d) garbage collection is enabled

<details><summary>Answer</summary>

**(a).**

Resource Acquisition Is Initialisation ties a resource's lifetime to an object's scope, so cleanup happens deterministically on scope exit — **including during exception unwinding**. That is why C++ has no `finally` block and why `std::lock_guard` and `std::unique_ptr` exist.
</details>

---

**Q19.** A shallow copy of an object
(a) duplicates the entire object graph
(b) duplicates the object but shares its referenced objects
(c) is always slower than a deep copy
(d) is not possible in Java

<details><summary>Answer</summary>

**(b).**

The copy holds the same references, so mutating a nested object through one copy is visible through the other. Java's `Object.clone()` is shallow by default, which is the usual source of this bug.
</details>

---

**Q20.** Which statement about the diamond problem is correct?
(a) Java solves it with virtual inheritance
(b) C++ solves it with virtual inheritance; Java avoids it by forbidding multiple class inheritance
(c) Python has no solution
(d) It cannot occur with interfaces

<details><summary>Answer</summary>

**(b).**

C++ uses `virtual public Base` to keep a single shared base subobject. Java forbids multiple inheritance of **classes** entirely; interfaces may be multiply inherited, and since Java 8 a conflict between two default methods must be resolved explicitly — so (d) is false too. Python resolves it deterministically with C3 linearisation.
</details>

---

## Scoring

| Score /20 | Reading |
|---|---|
| 18+ | OOP is OA-ready |
| 14–17 | Solid; redo the output-prediction set |
| 9–13 | Re-read `concepts.md` §3 and §7 |
| < 9 | Work through `output-prediction.md` line by line |

**Diagnostic:** misses on Q1, Q2, Q7, Q8, Q12 mean the static-vs-dynamic binding rule has not landed. That one rule is worth more marks in this folder than everything else combined.
