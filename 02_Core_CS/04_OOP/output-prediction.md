# OOP — Output Prediction

> Output-prediction snippets are the most common OOP format in OAs. They all test the same handful of rules, so learning the rules beats memorising snippets.
> **Method:** trace on paper with a two-column table (expression → resolved target). Mentally tracing an inheritance chain is unreliable under time pressure.

**The four rules that explain almost every trap:**
1. **Overridden instance methods** dispatch on the **actual object**.
2. **Static methods, fields and C++ default arguments** resolve from the **declared type**.
3. **Constructors** run superclass-first, and an overridable method called from a constructor runs the subclass version *before* subclass fields are initialised.
4. **Overload resolution** happens at compile time using declared types; **override resolution** happens at runtime.

---

## P1. Dynamic dispatch (Java)

```java
class A { void show() { System.out.println("A"); } }
class B extends A { void show() { System.out.println("B"); } }

public class Main {
    public static void main(String[] args) {
        A obj = new B();
        obj.show();
    }
}
```
<details><summary>Output</summary>

**`B`**

`show()` is an overridden **instance** method, so dispatch uses the actual object (a `B`), not the declared type `A`. This is the baseline case — everything below is a deviation from it.
</details>

---

## P2. Static methods are hidden, not overridden (Java)

```java
class A { static void f() { System.out.println("A.f"); } }
class B extends A { static void f() { System.out.println("B.f"); } }

A obj = new B();
obj.f();
```
<details><summary>Output</summary>

**`A.f`**

Static methods belong to the **class**, not the instance. The compiler resolves `obj.f()` from the *declared* type `A`. `B.f()` **hides** `A.f()`, it does not override it — and calling a static method through an instance reference is itself a warning in most compilers.

Change `A obj` to `B obj` and the output becomes `B.f`, which is the giveaway that binding is static.
</details>

---

## P3. Fields are not polymorphic (Java)

```java
class A { int x = 10; }
class B extends A { int x = 20; }

A a = new B();
B b = new B();
System.out.println(a.x + " " + b.x + " " + ((A) b).x);
```
<details><summary>Output</summary>

**`10 20 10`**

Field access is resolved at compile time from the declared type. The `B` object contains **both** fields — `A.x` = 10 and `B.x` = 20 — and which one you see depends on the reference type you look through. Casting to `A` selects `A.x` again.

**This is field hiding**, and it is why exposing public fields in a hierarchy is a design error rather than merely a style one.
</details>

---

## P4. Constructor order and the overridable-method trap (Java)

```java
class A {
    A() { System.out.println("A ctor"); init(); }
    void init() { System.out.println("A.init"); }
}
class B extends A {
    int value = 42;
    B() { System.out.println("B ctor"); }
    void init() { System.out.println("B.init, value = " + value); }
}

new B();
```
<details><summary>Output</summary>

```
A ctor
B.init, value = 0
B ctor
```

Trace it: `new B()` implicitly calls `super()` first → `A`'s constructor prints, then calls `init()`. Because `init()` is overridden, **B's version runs** — but B's field initialisers have not executed yet, so `value` is still the default `0`. Only after `A`'s constructor returns does `value = 42` run, followed by B's constructor body.

**The rule this teaches:** never call an overridable method from a constructor. It is the reason `final` exists on many framework base-class methods.
</details>

---

## P5. Virtual destructor (C++)

```cpp
#include <iostream>
using namespace std;
class Base    { public: ~Base()    { cout << "~Base "; } };
class Derived : public Base { public: ~Derived() { cout << "~Derived "; } };

int main() { Base* p = new Derived(); delete p; }
```
<details><summary>Output</summary>

**`~Base `** — and `~Derived` is **never called**.

Deleting a derived object through a base pointer whose destructor is **not virtual** is undefined behaviour; in practice only the base destructor runs, leaking whatever the derived class owned.

**The fix:** `virtual ~Base() { ... }`. Then the output becomes `~Derived ~Base ` — destructors run derived-first, the reverse of construction order.

**Rule:** any class intended to be used polymorphically must have a virtual destructor. This is the most-asked C++ OOP question there is.
</details>

---

## P6. Object slicing (C++)

```cpp
class Base    { public: virtual void f() { cout << "Base"; } };
class Derived : public Base { public: void f() override { cout << "Derived"; } };

void byValue(Base b)  { b.f(); }
void byRef  (Base& b) { b.f(); }

Derived d;
byValue(d);   cout << " ";
byRef(d);
```
<details><summary>Output</summary>

**`Base Derived`**

Passing by value copy-constructs a `Base` from the `Derived`, keeping only the base subobject — the derived part is **sliced off**, along with the vptr that pointed at `Derived`'s vtable. Passing by reference leaves the object intact, so virtual dispatch works.

**Rule:** pass polymorphic types by reference or pointer, never by value.
</details>

---

## P7. Default arguments are statically bound (C++)

```cpp
class Base    { public: virtual void f(int x = 10) { cout << "Base " << x; } };
class Derived : public Base { public: void f(int x = 20) override { cout << "Derived " << x; } };

Base* p = new Derived();
p->f();
```
<details><summary>Output</summary>

**`Derived 10`**

The *function* is chosen dynamically (Derived's override runs), but the *default argument* is substituted by the compiler from the **declared** type `Base`. You get Derived's body with Base's default — almost certainly not what anyone intended.

**Rule:** never give a virtual function a default argument. This appears in OAs precisely because the answer looks wrong.
</details>

---

## P8. Integer caching (Java)

```java
Integer a = 127, b = 127;
Integer c = 128, d = 128;
System.out.println((a == b) + " " + (c == d) + " " + c.equals(d));
```
<details><summary>Output</summary>

**`true false true`**

Java caches boxed `Integer` objects in the range **−128 to 127**, so `a` and `b` are the *same object* and `==` (reference comparison) is true. At 128 the cache ends, so `c` and `d` are distinct objects and `==` is false. `.equals()` compares values and is true in both cases.

**Rule:** never compare boxed values with `==`. This is a real production bug, not just an exam question.
</details>

---

## P9. String interning (Java)

```java
String s1 = "abc";
String s2 = "abc";
String s3 = new String("abc");
String s4 = s3.intern();
System.out.println((s1 == s2) + " " + (s1 == s3) + " " + (s1 == s4) + " " + s1.equals(s3));
```
<details><summary>Output</summary>

**`true false true true`**

String literals are interned in a shared pool, so `s1` and `s2` are one object. `new String(...)` forces a fresh heap object, so `s1 == s3` is false. `intern()` returns the pooled instance, so `s1 == s4` is true. `equals` always compares content.
</details>

---

## P10. `finally` overriding a return (Java)

```java
static int f() {
    try { return 1; }
    finally { return 2; }
}
System.out.println(f());
```
<details><summary>Output</summary>

**`2`**

`finally` runs before the method actually returns, and a `return` inside it **replaces** the pending return value — it will even swallow a pending exception.

**Rule:** never `return` or `throw` from a `finally` block. Compilers warn about it for exactly this reason.

Variant worth knowing: if `finally` only *modifies a local variable* that was already returned, the original value stands, because the return value was copied before `finally` ran.
</details>

---

## P11. Overload resolution is compile-time (Java)

```java
class A {}
class B extends A {}

static void f(A a) { System.out.println("f(A)"); }
static void f(B b) { System.out.println("f(B)"); }

A obj = new B();
f(obj);
```
<details><summary>Output</summary>

**`f(A)`**

Overload resolution uses the **declared** type of the argument, which is `A`. Runtime polymorphism applies to *overriding*, never to *overloading*.

This is the cleanest demonstration of the overloading/overriding split, and it is the single most common conceptual error in this topic.
</details>

---

## P12. Static initialisation order (Java)

```java
class T {
    static int a = f("a", 1);
    static { System.out.println("static block"); }
    static int b = f("b", 2);
    int c = f("c", 3);
    T() { System.out.println("ctor"); }
    static int f(String s, int v) { System.out.println(s); return v; }
}
new T(); new T();
```
<details><summary>Output</summary>

```
a
static block
b
c
ctor
c
ctor
```

Static fields and static blocks execute **once**, at class load, in source order. Instance initialisers and the constructor execute on **every** instantiation, also in source order with the constructor body last.
</details>

---

## P13. Python MRO (C3 linearisation)

```python
class A:
    def who(self): return "A"
class B(A):
    def who(self): return "B"
class C(A):
    def who(self): return "C"
class D(B, C):
    pass

print(D().who())
print([c.__name__ for c in D.__mro__])
```
<details><summary>Output</summary>

```
B
['D', 'B', 'C', 'A', 'object']
```

Python resolves the diamond with **C3 linearisation**: depth-first, left-to-right, but with each class appearing after all of its subclasses. `D` finds `who` on `B` first.

Swap the bases to `class D(C, B)` and the answer becomes `C`. Being able to print `__mro__` rather than guessing is the practical takeaway.
</details>

---

## P14. Shallow copy (Python)

```python
import copy
a = [[1, 2], [3, 4]]
b = copy.copy(a)        # shallow
c = copy.deepcopy(a)    # deep
a[0][0] = 99
print(b[0][0], c[0][0])
```
<details><summary>Output</summary>

**`99 1`**

A shallow copy duplicates the outer list but shares the inner lists, so the mutation is visible through `b`. A deep copy duplicates the whole object graph, so `c` is unaffected.

The same distinction applies to Java's `clone()`, which is shallow by default — a frequent source of subtle bugs.
</details>

---

## P15. `equals` without `hashCode` (Java)

```java
class P {
    int id;
    P(int id) { this.id = id; }
    @Override public boolean equals(Object o) {
        return o instanceof P && ((P) o).id == this.id;
    }
    // hashCode NOT overridden
}

Set<P> s = new HashSet<>();
s.add(new P(1));
System.out.println(s.contains(new P(1)) + " " + s.size());
```
<details><summary>Output</summary>

**`false 1`**

The two objects are `equals`, but they inherit `Object.hashCode()`, which is identity-based — so they land in different buckets and `contains` never even reaches the `equals` check.

**The contract:** equal objects must have equal hash codes. Violating it breaks every hash-based collection silently, which is why IDEs generate the two methods together.
</details>

---

## Practice snippets

1. In Java, what does `A a = new B(); System.out.println(a instanceof B);` print when `B extends A`?
2. In C++, what happens if a pure virtual function is not overridden in a derived class?
3. What does `System.out.println(0.1 + 0.2 == 0.3);` print in Java?
4. In Java, can a subclass override a method and make it `private`?
5. What is printed by `class X { X() { this(5); System.out.print("no-arg "); } X(int i) { System.out.print("int "); } } new X();`?

<details><summary>Answers</summary>

1. **`true`** — `instanceof` tests the actual object's type, not the declared one.
2. The derived class remains **abstract** and cannot be instantiated; attempting to do so is a compile error.
3. **`false`** — 0.1 and 0.2 are not exactly representable in binary floating point, so the sum is 0.30000000000000004.
4. **No** — an override may not reduce visibility. Widening (`protected` → `public`) is allowed; narrowing is a compile error.
5. **`int no-arg `** — `this(5)` delegates to the other constructor first, and it must be the first statement.

</details>
