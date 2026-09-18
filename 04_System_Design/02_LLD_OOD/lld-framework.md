# The Low-Level Design Method

> For a fresher or 0–2 YOE candidate, **LLD is the more likely design round than HLD**. It is also more tractable: there is a repeatable procedure, and the output is code you can actually write.
> A machine-coding round is the same skill with a compiler attached.

---

## 1. What is actually being assessed

Not whether you know twenty design patterns. The round tests four things:

1. **Can you extract entities and responsibilities from an ambiguous prose description?**
2. **Are your abstractions at the right level** — neither a single god class nor fifteen anaemic ones?
3. **Does the design absorb a new requirement gracefully?** Interviewers almost always add one at minute 35, and that is the real exam.
4. **Can you write clean, compiling code under time pressure?**

A design that is slightly over-simple but clean and extensible beats an elaborate one full of patterns applied for their own sake.

---

## 2. The six steps

### Step 1 — Clarify requirements (5 minutes)

Ask about **scope**, **scale** and **the interface**. For a parking lot:

- What vehicle types? Do they have different spot sizes and rates?
- One floor or several? One entrance or several?
- How is payment handled — cash, card, both? Is pricing hourly, or slab-based?
- Is this a single process, or do multiple gates run concurrently?
- Is it a library, a CLI, or a service with an API?

**Write the confirmed requirements down and say them back.** Everything after this is derived from that list, and an unconfirmed assumption is the most common way an LLD round goes wrong.

Then explicitly list **out of scope**: "I'll assume a single parking lot instance, no reservations, and no vehicle-registration lookup, unless you'd like those."

### Step 2 — Identify entities (5 minutes)

**Underline the nouns** in the requirements. That is genuinely the technique.

> "A **vehicle** enters through an **entry gate**, receives a **ticket**, parks in a **spot** on a **floor**, and on exit pays at an **exit gate** according to a **pricing strategy**."

Candidates: Vehicle, EntryGate, Ticket, ParkingSpot, Floor, ExitGate, PricingStrategy, Payment, ParkingLot.

Then filter:
- **Is it a real thing with identity and state?** → a class.
- **Is it just a value?** → an enum or a value object (VehicleType, Money).
- **Is it a behaviour that varies?** → an interface with implementations (PricingStrategy).
- **Is it derivable from other state?** → not an entity (available spot count).

**Nouns become classes; verbs become methods.** It is crude and it works.

### Step 3 — Define relationships (5 minutes)

For every pair that interacts, decide:

| Relationship | Meaning | Test |
|---|---|---|
| **Inheritance (is-a)** | subtype substitutable for supertype | can it be used *anywhere* the parent can, without surprising the caller? |
| **Composition (has-a, owns)** | the part cannot exist without the whole | does destroying the whole destroy the part? Floor owns its Spots |
| **Aggregation (has-a, shared)** | the part exists independently | ParkingLot has Vehicles, but a Vehicle exists without the lot |
| **Association (uses)** | one calls the other | Gate uses TicketService |

**Prefer composition.** Inheritance is the tightest coupling a language offers, and it is correct only when substitutability genuinely holds (see `solid-in-practice.md`).

Also settle **cardinality**: one floor has many spots; one ticket belongs to one vehicle; one vehicle may have many tickets over time.

### Step 4 — Assign responsibilities (5 minutes)

The core question: **who should own this behaviour?**

The heuristic is **Information Expert** — put the behaviour with the data it needs. If computing a parking fee needs the entry time, the exit time and the spot type, the fee calculation belongs where those are reachable, not in a utility class that has to be handed all three.

Watch for the two failure shapes:
- **God class.** `ParkingLot` doing parking, pricing, payment, display and persistence. Split it.
- **Anaemic model.** Classes that are only getters and setters, with all logic in a `Service`. This is not object-oriented design, it is procedural code with extra ceremony.

Ask of each class: **"what is its one reason to change?"** If you need "and" to answer, split it.

### Step 5 — Apply patterns where they fit (5 minutes)

Patterns should **emerge from the requirements**, not be imposed.

| Requirement phrasing | Pattern |
|---|---|
| "different pricing for weekday/weekend/holiday" | **Strategy** |
| "notify all displays when a spot frees up" | **Observer** |
| "create the right Vehicle subclass from a type code" | **Factory** |
| "one instance controls the whole lot" | Singleton (with caution) |
| "a ticket moves through issued → paid → exited" | **State** |
| "add tax, then discount, then loyalty points" | **Decorator** or a chain |
| "undo the last operation" | **Command** |
| "many objects share immutable data" | Flyweight |

**Name the pattern out loud when you use it**, with the reason: "I'm using Strategy here so a new pricing scheme is a new class rather than an edit to the existing one — that's Open/Closed." Naming without justification reads as pattern-dropping.

### Step 6 — Write the code (20 minutes)

Write **interfaces first**, then the core classes, then a small `main` that demonstrates the flow.

- Compiling, runnable code beats more classes.
- Skip persistence unless asked — an in-memory `Map` is fine, and say so.
- Handle the obvious edge cases: lot full, invalid ticket, double exit.
- Keep methods short and named for intent.

**Talk while you type.** Silence is the most common way to lose a round you are otherwise passing.

---

## 3. The minute-35 question

The interviewer will add a requirement. Common ones:

- "Now support electric vehicles that need charging spots."
- "Now the lot has several entrances and exits."
- "Now pricing differs on weekends."
- "Now we need a monthly pass."

**A good design absorbs these as new classes, not as edits to existing ones.** If adding a vehicle type means editing a `switch` in three places, the design has failed Open/Closed and the interviewer has just demonstrated it.

**Anticipate this while designing.** When you introduce an enum, ask yourself what happens when a value is added. When you write a conditional on type, ask whether polymorphism would remove it.

---

## 4. Class diagram notation

You will draw one, so use the notation correctly:

```
  ┌─────────────────────┐
  │   <<interface>>     │        ◁──── inheritance / implements (hollow triangle)
  │  PricingStrategy    │        ◆──── composition (filled diamond, owner side)
  ├─────────────────────┤        ◇──── aggregation (hollow diamond)
  │ + calculate(t): Money│       ────► association (arrow, direction of use)
  └─────────────────────┘
            △
            │ implements
  ┌─────────┴───────────┐
  │  HourlyPricing      │        + public   - private   # protected
  └─────────────────────┘        underlined = static
```

```
  ParkingLot ◆──── Floor ◆──── ParkingSpot        (owns: destroy the lot, spots go)
  ParkingLot ◇──── Vehicle                        (shares: vehicles outlive the visit)
  EntryGate ────► TicketService                   (uses)
```

---

## 5. Code quality signals

What interviewers actually notice:

- **Naming.** `calculateFee` not `calc`; `isAvailable` not `flag`.
- **No magic numbers.** `MAX_FLOORS`, not a bare 5.
- **Enums over string constants.** `VehicleType.CAR`, not `"car"`.
- **Encapsulation.** Fields private, invariants enforced in methods, no setter that can break state.
- **Immutability where possible.** A `Ticket`'s entry time should not be mutable.
- **Small methods.** If it does not fit on a screen, split it.
- **Explicit errors.** Throw a meaningful exception rather than returning `null`.
- **Thread safety mentioned**, even if not implemented: "two gates could allocate the same spot concurrently — I'd guard allocation with a lock, or use an atomic compare-and-set on spot status."

That last one is a strong differentiator and takes one sentence.

---

## 6. Time management for a 45-minute round

| Minutes | Activity |
|---|---|
| 0–5 | Clarify requirements; confirm and scope out |
| 5–10 | Entities, enums, relationships — sketch the class diagram |
| 10–15 | Interfaces and key method signatures |
| 15–35 | Implementation of the core flow |
| 35–40 | The new requirement; extend the design |
| 40–45 | Edge cases, concurrency, what you would add next |

**If you are running out of time, say so and prioritise:** "I'll implement the core flow fully and describe the payment classes rather than writing them out." Managing the clock explicitly is itself a signal.

---

## 7. Common mistakes

| Mistake | Fix |
|---|---|
| Designing before clarifying | Five minutes of questions, always |
| One god class | One reason to change per class |
| Anaemic classes with all logic in a service | Information Expert — behaviour with its data |
| Inheritance for code reuse | Composition unless "is-a" genuinely holds |
| A `switch` on type | Polymorphism |
| Patterns for their own sake | Let the requirement justify the pattern |
| Building persistence, auth, logging | In-memory, and say why |
| Silence while coding | Narrate continuously |
| Ignoring the new requirement's implications | Show *where* it plugs in |

---

## 8. Recall questions

1. What four things does an LLD round actually assess?
2. Give the noun/verb technique for entity extraction, and the four-way filter for what becomes a class.
3. Give the test that distinguishes composition from aggregation.
4. State the Information Expert heuristic.
5. What are the two opposite failure shapes for responsibility assignment?
6. Give five requirement phrasings and the pattern each implies.
7. Why does the interviewer add a requirement at minute 35, and what are they measuring?
8. Draw the UML symbols for inheritance, composition, aggregation and association.
9. List six code-quality signals interviewers notice.
10. What is the one-sentence concurrency remark worth making in every LLD round?
