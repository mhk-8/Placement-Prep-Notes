# Behavioural Patterns

> Patterns about **how objects communicate and distribute responsibility**. These are the ones that appear most often in LLD rounds, because most "what varies here?" answers are behavioural.

---

## Strategy

**Intent.** Define a family of interchangeable algorithms, encapsulate each, and make them swappable at runtime.

**The single most useful pattern in LLD.** Whenever a requirement says "different", "based on", "configurable" or "types of", this is the answer.

```python
class PricingStrategy(ABC):
    @abstractmethod
    def price(self, base: int, context: dict) -> int: ...

class RegularPricing(PricingStrategy):
    def price(self, base, ctx): return base

class WeekendPricing(PricingStrategy):
    def price(self, base, ctx): return int(base * 1.5)

class Order:
    def __init__(self, strategy: PricingStrategy):
        self._strategy = strategy            # injected, swappable at runtime
    def total(self, base, ctx): return self._strategy.price(base, ctx)
```

**What it buys.** Open/Closed: a new pricing scheme is a new class, and nothing that works today is edited. It also removes conditional chains and makes each algorithm independently testable.

**Strategy vs State** — the classic confusion. They are structurally identical and differ in intent and control:
- **Strategy**: the *client* chooses the algorithm, and it typically does not change during the object's life. The strategies do not know about each other.
- **State**: the *object* changes its own state as events occur, and **states know which state comes next**.

"Weekend pricing versus weekday pricing" is Strategy. "Ticket goes issued → paid → exited" is State.

---

## Observer

**Intent.** When one object changes state, all its dependants are notified automatically.

```python
class Subject:
    def __init__(self): self._observers = []
    def subscribe(self, o): self._observers.append(o)
    def unsubscribe(self, o): self._observers.remove(o)
    def notify(self, event):
        for o in list(self._observers):      # copy: a handler may unsubscribe
            o.update(event)

class ParkingSpot(Subject):
    def release(self):
        self._vehicle = None
        self.notify({"spot": self.id, "free": True})
```

**The decoupling it achieves:** the subject knows only that observers exist, not who they are. Adding a mobile app, an analytics sink or a dashboard is adding a subscriber — the subject never changes.

**Three real pitfalls worth naming:**
1. **Lapsed listener.** An observer that is never unsubscribed is kept alive by the subject's reference — a memory leak. Use weak references, or make unsubscription explicit and reliable.
2. **Notification order is undefined**, so observers must not depend on running before or after each other.
3. **Synchronous notification** means one slow observer blocks the subject. For anything non-trivial, publish to a queue instead.

**In system design:** this pattern *is* pub/sub. Kafka, SNS and webhooks are Observer at system scale, with the queue solving the third pitfall.

**Where you meet it:** UI event listeners, React state subscriptions, `PropertyChangeListener`, and reactive streams.

---

## State

**Intent.** Let an object alter its behaviour when its internal state changes — so it appears to change class.

**The problem it solves:** the growing conditional.

```python
def handle(self):
    if self.state == "IDLE": ...
    elif self.state == "MOVING": ...
    elif self.state == "DOORS_OPEN": ...     # every new state edits this method
```

```python
class ElevatorState(ABC):
    @abstractmethod
    def step(self, car) -> None: ...

class IdleState(ElevatorState):
    def step(self, car):
        target = car.next_target()
        if target is not None:
            car.set_state(MovingState())     # the state decides the transition

class MovingState(ElevatorState):
    def step(self, car):
        car.floor += car.direction.value
        if car.should_stop_at(car.floor):
            car.set_state(DoorsOpenState())
```

**What it buys.** Adding MAINTENANCE or EMERGENCY is a new class; no existing method grows. And the transitions are readable, because each lives beside the behaviour that triggers it rather than being scattered through one long conditional.

**Where it applies:** elevators, vending machines, order lifecycles, TCP connections, media players, workflow engines, and the booking status machine in `02_LLD_OOD`.

---

## Command

**Intent.** Encapsulate a request as an object, so it can be parameterised, queued, logged and undone.

```python
class Command(ABC):
    @abstractmethod
    def execute(self) -> None: ...
    @abstractmethod
    def undo(self) -> None: ...

class AddTextCommand(Command):
    def __init__(self, doc, text):
        self._doc, self._text = doc, text
    def execute(self): self._doc.append(self._text)
    def undo(self):    self._doc.truncate(len(self._text))

class CommandHistory:
    def __init__(self): self._stack = []
    def run(self, cmd: Command):
        cmd.execute(); self._stack.append(cmd)
    def undo(self):
        if self._stack: self._stack.pop().undo()
```

**What it buys.** **Undo/redo** falls out naturally — a stack of commands. So does **queuing** (a command is a serialisable unit of work), **logging and replay** (re-execute the log to rebuild state), and **macro commands** (a command containing commands).

**In system design:** this is **event sourcing**. Store the commands rather than the resulting state, and the current state is a fold over the command log — which gives you audit, time travel and replay for free. It is also the shape of a transaction log and of a job queue.

**Where you meet it:** undo in editors, `Runnable` and task queues, database transaction logs, and CQRS write models.

---

## Chain of Responsibility

**Intent.** Pass a request along a chain of handlers until one handles it.

```python
class Handler(ABC):
    def __init__(self): self._next = None
    def set_next(self, h): self._next = h; return h
    def handle(self, request):
        if self._can_handle(request):
            return self._process(request)
        return self._next.handle(request) if self._next else None

class AuthHandler(Handler): ...
class RateLimitHandler(Handler): ...
class ValidationHandler(Handler): ...

auth = AuthHandler()
auth.set_next(RateLimitHandler()).set_next(ValidationHandler())
auth.handle(request)
```

**What it buys.** The sender does not know which handler will act, handlers are added and reordered without touching each other, and each handler has one responsibility.

**In system design:** **middleware** is exactly this — authentication, rate limiting, logging, compression and routing, each a link in the chain. Servlet filters, Express middleware and ASP.NET pipelines are all Chain of Responsibility.

**Also:** logging frameworks (a record passes through level filters to sinks), approval workflows (an expense escalates until someone has sufficient authority), and exception handling up a call stack.

**The pitfall:** a request can fall off the end unhandled. Either terminate the chain with a default handler, or make "unhandled" an explicit, detectable outcome.

---

## Template Method

**Intent.** Define the skeleton of an algorithm in a base class, deferring specific steps to subclasses.

```python
class DataImporter(ABC):
    def run(self, path):                     # the template -- do not override
        raw = self.read(path)
        clean = self.validate(raw)
        transformed = self.transform(clean)
        self.persist(transformed)
        self.notify()                        # a hook with a default

    @abstractmethod
    def read(self, path): ...
    @abstractmethod
    def transform(self, data): ...
    def validate(self, data): return data    # overridable default
    def notify(self): pass                   # optional hook

class CsvImporter(DataImporter):
    def read(self, path): ...
    def transform(self, data): ...
```

**What it buys.** The invariant ordering of the steps lives in one place and cannot be broken by a subclass, while the varying steps are pluggable.

**Template Method vs Strategy:** Template Method uses **inheritance** and fixes the structure at compile time; Strategy uses **composition** and allows runtime swapping. **Prefer Strategy** unless the algorithm's skeleton genuinely must be enforced — inheritance is the tighter coupling.

---

## Iterator

**Intent.** Access the elements of a collection sequentially without exposing its internal representation.

You use it constantly without naming it — `for x in collection` in Python, `Iterable`/`Iterator` in Java, `begin()`/`end()` in C++.

**Why it matters conceptually:** the client iterates a list, a tree or a database cursor with identical code. The collection's structure is private, and multiple independent traversals can coexist.

**The detail interviewers probe:** **modifying a collection while iterating it.** Java throws `ConcurrentModificationException`; Python skips elements silently (which is worse). Iterate over a copy, use the iterator's own `remove`, or build a new collection.

---

## Mediator

**Intent.** Encapsulate how a set of objects interact, so they do not refer to each other directly.

**The problem:** n components each knowing about the others is n² relationships, and adding one touches all of them.

```python
class ChatRoom:                              # the mediator
    def __init__(self): self._users = []
    def register(self, user): self._users.append(user); user.room = self
    def send(self, msg, sender):
        for u in self._users:
            if u is not sender: u.receive(msg)
```

Users know the room; they do not know each other.

**In system design:** a message broker is a mediator. So is an API gateway, and so is an orchestrator in a saga — the participants do not call each other, the coordinator drives them.

**The risk:** the mediator becomes a god object as it accumulates coordination logic. Watch for it.

---

## Memento

**Intent.** Capture and restore an object's state without violating its encapsulation.

The object produces an opaque snapshot; only the object itself can interpret it, so the caretaker holding snapshots learns nothing about the internals.

**Where it applies:** undo (with Command), checkpointing, transaction rollback, and game save states.

**In system design:** a database snapshot or a Redis RDB dump is this pattern. So is the periodic checkpoint in a stream processor that lets it resume without replaying from the beginning.

---

## Visitor

**Intent.** Add new operations to an object structure without modifying the classes in it.

Worth recognising rather than reaching for. It makes **adding operations** easy and **adding types** hard — the exact inverse of ordinary polymorphism, which is why it suits stable type hierarchies with growing operation sets: compilers walking an AST (type check, optimise, generate code), document processors, and static analysers.

**Its cost:** every new node type requires a new method on **every** visitor. If the type hierarchy is still changing, Visitor is the wrong choice.

---

## Choosing between them

| Requirement phrasing | Pattern |
|---|---|
| "Pricing differs by day / user type / region" | **Strategy** |
| "Notify all displays when something changes" | **Observer** |
| "The order moves through pending → paid → shipped" | **State** |
| "Support undo" / "queue the work" / "replay the log" | **Command** |
| "Requests pass through auth, then rate limit, then validation" | **Chain of Responsibility** |
| "The steps are always the same; only two of them differ" | **Template Method** |
| "Traverse without exposing the structure" | **Iterator** |
| "These components must stop knowing about each other" | **Mediator** |
| "Snapshot and restore" | **Memento** |
| "Add operations to a stable type hierarchy" | **Visitor** |

---

## Recall questions

1. What requirement wording signals Strategy?
2. State the difference between Strategy and State in terms of *who decides* the change.
3. Give three pitfalls of Observer, and say which one queues solve.
4. What conditional problem does State fix, and what does adding a state cost afterwards?
5. What four capabilities does Command give you, and which system-design pattern is it?
6. Where does Chain of Responsibility appear in every web framework?
7. What is Chain of Responsibility's failure mode?
8. Compare Template Method with Strategy on coupling and binding time; which is preferred and why?
9. What happens when you modify a collection while iterating it in Java and in Python?
10. What is Mediator's risk?
11. Why does Visitor make adding operations easy and adding types hard?
