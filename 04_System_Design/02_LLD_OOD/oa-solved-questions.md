# LLD & OOD — Solved Questions

> 16 questions in the style of design-round follow-ups and OOD MCQs. Every option explained.

---

**Q1.** In an LLD round, what is the interviewer most likely testing when they add a new requirement at minute 35?
(a) Whether you can type quickly
(b) **Whether the design absorbs change additively rather than by editing existing classes**
(c) Whether you know the requirement's domain
(d) Whether you can finish on time

<details><summary>Answer</summary>

**(b).**

This is the Open/Closed Principle being examined empirically. If adding an electric-vehicle type means editing a `switch` in three places, the design has failed and the interviewer has just demonstrated it rather than debating it.

**The preparation this implies:** every time you write an enum or a conditional on a type code while designing, ask what happens when a value is added.
</details>

---

**Q2.** A class has methods `calculateTotals`, `renderHtml`, `sendEmail` and `saveToDb`. Which principle is violated, and what is the symptom?
(a) LSP — subtypes are not substitutable
(b) **SRP — four unrelated reasons to change, so four teams edit one file**
(c) DIP — it depends on concretions
(d) None

<details><summary>Answer</summary>

**(b) Single Responsibility.**

"Reason to change" means an axis of change with its own stakeholder: business rules, the design team, the mail provider, the schema. Four axes in one class means constant merge conflicts, and a mail-provider change risking the totals calculation.

**The test:** describe the class in one sentence. If you need "and", split it.
</details>

---

**Q3.** Why is a mutable `Square extends Rectangle` a Liskov violation?
(a) A square is not a rectangle mathematically
(b) **`setWidth(5)` must also change the height, breaking what callers of Rectangle assume**
(c) Squares have fewer fields
(d) It is not a violation

<details><summary>Answer</summary>

**(b).**

```java
void stretch(Rectangle r) {
    r.setWidth(5);
    r.setHeight(4);
    assert r.area() == 20;   // holds for Rectangle, fails for Square (16)
}
```

Mathematically a square **is** a rectangle — which is exactly why this example is instructive. **LSP is about behaviour, not taxonomy.** A subtype must satisfy everything callers of the supertype reasonably assume.

**The fix:** siblings under an immutable `Shape` with an `area()`, so the mutator problem never arises.
</details>

---

**Q4.** You find an implementation whose methods mostly `throw new UnsupportedOperationException()`. Which principle is being violated?
(a) SRP  (b) OCP  (c) LSP  (d) **ISP — and also LSP**

<details><summary>Answer</summary>

**(d) — primarily Interface Segregation, and it causes an LSP violation too.**

The interface is too fat, forcing implementers to depend on methods they cannot support (ISP). And because callers holding the interface will crash on those subtypes, substitutability is broken (LSP).

**Stub-throwing implementations are the clearest code smell for a fat interface.** The fix is several small role-based interfaces — `Workable`, `Feedable` — each implemented only by types that genuinely support it.
</details>

---

**Q5.** In an LRU cache, why must the linked list be **doubly** linked?
(a) To iterate backwards
(b) **To unlink an arbitrary node in O(1), which needs its `prev` pointer**
(c) To store more data
(d) It does not need to be

<details><summary>Answer</summary>

**(b).**

A `get` must move the accessed node to the front, which means unlinking it from the middle. Unlinking node X requires setting `X.prev.next = X.next` — and finding `X.prev` in a singly linked list is an O(n) scan, which destroys the O(1) guarantee.

**The related detail:** sentinel head and tail nodes remove every null check from the link/unlink code, which is exactly where the bugs in this implementation live.
</details>

---

**Q6.** In an LRU cache implementation, what is the classic silent bug?
(a) Using a hash map
(b) **Unlinking the evicted node from the list but forgetting to delete it from the map**
(c) Using sentinels
(d) Making it thread-safe

<details><summary>Answer</summary>

**(b).**

The node leaves the recency list but its key remains in the map, so the map grows unboundedly (a memory leak) and a subsequent `get` on that key returns a node that is no longer in the list — corrupting the order.

**Eviction must remove from both structures.** It is worth writing `_evict` as a single method that does both, rather than inlining the two operations.
</details>

---

**Q7.** Two users try to book the same seat simultaneously in a distributed booking system. What is the correct mechanism?
(a) Read the seat status, then write if it is free
(b) **A conditional write — `UPDATE ... WHERE status='AVAILABLE'` — and check the affected row count**
(c) A longer read timeout
(d) A cache in front of the database

<details><summary>Answer</summary>

**(b).**

Read-then-write (a) is a **time-of-check-to-time-of-use race**: both users read "available" and both write. The read is advisory; the **conditional write is the decision point**, and the database's own row lock makes it atomic. If the affected row count is less than the number of seats requested, someone else won — roll back and tell the user.

A distributed lock in Redis also works, but adds a dependency and the usual lease-expiry caveat, so the underlying write must still be conditional anyway.
</details>

---

**Q8.** Why does a seat **hold** need an expiry?
(a) To save memory
(b) **Otherwise an abandoned checkout locks the seat permanently**
(c) To speed up payments
(d) It does not

<details><summary>Answer</summary>

**(b).**

A user who selects seats and closes the tab would otherwise hold them forever.

**The design refinement worth stating:** make the "is this seat free?" check treat a *lapsed* hold as free. Then correctness does not depend on a cleanup job running promptly — the sweeper merely keeps the displayed seat map tidy. Separating correctness from a background job is a genuinely good design instinct.
</details>

---

**Q9.** Splitting ₹100 equally among three people, in integer paise, must produce
(a) 3333, 3333, 3333  (b) **3333, 3333, 3334**  (c) 33.33 each as floats  (d) an error

<details><summary>Answer</summary>

**(b).**

The splits must sum **exactly** to the total, so the remainder has to be assigned to someone — deterministically, and documented.

(a) loses a paisa on every such expense. (c) is the deeper error: floating-point money accumulates drift, and `0.1 + 0.2 != 0.3`. **Money is stored in integer minor units**, and remainder handling is an explicit design decision, not an afterthought.
</details>

---

**Q10.** In an elevator system, why is SCAN preferred over always serving the nearest request?
(a) SCAN is faster on average
(b) **Nearest-first can starve a distant request indefinitely; SCAN bounds waiting time**
(c) SCAN uses less memory
(d) SCAN is simpler to implement

<details><summary>Answer</summary>

**(b).**

If requests keep arriving near the car, a request at the far end is never the nearest and is never served. SCAN sweeps to one extreme and reverses, giving every request a bounded wait.

**This is the same trade-off as SSTF versus SCAN in disk scheduling** (`02_Core_CS/01_Operating_Systems`) — and drawing that connection explicitly is a strong move in the round.
</details>

---

**Q11.** Modelling elevator behaviour with `if state == MOVING_UP ... elif state == IDLE ...` is a problem because
(a) it is slow
(b) **every new state edits the same method, and transitions become untraceable**
(c) enums cannot represent states
(d) it uses too much memory

<details><summary>Answer</summary>

**(b).**

This is the god-class failure mode. Adding MAINTENANCE or EMERGENCY means editing a method that already works, in several places.

**The State pattern** makes each state a class that owns its own behaviour and knows which state follows. Adding one is adding a class, and the transitions are readable because each lives beside the behaviour that triggers it.
</details>

---

**Q12.** Which requirement phrasing most directly implies the **Strategy** pattern?
(a) "Notify all displays when a spot frees"
(b) **"Pricing differs on weekdays, weekends and holidays"**
(c) "A ticket moves through issued, paid, exited"
(d) "Only one instance controls the lot"

<details><summary>Answer</summary>

**(b).**

One behaviour with several interchangeable implementations, selected at runtime — that is Strategy exactly.

(a) is **Observer**. (c) is **State**. (d) suggests Singleton, with the usual caveat that it is global state.

**The general trigger words for Strategy:** "different", "based on", "configurable", "types of", "policy".
</details>

---

**Q13.** In a design review, `OrderService` constructs `new MySQLOrderRepository()` in its constructor. The primary problem is
(a) MySQL is slow
(b) **the class cannot be unit-tested without a real database, and the dependency is invisible in its signature**
(c) it uses too much memory
(d) MySQL is not open source

<details><summary>Answer</summary>

**(b).**

Two symptoms, both serious: a unit test now requires a live MySQL, and the constructor signature does not reveal what the class actually needs.

**The fix is Dependency Inversion** — inject an `OrderRepository` abstraction. Production supplies `PostgresOrderRepository`, tests supply `InMemoryOrderRepository`.

**The detail that shows real understanding:** the abstraction belongs to the *domain* (what an order service needs), not to the database layer. That direction is what "inversion" means.
</details>

---

**Q14.** Which of these is the strongest signal of a **well-extensible** LLD submission?
(a) It uses six design patterns
(b) It has many small interfaces
(c) **Adding a stated likely requirement means adding a class, not editing existing ones**
(d) It has 100% test coverage

<details><summary>Answer</summary>

**(c).**

That is the operational definition of Open/Closed, and it is what the live extension question measures.

(a) and (b) are frequently **negative** signals — patterns applied for their own sake, and interfaces with exactly one implementation and no stated axis of variation, are speculative generality. Over-engineering costs time in a machine-coding round and adds noise for the reviewer.
</details>

---

**Q15.** In a machine coding round, the most common reason candidates fail is
(a) poor algorithm choice
(b) **not finishing a working system**
(c) not using enough design patterns
(d) not building a UI

<details><summary>Answer</summary>

**(b).**

A complete, simple, runnable system beats an elegant half-built one every time. The reviewer's first action is to run it.

**The practical rule:** checkpoint at the halfway mark. If the core flow is not working, cut a feature explicitly and record the cut in the README. A stated trade-off reads as judgement; a non-functional submission reads as a failure.

(d) is actively wrong — UI, persistence and authentication are explicitly unmarked, and time spent on them is time lost.
</details>

---

**Q16.** What is the one-sentence remark about concurrency worth making in almost every LLD round?
(a) "I would use threads"
(b) **"Two gates could allocate the same spot concurrently, so I'd guard allocation with an atomic check-and-set"**
(c) "Concurrency is out of scope"
(d) "I would use a database"

<details><summary>Answer</summary>

**(b).**

It identifies the **specific** race in **this** design and names a concrete mechanism. That is a different thing from a generic gesture at concurrency, and it takes ten seconds.

Most LLD problems have exactly one genuine race: allocating a parking spot, holding a seat, decrementing inventory, dispensing from a vending machine. **Find it and name it** — it is one of the cheapest differentiators available in the round.
</details>

---

## Scoring

| Score /16 | Reading |
|---|---|
| 14+ | LLD-ready; practise under a timer |
| 10–13 | Re-read `solid-in-practice.md` and the two weakest case studies |
| 6–9 | Work `lld-framework.md` then the parking lot case fully |
| < 6 | Start at `lld-framework.md` and do one case study per session |

**Diagnostic:** misses on Q1, Q12 and Q14 mean the *extensibility* idea has not landed — which is the single thing this round exists to measure.
