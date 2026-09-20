
# Object-Oriented Programming — Theory and MCQ Bank

> **Why it matters.** OOP appears in every technical MCQ section and in almost every interview
> opener ("explain polymorphism"). Adobe, Oracle and Samsung additionally ask **output-prediction**
> questions in C++ and Java, which this file covers in Part 1.5.

---

# Part 1 — Theory

## 1.1 The four pillars ⭐⭐⭐

| Pillar | Definition | Mechanism |
|---|---|---|
| **Encapsulation** | Bundling data with the methods that operate on it, and restricting direct access | `private` fields + public getters/setters |
| **Abstraction** | Exposing *what* an object does while hiding *how* | Abstract classes, interfaces |
| **Inheritance** | Deriving a new class from an existing one, reusing and extending it | `extends` / `: public` |
| **Polymorphism** | One interface, many implementations | Overloading (compile-time) and overriding (run-time) |

⚠️ **Encapsulation vs abstraction** — the distinction interviewers probe:
```
ABSTRACTION   is about DESIGN — deciding what to expose.   (hiding complexity)
ENCAPSULATION is about IMPLEMENTATION — how you enforce it. (hiding data)
An interface provides abstraction; private fields provide encapsulation.
```

## 1.2 Polymorphism in detail ⭐⭐⭐

| | Compile-time (static) | Run-time (dynamic) |
|---|---|---|
| Also called | Overloading, early binding | Overriding, late binding |
| Resolved at | Compile time | Run time |
| Mechanism | Different signatures | Virtual function table (vtable) |
| Java | Method overloading | Method overriding |
| C++ | Function/operator overloading | `virtual` functions ⭐ |

**Overloading vs overriding ⭐⭐⭐** — the single most-asked OOP question:

| Aspect | Overloading | Overriding |
|---|---|---|
| Where | Same class (or inherited) | Base and derived class |
| Signature | **Must differ** (parameter type, number or order) | **Must be identical** |
| Return type | May differ (but cannot differ *alone*) ⚠️ | Must be same or covariant |
| Access modifier | Any | Cannot be **more restrictive** than the base ⚠️ |
| Binding | Compile time | Run time |
| `static` methods | Can be overloaded | **Cannot be overridden** — they are *hidden* ⭐⚠️ |

⚠️ **Two changes of return type alone do not overload.** `int f(int)` and `double f(int)` is a
compile error in both C++ and Java.

⭐ **Method hiding vs overriding:** a `static` method in a subclass with the same signature *hides*
the parent's; the call is resolved by the **reference type**, not the object type. Same for fields.
This is a classic output-prediction trap.

## 1.3 Inheritance ⭐⭐

**Types:**
```
Single       : B extends A
Multilevel   : C extends B extends A
Hierarchical : B and C both extend A
Multiple     : D extends B, C      ⚠️ NOT supported for classes in Java/C#; IS in C++
Hybrid       : combination
```

⚠️ **The Diamond Problem ⭐⭐⭐**
```
        A
       / \
      B   C          B and C both inherit from A and both override f()
       \ /
        D            Which f() does D inherit?  Ambiguous.

C++ solution : VIRTUAL inheritance — class B : virtual public A
Java solution: forbid multiple class inheritance; allow multiple INTERFACE inheritance,
               and since Java 8 require the implementing class to resolve conflicting
               default methods explicitly:  A.super.f();
```

**Composition vs inheritance ⭐⭐:** prefer **composition** ("has-a") over inheritance ("is-a").
Inheritance creates tight coupling to the parent's implementation and breaks encapsulation;
composition is flexible and can be changed at run time. The heuristic: use inheritance only when
the subclass genuinely *is* a kind of the superclass and can be substituted for it (see LSP below).

## 1.4 Abstract class vs interface ⭐⭐⭐

| | Abstract class | Interface |
|---|---|---|
| Instantiable | No | No |
| Methods | Abstract **and** concrete | Abstract; `default`/`static` allowed since Java 8 |
| Fields | Any, including mutable state | `public static final` constants only |
| Constructor | **Yes** | **No** ⭐ |
| Multiple inheritance | One only | Many ⭐ |
| Access modifiers | Any | Methods implicitly `public` |
| Use when | Classes share state and behaviour ("is-a") | Unrelated classes share a capability ("can-do") ⭐ |

⭐ **The rule of thumb:** an abstract class models *what something is*; an interface models *what
something can do*. `Bird` is an abstract class; `Flyable` is an interface (and a penguin is a Bird
that is not Flyable).

## 1.5 Language-specific points that get asked ⭐⭐

### C++
```
virtual function    : enables run-time dispatch via the vtable
pure virtual        : virtual void f() = 0;  → makes the class ABSTRACT ⭐
VIRTUAL DESTRUCTOR  : ⚠️⭐⭐⭐ REQUIRED in any base class deleted through a base pointer;
                      otherwise only the base destructor runs → resource leak
object slicing      : ⚠️ assigning a Derived to a Base BY VALUE copies only the base part,
                      losing the derived data and the polymorphic behaviour
                        Base b = derivedObj;   // sliced
                        Base& r = derivedObj;  // fine, polymorphic
                        Base* p = &derivedObj; // fine, polymorphic
constructor order   : base → members (in DECLARATION order, not initialiser-list order ⚠️)
                      → derived body.  Destructors run in exactly the reverse order.
copy constructor    : X(const X&) — needed when the class owns raw resources
rule of three/five  : if you write a destructor, copy ctor or copy assignment, you probably need
                      all three (and in modern C++, the move ctor and move assignment too) ⭐
friend              : grants access to private members; NOT inherited, NOT transitive, and it
                      breaks encapsulation deliberately
static member       : shared by all objects; must be defined outside the class
const member fn     : cannot modify the object; can be called on const objects
```

### Java
```
final     : final class = cannot be extended;  final method = cannot be overridden;
            final variable = assigned once ⭐
static    : belongs to the class, not the instance. A static method cannot use `this` or access
            instance members directly ⭐
abstract  : cannot be instantiated. abstract + final is a contradiction → compile error ⚠️
super     : call the parent constructor or method
this      : the current instance; this() calls another constructor of the same class
Object methods: toString, equals, hashCode, getClass, clone, finalize, wait, notify, notifyAll
EQUALS/HASHCODE CONTRACT ⭐⭐⭐:
   if a.equals(b) then a.hashCode() == b.hashCode()  — MUST hold
   the converse need NOT hold (hash collisions are legal)
   ⚠️ Overriding equals without hashCode breaks HashMap/HashSet lookups
== vs equals ⭐: == compares REFERENCES for objects (values for primitives);
                 equals() compares CONTENT if overridden
String immutability: String is immutable; use StringBuilder for repeated concatenation ⭐
Access modifiers (narrowest → widest):  private < default(package) < protected < public
```

**Java access-modifier table ⭐:**

| Modifier | Same class | Same package | Subclass (other package) | Anywhere |
|---|---|---|---|---|
| `private` | ✅ | ❌ | ❌ | ❌ |
| default (package-private) | ✅ | ✅ | ❌ | ❌ |
| `protected` | ✅ | ✅ | ✅ | ❌ |
| `public` | ✅ | ✅ | ✅ | ✅ |

## 1.6 SOLID principles ⭐⭐

| Letter | Principle | One-line meaning |
|---|---|---|
| **S** | Single Responsibility | A class should have one reason to change |
| **O** | Open/Closed | Open for extension, closed for modification |
| **L** | **Liskov Substitution** | A subclass must be usable wherever its base is, without surprising the caller ⭐ |
| **I** | Interface Segregation | Many small interfaces beat one fat one; no client should depend on methods it does not use |
| **D** | Dependency Inversion | Depend on abstractions, not concretions |

⭐ **The classic LSP violation:** `Square extends Rectangle`. Setting the width of a Rectangle
should not change its height — but it must for a Square, so code written against Rectangle breaks.
Interviewers love this example.

## 1.7 Design patterns worth naming ⭐
```
CREATIONAL : Singleton, Factory Method, Abstract Factory, Builder, Prototype
STRUCTURAL : Adapter, Decorator, Facade, Proxy, Composite, Bridge, Flyweight
BEHAVIOURAL: Observer, Strategy, Command, Iterator, Template Method, State,
             Chain of Responsibility, Mediator, Visitor
```
(Full treatment in `../../04_System_Design/04_Design_Patterns/`.)

---

# Part 2 — MCQ Bank (20 questions with full explanations)

---

**Q1.** Which of the following is **not** one of the four pillars of OOP?
```
(a) Encapsulation    (b) Inheritance    (c) Compilation    (d) Polymorphism
```
<details><summary>Answer: (c) Compilation</summary>

The four pillars are **encapsulation, abstraction, inheritance and polymorphism**. Compilation is a
step in the build process and has nothing to do with the object model.
</details>

---

**Q2.** Method overloading is resolved at:
```
(a) run time    (b) compile time    (c) link time    (d) load time
```
<details><summary>Answer: (b) compile time</summary>

The compiler selects the overload by matching the **argument types at the call site** — this is
static/early binding. Nothing about the runtime object affects the choice.

**Contrast:** method **overriding** is resolved at **run time** via the vtable (dynamic/late
binding), because the compiler cannot know the actual object type behind a base reference.
</details>

---

**Q3.** In C++, what happens if a base class destructor is **not** declared `virtual` and an object
is deleted through a base-class pointer?
```
(a) Nothing unusual
(b) Only the base destructor runs; the derived destructor is skipped
(c) A compile error
(d) The derived destructor runs twice
```
<details><summary>Answer: (b)</summary>

```cpp
class Base            { public: ~Base()          { /* not virtual */ } };
class Derived : Base  { int* p; public: ~Derived() { delete[] p; } };

Base* ptr = new Derived();
delete ptr;        // UNDEFINED BEHAVIOUR: only ~Base() runs → p leaks ⚠️
```
Without `virtual`, the call is statically bound to `~Base`, so `~Derived` never executes and any
resource it owned is leaked.

⭐ **The rule:** *if a class is intended to be a base class and objects may be deleted through a
base pointer, its destructor must be `virtual`* (or the class must be non-polymorphic and never
deleted polymorphically). This is one of the most frequently asked C++ questions.
</details>

---

**Q4.** Which of these **cannot** be overridden in Java?
```
(a) A public method    (b) A protected method    (c) A static method    (d) An abstract method
```
<details><summary>Answer: (c) A static method</summary>

Static methods belong to the **class**, not to an instance, so they are not part of the dynamic
dispatch mechanism. Declaring a static method with the same signature in a subclass **hides** the
parent's rather than overriding it, and the call is resolved by the **reference type**:
```java
class A { static void f() { System.out.println("A"); } }
class B extends A { static void f() { System.out.println("B"); } }

A obj = new B();
obj.f();          // prints "A" — resolved by the REFERENCE type ⚠️
```
`final` and `private` methods also cannot be overridden (private ones are not even visible).
</details>

---

**Q5.** An **abstract class** in Java:
```
(a) can have a constructor
(b) cannot have any concrete methods
(c) can be instantiated directly
(d) cannot have instance variables
```
<details><summary>Answer: (a)</summary>

An abstract class **can** have a constructor, which runs when a concrete subclass is instantiated
(via the implicit or explicit `super()` call) and initialises the inherited state.

**Why the others are false:**
- **(b)** It may have any mix of abstract and concrete methods — indeed it may have none abstract
  at all.
- **(c)** It cannot be instantiated; `new AbstractClass()` is a compile error. (An *anonymous
  subclass* `new AbstractClass(){...}` is legal and is a common point of confusion.)
- **(d)** It may have instance variables of any kind, including mutable ones — that is a key
  difference from an interface.
</details>

---

**Q6.** What is the **diamond problem**?
```
(a) A memory leak in circular references
(b) Ambiguity when a class inherits the same member from two paths
(c) An infinite recursion in constructors
(d) A performance problem with deep hierarchies
```
<details><summary>Answer: (b)</summary>

```
      A
     / \
    B   C        Both B and C inherit A and may override f()
     \ /
      D          D inherits two versions of A's members — which one applies?
```

**Resolutions:**
- **C++:** virtual inheritance — `class B : virtual public A` ensures only **one** shared `A`
  subobject exists in `D`.
- **Java/C#:** multiple class inheritance is simply forbidden. Multiple *interface* inheritance is
  allowed, and since Java 8 (default methods) a conflict must be resolved explicitly by the
  implementing class with `InterfaceName.super.method()`.
</details>

---

**Q7.** In Java, `==` applied to two `String` objects compares:
```
(a) their contents    (b) their references    (c) their lengths    (d) their hash codes
```
<details><summary>Answer: (b) references</summary>

For objects, `==` compares **references** (whether both point to the same object). `.equals()`
compares contents, and `String` overrides `equals()` to do a character-by-character comparison.

⚠️ **The trap that makes this confusing:**
```java
String a = "hello";           String b = "hello";
a == b        // TRUE  — both refer to the same interned literal in the string pool

String c = new String("hello");
a == c        // FALSE — `new` forces a distinct object
a.equals(c)   // TRUE
```
The string-literal pool makes `==` *appear* to work for literals, which is exactly why the question
is asked. **Always use `.equals()` for content comparison.** ⭐
</details>

---

**Q8.** Which statement about **interfaces** in Java 8+ is correct?
```
(a) They can have constructors
(b) They can have default and static methods with bodies
(c) They can have private instance variables
(d) A class can implement only one interface
```
<details><summary>Answer: (b)</summary>

Java 8 added **`default`** methods (with a body, inherited by implementers) and **`static`**
methods to interfaces; Java 9 added **private** methods (as helpers for default methods).

**Why the others are false:** interfaces have no constructors (nothing to construct); their fields
are implicitly `public static final` constants, not instance variables; and a class may implement
any number of interfaces — which is how Java provides multiple inheritance of *type*.
</details>

---

**Q9.** What does this C++ code print?
```cpp
class Base    { public: void show() { cout << "Base"; } };
class Derived : public Base { public: void show() { cout << "Derived"; } };
int main() { Base* p = new Derived(); p->show(); }
```
```
(a) Base    (b) Derived    (c) Compile error    (d) Undefined
```
<details><summary>Answer: (a) Base</summary>

`show()` is **not `virtual`**, so the call is resolved **statically** by the pointer's declared type
`Base*` — early binding. The derived version is never consulted.

**Add `virtual` to `Base::show()`** and the same code prints `Derived`, because the call then goes
through the vtable and is resolved by the **actual object type**.

⭐ This one-word difference is the essence of run-time polymorphism in C++, and the question is
asked in exactly this form very often.
</details>

---

**Q10.** In C++, **object slicing** occurs when:
```
(a) a derived object is assigned to a base object by value
(b) a base pointer points to a derived object
(c) a virtual function is called
(d) an object is deleted twice
```
<details><summary>Answer: (a)</summary>

```cpp
Derived d;
Base b = d;     // SLICED: only the Base subobject is copied; Derived's members are lost ⚠️
b.virtualFn();  // calls Base's version — polymorphism is gone

Base& r = d;    // fine — reference, no copy, polymorphism preserved
Base* p = &d;   // fine — pointer, polymorphism preserved
```
The copy constructor `Base(const Base&)` accepts the `Derived` object by reference-to-base and
copies only the base portion.

⭐ **The rule:** *use references or pointers for polymorphism; never pass polymorphic objects by
value.* This is also why `std::vector<Base>` cannot hold derived objects polymorphically and
`std::vector<std::unique_ptr<Base>>` is used instead.
</details>

---

**Q11.** Which access modifier allows access from a **subclass in a different package** but not
from unrelated classes in that package?
```
(a) private    (b) default    (c) protected    (d) public
```
<details><summary>Answer: (c) protected</summary>

`protected` grants access to the same class, the same package, **and** subclasses anywhere —
including in other packages (through inheritance, i.e. on `this` or on a reference of the
subclass's own type).

**Why not (b):** default (package-private) access stops at the package boundary and does **not**
extend to subclasses in other packages. That is the precise difference the question tests. ⭐
</details>

---

**Q12.** Overriding a method in Java requires the overriding method to have:
```
(a) a more restrictive access modifier
(b) the same or a less restrictive access modifier
(c) any access modifier
(d) no access modifier
```
<details><summary>Answer: (b)</summary>

Access **cannot be narrowed** when overriding. A `public` method cannot be overridden as
`protected` or `private` — doing so would break the Liskov Substitution Principle, since code
holding a base reference expects the method to be callable.

Widening is allowed: `protected` in the base may become `public` in the subclass.

⭐ The related rules: the overriding method may not throw **broader checked exceptions**, and the
return type must be the same or **covariant** (a subtype).
</details>

---

**Q13.** What is the output?
```java
class A { int x = 10; }
class B extends A { int x = 20; }
public class Test {
    public static void main(String[] a) {
        A obj = new B();
        System.out.println(obj.x);
    }
}
```
```
(a) 10    (b) 20    (c) Compile error    (d) Runtime error
```
<details><summary>Answer: (a) 10</summary>

**Fields are not polymorphic in Java.** Field access is resolved at **compile time** by the
**reference type**, which is `A`. So `obj.x` reads `A`'s `x` = 10.

Contrast with **methods**, which *are* polymorphic: had `x` been `getX()`, overridden in `B`, the
call would have printed 20.

⭐ **The rule to state in an interview:** *methods are overridden (dynamic dispatch); fields are
hidden (static binding by reference type).* The same applies to `static` methods.
</details>

---

**Q14.** Which of these correctly describes the `equals()` / `hashCode()` contract?
```
(a) Equal objects must have equal hash codes
(b) Objects with equal hash codes must be equal
(c) Both (a) and (b)
(d) Neither is required
```
<details><summary>Answer: (a)</summary>

The contract is **one-directional**: if `a.equals(b)` is true then `a.hashCode() == b.hashCode()`
must be true. The converse is *not* required — two unequal objects may legitimately collide on the
same hash code, which is exactly what hash buckets handle.

⚠️ **Why this matters practically:** `HashMap` first finds the bucket by `hashCode()` and only then
compares with `equals()`. If you override `equals()` without `hashCode()`, two "equal" objects land
in different buckets, and `map.get(key)` silently fails to find an entry you just inserted. This is
one of the most common real-world Java bugs. ⭐⭐
</details>

---

**Q15.** A **pure virtual function** in C++ is declared as:
```
(a) virtual void f();
(b) virtual void f() = 0;
(c) void f() = 0;
(d) abstract void f();
```
<details><summary>Answer: (b)</summary>

`virtual void f() = 0;` declares a pure virtual function, which makes the class **abstract** — it
cannot be instantiated, and any concrete derived class must provide an implementation.

**(a)** is an ordinary virtual function with a body elsewhere; **(c)** is a syntax error (`= 0`
requires `virtual`); **(d)** uses Java/C# syntax, which C++ does not have.

⭐ A pure virtual function *may* still have a definition, which derived classes can call
explicitly — a little-known fact occasionally asked as a follow-up.
</details>

---

**Q16.** Which SOLID principle does this violate?
> A `Rectangle` class has `setWidth` and `setHeight`. A `Square` class extends it and overrides
> both so that setting one also sets the other. Existing code that sets width and height
> independently now produces wrong areas.
```
(a) Single Responsibility    (b) Open/Closed
(c) Liskov Substitution      (d) Dependency Inversion
```
<details><summary>Answer: (c) Liskov Substitution</summary>

LSP requires that objects of a subclass be usable **wherever** the base class is expected, without
the caller needing to know the difference. Here, code written against `Rectangle` that assumes
width and height are independent breaks when handed a `Square` — so `Square` is not a valid subtype
of `Rectangle`, despite the mathematical "is-a" intuition.

**The fix:** do not inherit. Use a common `Shape` interface with an `area()` method, or make both
immutable so the invariant cannot be broken after construction.

⭐ The lesson generalises: *"is-a" in English is not the same as "is substitutable for" in code.*
</details>

---

**Q17.** In C++, the order of constructor execution for `class D : public B` where `D` has a member
object `M m;` is:
```
(a) D, B, M    (b) B, M, D    (c) M, B, D    (d) B, D, M
```
<details><summary>Answer: (b) B, M, D</summary>

```
1. Base class constructor(s)        — B
2. Member objects, in DECLARATION order   — M     ⚠️ not initialiser-list order
3. The derived class's constructor body — D
```
Destructors run in **exactly the reverse order**: `~D` body, then members in reverse declaration
order, then `~B`.

⚠️ The "declaration order, not initialiser-list order" detail is a genuine gotcha — writing the
initialiser list out of order produces a compiler warning precisely because the order it implies is
not the order that happens. ⭐
</details>

---

**Q18.** Which is **true** of a `final` class in Java?
```
(a) Its methods cannot be called
(b) It cannot be extended
(c) It cannot have a constructor
(d) It cannot be instantiated
```
<details><summary>Answer: (b)</summary>

`final` on a class prevents **subclassing**. `String`, `Integer` and the other wrapper types are
final, which is what makes their immutability guaranteeable (a subclass could otherwise add mutable
state or override behaviour).

**Why the others are false:** a final class can be instantiated, has constructors, and its methods
are callable normally. (Its methods are also implicitly final, since there is nothing to override
them from.)

⚠️ Note `final` on a **variable** means it can be assigned once — but for a reference, the *object*
remains mutable: `final List<Integer> l = new ArrayList<>(); l.add(1);` is legal. ⭐
</details>

---

**Q19.** What does this print?
```java
class A { A() { System.out.print("A"); } }
class B extends A { B() { System.out.print("B"); } }
class C extends B { C() { System.out.print("C"); } }
public class Test { public static void main(String[] x) { new C(); } }
```
```
(a) CBA    (b) ABC    (c) C    (d) Compile error
```
<details><summary>Answer: (b) ABC</summary>

Every constructor implicitly calls `super()` as its **first statement** before its own body runs.
So the chain goes *up* to the root and then executes *downward*:
```
new C()  →  C() calls super() → B() calls super() → A() runs, prints "A"
                                   ← B() body prints "B"
            ← C() body prints "C"
Output: ABC
```
⭐ The same principle in C++ (Q17): base before derived, always.
</details>

---

**Q20.** Which statement about **composition vs inheritance** is correct?
```
(a) Inheritance should always be preferred because it maximises reuse
(b) Composition models a "has-a" relationship and is generally more flexible
(c) Composition cannot achieve code reuse
(d) They are functionally identical
```
<details><summary>Answer: (b)</summary>

Composition models **"has-a"** (a `Car` *has an* `Engine`) and inheritance models **"is-a"** (a
`Car` *is a* `Vehicle`).

**Why composition is generally preferred ⭐⭐:**
- It does not expose the parent's implementation to the subclass, so it preserves encapsulation.
- The composed object can be **swapped at run time** (this is the Strategy pattern).
- It avoids the fragile-base-class problem, where a change in the parent silently breaks subclasses.
- It sidesteps the diamond problem and deep hierarchies entirely.

**Why not (a):** inheritance creates the tightest coupling available in OOP and should be reserved
for genuine, substitutable "is-a" relationships (see LSP, Q16).
**Why not (c):** composition achieves reuse by delegation — the composing class simply forwards
calls.

⭐ The standard formulation: *"favour composition over inheritance"* (Gang of Four).
</details>

---

## Scoring

| Correct | Read as |
|---|---|
| 18-20 | OOP is interview-ready |
| 14-17 | Revisit overriding rules and the C++ output questions |
| 10-13 | Re-read Part 1 and redo the bank |
| < 10 | Half a day here; OOP opens most technical interviews |

---

## Recall questions

1. Distinguish abstraction from encapsulation in one sentence each.
2. Give eight differences between overloading and overriding.
3. Why can a static method not be overridden, and what happens instead?
4. Explain the diamond problem and both language solutions.
5. When is a virtual destructor required, and what goes wrong without one?
6. What is object slicing and how do you avoid it?
7. Compare abstract classes and interfaces on six dimensions.
8. State the equals/hashCode contract and the bug caused by breaking it.
9. Give the constructor and destructor execution order for a derived class with member objects.
10. Explain LSP with the Rectangle/Square example and give the fix.
