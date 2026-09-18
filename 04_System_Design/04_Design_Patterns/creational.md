# Creational Patterns

> Patterns about **how objects are created** — decoupling the code that uses an object from the code that constructs it.

---

## Singleton

**Intent.** Ensure a class has exactly one instance, with a global access point.

**When it genuinely applies.** A single physical resource: a connection pool, a configuration registry, a logger, a hardware device handle.

```python
class ConfigRegistry:
    _instance = None
    _lock = threading.Lock()

    def __new__(cls):
        if cls._instance is None:
            with cls._lock:                 # double-checked locking
                if cls._instance is None:
                    cls._instance = super().__new__(cls)
                    cls._instance._load()
        return cls._instance
```

**The thread-safety detail interviewers probe:** naive lazy initialisation has a race where two threads both see `None` and both construct. Double-checked locking (check, lock, check again) fixes it. In Java the field must be `volatile`, or a partially-constructed object can be visible to another thread through instruction reordering. In Python, a module-level instance is already a singleton and is the idiomatic solution.

**Why it is widely considered an anti-pattern — and this is the answer to give:**
- It is **global mutable state** with a friendly name.
- It **hides dependencies**: a class using it does not declare that it does, so its constructor lies about what it needs.
- It **destroys testability**: you cannot substitute a test double, and state leaks between tests.
- It is **hard to get right under concurrency**, as above.
- Subclassing and lifecycle management are awkward.

**The mature position:** if you need exactly one instance, create one and **inject it**. Dependency injection gives you the single instance without the global access point, which removes every disadvantage while keeping the benefit. Say this — it is what distinguishes someone who has been bitten by Singleton from someone who has only read about it.

---

## Factory Method

**Intent.** Define an interface for creating an object, but let subclasses decide which class to instantiate.

**The problem it solves.** A `switch` on a type code, scattered through the codebase, that must be edited every time a type is added — an Open/Closed violation.

```python
class Notification(ABC):
    @abstractmethod
    def send(self, msg: str) -> None: ...

class EmailNotification(Notification): ...
class SmsNotification(Notification): ...

class NotificationFactory:
    _registry = {}

    @classmethod
    def register(cls, key: str, ctor) -> None:
        cls._registry[key] = ctor

    @classmethod
    def create(cls, key: str) -> Notification:
        if key not in cls._registry:
            raise ValueError(f"unknown notification type: {key}")
        return cls._registry[key]()

NotificationFactory.register("email", EmailNotification)
NotificationFactory.register("sms", SmsNotification)
```

**The registry variant above is worth showing**, because it makes the factory itself closed for modification — adding a type is a `register` call, not an edit to the factory.

**Where you have already seen it:** `Calendar.getInstance()`, `NumberFormat.getInstance()`, `Iterator` creation, the DOM's `createElement`.

**When not to use it:** when there is exactly one implementation and no prospect of a second. A factory that wraps a single constructor is ceremony.

---

## Abstract Factory

**Intent.** Create **families of related objects** without specifying their concrete classes.

**The distinction from Factory Method** — a common interview question: Factory Method creates **one** product; Abstract Factory creates a **family** of products that must be used together and must be mutually consistent.

```python
class UIFactory(ABC):
    @abstractmethod
    def create_button(self) -> Button: ...
    @abstractmethod
    def create_checkbox(self) -> Checkbox: ...

class MacFactory(UIFactory):
    def create_button(self):   return MacButton()
    def create_checkbox(self): return MacCheckbox()

class WindowsFactory(UIFactory):
    def create_button(self):   return WindowsButton()
    def create_checkbox(self): return WindowsCheckbox()
```

**The invariant it protects:** you cannot accidentally combine a Mac button with a Windows checkbox. The family is chosen once, at the factory, and consistency follows.

**Real uses:** cross-platform UI toolkits, database driver families (connection, statement, result set must all match the vendor), and environment-specific service bundles (production versus test implementations of a whole set of collaborators).

**Its weakness:** adding a new *product* to the family (say, a `Slider`) means changing the abstract factory interface and **every** implementation. It is open to new families, closed to new product types.

---

## Builder

**Intent.** Construct a complex object step by step, separating construction from representation.

**The problem it solves — the telescoping constructor:**

```java
new Pizza(12, "thin", true, false, true, false, true, 3);   // unreadable
new Pizza(12, "thin");
new Pizza(12, "thin", true);
new Pizza(12, "thin", true, false);                          // and so on
```

Unreadable at the call site, and impossible to extend without adding another overload.

```python
class Pizza:
    def __init__(self, builder):
        self.size = builder.size
        self.crust = builder.crust
        self.toppings = builder.toppings

    class Builder:
        def __init__(self, size: int):
            self.size = size                # required parameters in the constructor
            self.crust = "regular"          # optional ones get defaults
            self.toppings = []

        def with_crust(self, crust): self.crust = crust; return self
        def add_topping(self, t):    self.toppings.append(t); return self

        def build(self):
            if self.size not in (8, 12, 16):
                raise ValueError("invalid size")     # validate before construction
            return Pizza(self)

pizza = Pizza.Builder(12).with_crust("thin").add_topping("basil").build()
```

**Three benefits worth naming:**
1. **Readable at the call site** — each argument is labelled by its method name.
2. **Validation in `build()`** — the object is never constructed in an invalid state, so it can be immutable.
3. **Immutability** — all fields can be final, set once at construction.

**Where you have seen it:** `StringBuilder`, `Stream.Builder`, `HttpRequest.newBuilder()`, most HTTP client and query-builder APIs.

**When not to use it:** for an object with three or four parameters. Python's keyword arguments and default values already solve the readability problem, so Builder is much less necessary there than in Java.

---

## Prototype

**Intent.** Create new objects by **cloning** an existing instance rather than constructing from scratch.

**When it applies.** When construction is genuinely expensive — parsing a large configuration, loading a template, building a populated object graph — and you need many similar instances.

```python
import copy

class DocumentTemplate:
    def __init__(self, layout, styles, sections):
        self.layout, self.styles, self.sections = layout, styles, sections

    def clone(self) -> "DocumentTemplate":
        return copy.deepcopy(self)          # deep: the sections must not be shared
```

**The critical detail:** shallow versus deep copy. A shallow clone shares the nested objects, so mutating one clone's `sections` is visible through every other — the same trap as Java's `clone()` and Python's `copy.copy`. Decide deliberately and document it.

**Real uses:** document and object templates, game entity spawning, and the JavaScript prototype chain (which is this pattern at the language level).

---

## Object Pool

**Intent.** Reuse expensive-to-create objects instead of creating and destroying them repeatedly.

**Where it is genuinely used:** database connection pools, thread pools, buffer pools, socket pools.

**Why it matters in system design:** opening a database connection costs a TCP handshake plus authentication — milliseconds — and each connection consumes memory on the server. With 100 application instances each opening 50 connections, the database sees 5,000 connections and spends its time context-switching. A pool bounds this, and a **connection pooler** (PgBouncer, ProxySQL) multiplexes many application connections onto few database ones.

**The pitfalls:** objects must be **reset** before reuse or state leaks between users (a genuine security bug when the state is user data); a pool that is too small becomes a bottleneck; and a leaked object that is never returned shrinks the pool until it is empty and everything blocks.

---

## Choosing between them

| Situation | Pattern |
|---|---|
| Exactly one instance of a real resource | Singleton — but prefer **injecting** one instance |
| Choose an implementation from a type code | **Factory Method** |
| Choose a *consistent family* of implementations | **Abstract Factory** |
| Many optional parameters, want immutability and validation | **Builder** |
| Construction is expensive; clone a prepared instance | **Prototype** |
| Objects are expensive to create and are reused constantly | **Object Pool** |

---

## Recall questions

1. Give four reasons Singleton is considered an anti-pattern, and the mature alternative.
2. Describe double-checked locking and why Java needs `volatile`.
3. What Open/Closed violation does Factory Method fix?
4. How does the registry variant make the factory itself closed for modification?
5. State the difference between Factory Method and Abstract Factory, and the invariant Abstract Factory protects.
6. What is Abstract Factory's weakness?
7. What is the telescoping constructor problem, and give Builder's three benefits.
8. Why is Builder less necessary in Python than in Java?
9. What is the critical decision when implementing Prototype?
10. Why does a connection pool exist, with the numbers?
11. Name three pitfalls of object pooling.
