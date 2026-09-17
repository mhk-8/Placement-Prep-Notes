# OOP — Diagrams

---

## 1. The four pillars

```
   ┌──────────────────────────────────────────────────────────┐
   │ ENCAPSULATION   data + methods bundled; invariants held   │
   │                 "can an outsider corrupt this object?"    │
   ├──────────────────────────────────────────────────────────┤
   │ ABSTRACTION     minimal interface; mechanism hidden       │
   │                 hides COMPLEXITY (encapsulation hides DATA)│
   ├──────────────────────────────────────────────────────────┤
   │ INHERITANCE     is-a; substitutability, not reuse         │
   ├──────────────────────────────────────────────────────────┤
   │ POLYMORPHISM    compile-time: overloading (declared type) │
   │                 runtime:      overriding  (actual type)   │
   └──────────────────────────────────────────────────────────┘
```

---

## 2. Static vs dynamic binding

```
   A a = new B();

   a.instanceMethod()   ──► resolved at RUNTIME from the OBJECT   → B's version
   a.staticMethod()     ──► resolved at COMPILE TIME from the     → A's version
                            DECLARED type
   a.field              ──► resolved at COMPILE TIME              → A's field

               declared type A ─┐
                                ├──► compiler picks: static methods, fields,
               actual type B ───┤                    overload resolution
                                └──► runtime picks:  overridden instance methods
```

**Memorise:** only overridden **instance methods** are polymorphic. Everything else follows the declared type.

---

## 3. vtable and dynamic dispatch

```
   Object of class D                 D's vtable
   ┌──────────────┐               ┌──────────────────┐
   │ vptr  ───────┼──────────────►│ &D::speak        │
   ├──────────────┤               │ &B::walk         │  (inherited, not overridden)
   │ field a      │               │ &D::~D           │
   │ field b      │               └──────────────────┘
   └──────────────┘

   p->speak()   ≡   (*(p->vptr[0]))(p)     ← one indirect call
```

Cost: one extra pointer per object, one indirection per call, and the call usually cannot be inlined. That is the entire price of runtime polymorphism.

---

## 4. Abstract class vs interface

```
        ABSTRACT CLASS                        INTERFACE
   ┌────────────────────────┐          ┌────────────────────────┐
   │ fields (state)     ✔   │          │ constants only     ✔   │
   │ constructors       ✔   │          │ constructors       ✘   │
   │ concrete methods   ✔   │          │ default methods  (8+)  │
   │ abstract methods   ✔   │          │ abstract methods   ✔   │
   │ ONE per class          │          │ MANY per class         │
   └────────────────────────┘          └────────────────────────┘
        models  IS-A                        models  CAN-DO
        AbstractList                        Comparable, Runnable
```

---

## 5. The diamond problem

```
              A
            ╱   ╲
           B     C          D inherits from both B and C.
            ╲   ╱           Which A does D get — one copy or two?
              D

   JAVA    forbidden for classes; interfaces may be multiply inherited,
           and conflicting default methods must be resolved explicitly
   C++     virtual inheritance:  class B : virtual public A
           → a single shared A subobject
   PYTHON  C3 linearisation (MRO): D → B → C → A → object
           inspect with  D.__mro__
```

---

## 6. Composition vs inheritance

```
   INHERITANCE (is-a)                 COMPOSITION (has-a)
   ┌──────────┐                       ┌──────────┐      ┌──────────┐
   │   Bird   │                       │   Car    │◆────►│  Engine  │
   └────▲─────┘                       └──────────┘      └──────────┘
        │                                  │
   ┌────┴─────┐                            └──► behaviour swappable at RUNTIME
   │ Penguin  │  ← but Penguin can't fly:        (this IS the Strategy pattern)
   └──────────┘    the hierarchy lied
```

**The test before using inheritance:** can every use of the base be replaced by the derived without surprising the caller? If not, compose.

---

## 7. Constructor and initialisation order (Java)

```
   CLASS LOAD (once)
     1. static fields  →  default values
     2. static initialisers + static field assignments, in SOURCE order

   EACH  new  (per object)
     3. instance fields → default values
     4. super() — the ENTIRE superclass chain completes first
     5. instance initialiser blocks + field assignments, in SOURCE order
     6. the constructor body
```

**The trap:** if the superclass constructor (step 4) calls an overridable method, the subclass override runs **before** step 5 — so it sees its own fields as `null` / `0`. Never call an overridable method from a constructor.

---

## 8. Object slicing (C++)

```
   Derived d;                     ┌─────────────┐
                                  │ base part   │
   Base b = d;   ← SLICED         │ derived part│ ✂ discarded
                                  └─────────────┘

   void f(Base b)   ← by value:  sliced, polymorphism lost, prints Base's version
   void f(Base& b)  ← by ref:    intact, virtual dispatch works
   void f(Base* b)  ← by ptr:    intact
```
