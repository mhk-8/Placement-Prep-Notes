# Pattern Selection Guide

> The lookup you use while designing, plus the honest caveats about when **not** to use a pattern.

---

## 1. Requirement phrasing → pattern

The practical skill is hearing a requirement and recognising the shape.

| The requirement says… | Pattern | Why |
|---|---|---|
| "different X depending on Y" | **Strategy** | interchangeable algorithms |
| "configurable behaviour" | **Strategy** | chosen at runtime |
| "notify everyone when this changes" | **Observer** | one-to-many dependency |
| "X moves through these stages" | **State** | behaviour depends on state |
| "support undo" | **Command** | requests as objects on a stack |
| "queue the work for later" | **Command** | a serialisable unit of work |
| "requests pass through several checks" | **Chain of Responsibility** | a pipeline of handlers |
| "add features in any combination" | **Decorator** | composable wrappers |
| "create the right subclass from a code" | **Factory Method** | construction behind an interface |
| "these things must be used together consistently" | **Abstract Factory** | a family of products |
| "many optional parameters" | **Builder** | readable, validated, immutable construction |
| "only one of these should exist" | Singleton — but **inject** it | a single instance without global state |
| "wrap this third-party API" | **Adapter** | interface conversion |
| "simplify this subsystem for callers" | **Facade** | one convenient entry point |
| "load it only when needed" | **Proxy (virtual)** | controlled access |
| "check permissions before every call" | **Proxy (protection)** | controlled access |
| "a folder contains files and folders" | **Composite** | uniform tree treatment |
| "shapes × renderers, independently" | **Bridge** | two varying dimensions |
| "millions of objects sharing data" | **Flyweight** | intrinsic/extrinsic split |
| "the steps are fixed, two of them vary" | **Template Method** | invariant skeleton |
| "these components must stop knowing each other" | **Mediator** | centralised interaction |
| "snapshot and restore" | **Memento** | encapsulated state capture |

---

## 2. The distinctions that get asked

| Pair | The difference |
|---|---|
| **Strategy vs State** | Strategy: the *client* picks the algorithm and it rarely changes. State: the *object* transitions itself, and states know their successors. |
| **Adapter vs Decorator** | Adapter **changes the interface**, keeps behaviour. Decorator **keeps the interface**, adds behaviour. |
| **Decorator vs Proxy** | Structurally identical; **intent** differs. Decorator adds behaviour the caller wants; Proxy controls access the caller may not know about. |
| **Facade vs Adapter** | Facade simplifies **many** interfaces into one convenient one. Adapter converts **one** interface into another specific one. |
| **Factory Method vs Abstract Factory** | Factory Method creates **one** product; Abstract Factory creates a **consistent family**. |
| **Template Method vs Strategy** | Template Method uses **inheritance**, fixed at compile time. Strategy uses **composition**, swappable at runtime. Prefer Strategy. |
| **Bridge vs Adapter** | Bridge is designed **in advance** to keep two dimensions independent. Adapter is applied **after the fact** to reconcile interfaces you did not control. |
| **Composite vs Decorator** | Both wrap; Composite wraps **many** children (a tree), Decorator wraps **one** (a chain). |
| **Observer vs Mediator** | Observer: one subject, many dependants. Mediator: many peers, one coordinator. |
| **Command vs Strategy** | Command encapsulates a **request** (with undo, queuing, logging). Strategy encapsulates an **algorithm**. |

---

## 3. Patterns you have already used without naming them

| You wrote | The pattern |
|---|---|
| `PricingStrategy` in the parking lot | Strategy |
| `WeekendPricing(HourlyPricing())` | Decorator |
| `ElevatorState` subclasses | State |
| Displays subscribing to spot changes | Observer |
| `NotificationChannel` implementations | Strategy |
| Middleware: auth → rate limit → validate | Chain of Responsibility |
| An API gateway over microservices | Facade |
| A service-mesh sidecar | Proxy |
| A payment provider adapter | Adapter |
| An event-sourced ledger | Command |
| A message broker | Mediator + Observer |
| `Integer` caching for −128…127 | Flyweight |
| A connection pool | Object Pool |
| Java's `BufferedReader(new InputStreamReader(...))` | Decorator |
| A repository interface with in-memory and SQL implementations | Bridge / DIP |

**Naming these connections in an interview is worth more than reciting definitions**, because it shows the patterns are how you actually think rather than something you revised.

---

## 4. Patterns in system design (not just LLD)

| System-design concept | Underlying pattern |
|---|---|
| API gateway | Facade |
| Service mesh sidecar | Proxy + Decorator |
| Pub/sub, Kafka, webhooks | Observer |
| Middleware pipeline | Chain of Responsibility |
| Event sourcing | Command |
| Saga orchestration | Mediator + Command |
| Circuit breaker | State |
| Repository over several backends | Bridge |
| Provider adapters (payment, storage, email) | Adapter |
| Connection pooling | Object Pool |
| CDN edge | Proxy (caching) |
| Feature flags selecting behaviour | Strategy |

---

## 5. When NOT to use a pattern

This is the part that distinguishes judgement from enthusiasm, and it is worth saying in an interview.

**Do not add a pattern when:**
- **There is exactly one implementation and no stated prospect of a second.** An interface with one implementor is speculative generality — it adds indirection and a file for no benefit.
- **The requirement has not suggested that axis will vary.** You cannot be open to *every* kind of change, and trying is over-engineering.
- **The simple version is readable and the pattern version is not.** A two-branch `if` is clearer than two classes plus a factory.
- **You are in a timed machine-coding round.** Every unnecessary abstraction costs minutes you need for finishing, and finishing is the primary criterion.
- **The pattern is being used as vocabulary.** Applying Singleton, Factory, Observer and Decorator to a 200-line problem to demonstrate knowledge reads as inexperience, not expertise.

**The honest framing for an interview:**

> "I'd put pricing behind a Strategy because the requirements already mention weekday and weekend rates, so I know that axis varies. I wouldn't abstract the payment gateway yet — there's only one, and I'd extract the interface when a second appears."

That answer demonstrates you understand patterns **and** that you understand their cost, which is a stronger signal than either alone.

---

## 6. Anti-patterns worth recognising

| Anti-pattern | What it looks like | Fix |
|---|---|---|
| **God object** | one class doing everything | SRP: one reason to change |
| **Anaemic domain model** | classes of only getters and setters, all logic in services | Information Expert: behaviour with its data |
| **Singleton abuse** | globals everywhere, untestable code | inject one instance |
| **Speculative generality** | interfaces and hooks for imagined futures | YAGNI; extract when the second case arrives |
| **Pattern soup** | six patterns in a small problem | let requirements justify each one |
| **Poltergeist** | classes that exist only to call another class | delete; call directly |
| **Circular dependency** | A needs B needs A | extract an interface, or merge |
| **Distributed monolith** | microservices sharing a database | each service owns its data, or merge them back |

---

## 7. Recall questions

1. Give five requirement phrasings and the pattern each implies.
2. State the Strategy/State distinction in terms of who decides.
3. State the Adapter/Decorator and Decorator/Proxy distinctions.
4. State the Factory Method/Abstract Factory distinction.
5. Why prefer Strategy over Template Method?
6. Which pattern is an API gateway? A service-mesh sidecar? Event sourcing?
7. Give five reasons not to add a pattern.
8. What is speculative generality, and what is the rule that avoids it?
9. Name four anti-patterns and their fixes.
10. Give the sentence you would say in an interview about when you *would not* extract an interface.
