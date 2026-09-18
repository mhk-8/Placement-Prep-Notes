# Machine Coding Round Guide

> A 90–120 minute round where you build a working system from a prose problem statement. Increasingly common at Indian product companies (Flipkart, Swiggy, Zomato, Uber, PhonePe, Razorpay, Atlassian) and it is **the round most amenable to preparation**, because the format barely varies.

---

## 1. What it is

You are given a problem statement — usually one to two pages — and asked to produce **working, runnable code** in a fixed time. Typically:

- No IDE restrictions, no internet (or limited), your own machine or a shared environment.
- Input is either hard-coded in a `main`, read from a file, or driven by a simple CLI.
- **No UI, no database, no framework** — in-memory is expected and explicitly fine.
- A reviewer then reads your code and often asks you to extend it live.

**What is graded**

| Weight | Criterion |
|---|---|
| High | **It runs and demonstrates the required flows** |
| High | **Extensibility** — can a new requirement be added without editing existing classes? |
| High | Clean class design, correct responsibilities |
| Medium | Readability: naming, small methods, no dead code |
| Medium | Edge-case handling and meaningful errors |
| Medium | Demonstration — a `main` or tests that show it working |
| Low | Algorithmic cleverness |
| **Zero** | UI, persistence, frameworks, authentication |

**The single most common failure is not finishing.** A complete, simple, working system beats an elegant half-built one every time. Scope ruthlessly.

---

## 2. Time allocation for 120 minutes

| Minutes | Activity |
|---|---|
| 0–10 | **Read the statement twice.** List the required flows. Note explicit "must support" items |
| 10–20 | Entities, enums, interfaces on paper. Decide what varies and put it behind an interface |
| 20–30 | Skeleton: package structure, empty classes, method signatures. Get it compiling |
| 30–85 | **Implement the core flow end to end**, one requirement at a time, keeping it runnable |
| 85–100 | Edge cases, validation, meaningful exceptions |
| 100–110 | A `main` (or tests) demonstrating every required flow |
| 110–120 | Read your own code; rename badly-named things; delete dead code; write a short README |

**Checkpoint at 60 minutes.** If the core flow is not working by then, **cut scope**: drop an optional feature entirely and say so in the README. A reviewer respects a stated trade-off; they do not respect a non-functional submission.

---

## 3. Structure to aim for

```
src/
  model/        entities and enums  (Vehicle, Ticket, SpotType)
  strategy/     the varying behaviours (PricingStrategy and implementations)
  service/      orchestration (ParkingLotService, BookingService)
  repository/   in-memory stores behind interfaces
  exception/    domain exceptions
  Main.java     demonstrates every required flow
README.md       assumptions, how to run, what is out of scope
```

**Packages by layer are fine and legible.** The reviewer is scanning quickly, and a conventional structure means they find things without asking.

---

## 4. The decisions that earn marks

**Put the varying behaviour behind an interface.** Read the statement for the words "different", "based on", "configurable", "types of" — each one is a Strategy waiting to happen. This is what makes the live extension question easy.

**Use enums, not strings.** `VehicleType.CAR`, never `"car"`. Typos become compile errors, and `switch` coverage is checkable.

**Repositories behind interfaces, in-memory implementations.** `Map<String, Ticket>` inside an `InMemoryTicketRepository implements TicketRepository`. Two extra minutes, and it demonstrates dependency inversion without building a database.

**Throw meaningful exceptions.** `throw new SeatUnavailableException(seatId)` rather than returning `null` or `false`. Define two or three domain exceptions.

**Validate at the boundary.** Reject bad input where it enters, with a clear message. A reviewer will try the obvious bad input.

**Write a `main` that exercises every required flow**, with printed output showing what happened. This is how the reviewer verifies it works in thirty seconds, and it is frequently the difference between a pass and a "did not demonstrate".

**Mention concurrency even if single-threaded.** One comment — `// two gates could allocate the same spot; guarded by a lock per spot` — costs nothing and reads as experience.

---

## 5. What loses marks

| Mistake | Why it costs |
|---|---|
| **Not finishing** | The top cause of failure, by a distance |
| Building a UI or wiring a database | Time spent on explicitly unmarked work |
| One 500-line god class | Fails the primary design criterion |
| A `switch` on type everywhere | The live extension then requires edits in five places |
| No `main` or tests | The reviewer cannot see it work |
| Commented-out code, `System.out.println` debris | Reads as careless |
| Over-engineering: six patterns, ten interfaces with one implementation each | Speculative generality; also consumes the clock |
| Ignoring an explicitly stated requirement | Reads as not having read the statement |
| No README | Assumptions become invisible, and every reviewer wonders about them |

**Over-engineering is a real failure mode here**, not a safe one. Interfaces with exactly one implementation and no stated axis of variation cost time and add noise.

---

## 6. The README

Three minutes, and it disproportionately affects the reviewer's impression:

```markdown
# Parking Lot System

## How to run
`javac -d out $(find src -name "*.java") && java -cp out Main`

## Assumptions
- Single parking lot instance; no reservations.
- Pricing is hourly, rounded up, minimum one hour.
- Payment always succeeds (gateway is mocked behind an interface).

## Design
- `PricingStrategy` and `SpotAllocationStrategy` are interfaces, so new
  pricing schemes or allocation policies are new classes (Open/Closed).
- `ParkingLot` is the aggregate root; gates never touch spots directly.
- Spot occupancy uses an atomic check-and-set so two gates cannot
  allocate the same spot.

## Out of scope (and how I would add it)
- Persistence: repositories are already interfaces; add a JDBC implementation.
- Multiple lots: add a registry above ParkingLot.
```

That last section is the valuable one. **Naming what you deliberately left out, and how it would plug in, converts an omission into a design decision.**

---

## 7. The live extension

After reviewing, you will usually be asked to add something on the spot:

- Parking lot → electric vehicles with charging spots; a monthly pass
- Booking → group bookings with adjacent-seat constraints; a refund policy
- Cache → change LRU to LFU
- Splitwise → a new split type
- Logging framework → a new sink or a new format

**If the design is right, this is ten minutes of writing one new class.** If it is wrong, you will be editing four files while the reviewer watches — which is precisely the information they wanted.

**Prepare for this while designing.** Every time you write an enum or a conditional on type, ask what happens when a value is added.

---

## 8. Practice problems, in a sensible order

| # | Problem | Teaches |
|---|---|---|
| 1 | **Parking Lot** | Strategy, allocation, the canonical warm-up |
| 2 | **LRU / LFU Cache** | Data structure design, policy extraction |
| 3 | **Rate Limiter** | Strategy, time-based state |
| 4 | **Splitwise** | Split strategies, a real algorithm |
| 5 | **BookMyShow** | Concurrency, holds, the hardest of the common set |
| 6 | **Elevator System** | State pattern, scheduling |
| 7 | **Vending Machine** | State pattern, clean and small |
| 8 | **Snake and Ladder / Tic-Tac-Toe** | Game loop, board abstraction, win detection |
| 9 | **Logging Framework** | Chain of Responsibility, sinks, levels |
| 10 | **Notification Service** | Strategy + Observer, channel abstraction |
| 11 | **Library Management** | CRUD-heavy modelling, borrowing rules |
| 12 | **Chess** | Polymorphic move validation — the largest of these |

**Do 1, 2, 5 and 6 properly under a timer.** They cover Strategy, State, concurrency and data-structure design between them, which is most of the surface area.

---

## 9. The checklist to run at minute 110

- [ ] It compiles and runs from a clean checkout
- [ ] `main` demonstrates **every** required flow, with visible output
- [ ] No class exceeds ~150 lines; no method exceeds ~30
- [ ] No `switch` on a type code that a new value would break
- [ ] Enums instead of string constants
- [ ] Domain exceptions instead of `null` returns
- [ ] Inputs validated at the boundary
- [ ] No commented-out code or debug printing
- [ ] Names describe intent
- [ ] README with assumptions and out-of-scope
