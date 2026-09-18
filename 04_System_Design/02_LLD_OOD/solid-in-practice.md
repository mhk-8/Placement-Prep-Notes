# SOLID in Practice

> Reciting the five principles is worth nothing. Being able to show a violation, explain the *symptom* it causes, and fix it is what gets graded — and it is what makes the principles actually useful when you design.

---

## S — Single Responsibility Principle

**A class should have one reason to change.**

Note that "reason to change" means a **stakeholder or axis of change**, not "does one thing". A class can have many methods and still one responsibility.

### Violation

```python
class Report:
    def calculate_totals(self): ...      # changes when business rules change
    def render_html(self): ...           # changes when the design team changes layout
    def send_email(self): ...            # changes when the mail provider changes
    def save_to_db(self): ...            # changes when the schema changes
```

**The symptom:** four unrelated teams all edit this file, merge conflicts are constant, and a mail-provider change risks breaking the totals. Tests for the calculation require mocking a mail server.

### Fix

```python
class ReportCalculator:  # business rules
    def calculate(self, data) -> ReportData: ...

class ReportRenderer:    # presentation
    def render(self, report: ReportData) -> str: ...

class ReportMailer:      # delivery
    def send(self, html: str, to: str) -> None: ...

class ReportRepository:  # persistence
    def save(self, report: ReportData) -> None: ...
```

Each now has one axis of change, and each is testable in isolation.

### The test to apply
Describe the class in one sentence. **If you need "and", split it.** "Calculates report totals" is fine; "calculates totals and emails the report" is not.

---

## O — Open/Closed Principle

**Open for extension, closed for modification.** You should be able to add behaviour by adding code, not by editing existing, working, tested code.

### Violation

```python
class PaymentProcessor:
    def process(self, payment, method):
        if method == "credit_card":
            # charge card
        elif method == "paypal":
            # call paypal
        elif method == "upi":       # every new method edits this method
            # call upi
```

**The symptom:** adding a payment method means modifying a class that already works, re-testing everything, and risking a regression in code you did not intend to touch. And the same `if` chain almost always appears in three other places — refunds, receipts, reconciliation.

### Fix

```python
from abc import ABC, abstractmethod

class PaymentMethod(ABC):
    @abstractmethod
    def pay(self, amount: Money) -> PaymentResult: ...

class CreditCardPayment(PaymentMethod):
    def pay(self, amount): ...

class UpiPayment(PaymentMethod):
    def pay(self, amount): ...

class PaymentProcessor:
    def process(self, amount: Money, method: PaymentMethod) -> PaymentResult:
        return method.pay(amount)       # never changes again
```

Adding a method is now adding a class. `PaymentProcessor` is closed.

### Recognising it in an interview
**A `switch` or `if/elif` on a type code is the signature of an OCP violation.** When you catch yourself writing one, ask whether polymorphism removes it. This is also exactly what the minute-35 new requirement tests.

**The caveat worth stating:** you cannot be open to *every* axis of change, and trying to is over-engineering. Be open along the axis the requirements suggest will vary — payment methods will multiply; the concept of "payment" will not.

---

## L — Liskov Substitution Principle

**A subtype must be usable anywhere its supertype is, without the caller noticing.**

This is the deepest of the five and the one most often violated by "reasonable-looking" hierarchies.

### Violation 1 — Square / Rectangle

```python
class Rectangle:
    def set_width(self, w):  self.w = w
    def set_height(self, h): self.h = h
    def area(self):          return self.w * self.h

class Square(Rectangle):
    def set_width(self, w):  self.w = self.h = w    # must keep sides equal
    def set_height(self, h): self.w = self.h = h
```

```python
def stretch(r: Rectangle):
    r.set_width(5)
    r.set_height(4)
    assert r.area() == 20      # holds for Rectangle, FAILS for Square (16)
```

The caller's reasonable assumption — setting width leaves height alone — is broken. **Mathematically a square is a rectangle; behaviourally, a mutable Square is not a substitutable Rectangle.** LSP is about behaviour, not taxonomy.

**Fix:** make them siblings under an immutable `Shape` with an `area()`, so the mutator problem never arises.

### Violation 2 — the throwing override

```python
class Bird:
    def fly(self): ...

class Penguin(Bird):
    def fly(self):
        raise NotImplementedError("penguins can't fly")   # LSP violation
```

Any code holding a `Bird` and calling `fly()` now crashes on some birds.

**Fix:** separate the capability — `Bird` and `FlyingBird`, or a `Flyable` interface. (Which is also Interface Segregation.)

### The rules a subtype must respect
- **Preconditions may not be strengthened.** If the parent accepts any integer, the child may not demand a positive one.
- **Postconditions may not be weakened.** If the parent guarantees a sorted result, the child must too.
- **Invariants must be preserved.**
- **No new exceptions** the caller is not prepared for.

### The practical test
Write the code that *uses* the base class. Then ask: **would every subclass satisfy it?** If a caller needs to know which subclass it holds, substitutability is already broken.

---

## I — Interface Segregation Principle

**No client should be forced to depend on methods it does not use.**

### Violation

```python
class Worker(ABC):
    @abstractmethod
    def work(self): ...
    @abstractmethod
    def eat(self): ...
    @abstractmethod
    def sleep(self): ...

class RobotWorker(Worker):
    def work(self):  ...
    def eat(self):   raise NotImplementedError      # meaningless
    def sleep(self): raise NotImplementedError      # meaningless
```

**The symptom:** implementations full of stubs that throw or do nothing — the clearest sign that an interface is too fat. Worse, a change to `eat()` forces `RobotWorker` to recompile and be re-tested for no reason.

### Fix

```python
class Workable(ABC):
    @abstractmethod
    def work(self): ...

class Feedable(ABC):
    @abstractmethod
    def eat(self): ...

class HumanWorker(Workable, Feedable): ...
class RobotWorker(Workable): ...
```

### The test
**Look for implementations that throw `NotImplementedError` or silently do nothing.** Each one is an interface segregation failure. Prefer several small, role-based interfaces to one large one — and name them for the *role* (`Comparable`, `Closeable`, `Runnable`) rather than the type.

---

## D — Dependency Inversion Principle

**High-level modules should not depend on low-level modules; both should depend on abstractions.**

### Violation

```python
class OrderService:
    def __init__(self):
        self.repo = MySQLOrderRepository()      # constructs its own dependency
        self.mailer = SmtpMailer()
```

**The symptoms:**
- **Untestable.** A unit test now needs a real MySQL instance and an SMTP server.
- **Unswappable.** Changing to Postgres means editing `OrderService`.
- **Hidden dependencies.** The constructor signature does not reveal what this class actually needs.

### Fix

```python
class OrderRepository(ABC):
    @abstractmethod
    def save(self, order: Order) -> None: ...

class Mailer(ABC):
    @abstractmethod
    def send(self, to: str, body: str) -> None: ...

class OrderService:
    def __init__(self, repo: OrderRepository, mailer: Mailer):
        self.repo = repo                         # injected
        self.mailer = mailer

# production
OrderService(PostgresOrderRepository(), SesMailer())
# test
OrderService(InMemoryOrderRepository(), FakeMailer())
```

### Why this is the most practically useful of the five
Most code that people describe as "untestable" is really **hard-coded-dependencies** code. Dependency inversion is the fix, and it is why every dependency-injection framework exists.

**The abstraction belongs to the high-level module.** `OrderRepository` is defined by the domain (what an order service needs), not by the database layer (what MySQL offers). That direction is the actual "inversion".

---

## How the five interact

They are not independent — they pull in the same direction.

- **SRP** produces small classes, which makes **ISP** natural (a small class has a small interface).
- **OCP** is usually achieved through polymorphism, which requires **LSP** to be safe — an OCP design where subtypes are not substitutable is worse than the `switch` it replaced.
- **DIP** is what makes OCP practical: you can only swap implementations if callers depend on the abstraction.

**The unifying idea:** isolate what varies behind a stable abstraction, so change is additive rather than invasive.

---

## Using SOLID in an interview

**Do not recite.** Use them as *justification* while designing:

> "I'm putting pricing behind a `PricingStrategy` interface — that's Open/Closed, so weekend pricing becomes a new class rather than an edit here."

> "I'll inject the repository rather than constructing it, so this is testable without a database. That's Dependency Inversion."

> "I'm not making `Square` extend `Rectangle`, because `setWidth` would have to change the height and that breaks what callers expect — a Liskov violation."

**And know the counter-argument.** Applied dogmatically, SOLID produces a haze of tiny interfaces with one implementation each. The honest position: apply a principle when the requirement suggests that axis will actually vary. An interface with exactly one implementation and no prospect of a second is speculative generality, and saying so is a mark of judgement rather than ignorance.

---

## Recall questions

1. State SRP in terms of "reasons to change", and give the one-sentence test.
2. What is the code signature of an OCP violation?
3. What is the OCP caveat — why can you not be open to everything?
4. Why is a mutable `Square extends Rectangle` a Liskov violation? Write the failing caller.
5. Give the four rules a subtype must respect under LSP.
6. What is the practical LSP test?
7. What is the code smell that reveals an ISP violation?
8. Give the DIP violation and its three symptoms.
9. Which module should own the abstraction under DIP, and why is that the "inversion"?
10. Explain how OCP, LSP and DIP depend on each other.
11. What is the honest counter-argument to applying SOLID everywhere?
