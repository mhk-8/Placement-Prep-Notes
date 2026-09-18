# LLD & OOD — Flashcards

## Questions

**Method**
1. What four things does an LLD round actually assess?
2. Give the noun/verb entity-extraction technique and the four-way filter for what becomes a class.
3. What distinguishes composition from aggregation? Give the test.
4. State the Information Expert heuristic.
5. Name the two opposite failure shapes in responsibility assignment.
6. Give the six steps of the LLD method with rough timings.
7. Why does the interviewer add a requirement at minute 35?
8. What is the one-sentence concurrency remark worth making in every round?

**SOLID**
9. State SRP in terms of reasons to change, and give the one-sentence test.
10. What is the code signature of an OCP violation?
11. What is the OCP caveat about being open to everything?
12. Why is mutable `Square extends Rectangle` an LSP violation? Write the failing caller.
13. Give the four rules a subtype must respect.
14. What code smell reveals an ISP violation?
15. Give the DIP violation and its three symptoms; which module owns the abstraction?

**Patterns in LLD**
16. Map these phrasings to patterns: "pricing differs by day"; "notify all displays"; "ticket moves through states"; "create the right subclass from a type code"; "undo the last operation".
17. Why does a `switch` on a type code usually indicate a missing polymorphism?

**Parking lot**
18. Why are `PricingStrategy` and `SpotAllocationStrategy` both interfaces?
19. What is the genuine race, and how is it resolved in one process and in a distributed system?
20. How does the design absorb electric vehicles without editing existing classes?

**Elevator**
21. Why model car behaviour with the State pattern rather than conditionals?
22. Why does the car keep separate up-stops and down-stops sets?
23. Why SCAN rather than nearest-request-first, and which OS topic is this the same as?

**Booking**
24. What is the difference between `Seat` and `ShowSeat`, and why does conflating them fail?
25. Why lock per show rather than globally?
26. Give the SQL conditional update that prevents double booking, and say what you check afterwards.
27. Why must a hold expire, and why should `is_free` honour a lapsed hold?

**Splitwise**
28. Why store money as integer minor units?
29. ₹100 split three ways — what are the three amounts, and why?
30. Explain the debt-simplification reduction and the greedy algorithm's bound.
31. Why is the exact minimum-transactions problem NP-hard?

**Cache and rate limiter**
32. Why must the LRU list be doubly linked, and what do sentinels buy?
33. What is the classic silent bug in an LRU implementation?
34. Describe the LFU structure that achieves O(1).
35. Which rate-limiting algorithm suits real API clients, and why?

**Machine coding**
36. What is graded, and what is explicitly worth zero?
37. What is the top cause of failure, and what is the checkpoint rule?
38. What three things belong in the README?

---

## Answers

1. Extracting entities from ambiguous prose; abstractions at the right level; whether the design absorbs a new requirement; clean compiling code under time pressure.
2. Underline the nouns for classes and the verbs for methods. Then filter: a thing with identity and state → class; a pure value → enum or value object; a behaviour that varies → interface; derivable from other state → not an entity.
3. Composition means the part cannot exist without the whole (destroy the whole and the part is meaningless). Aggregation means it exists independently. Test: does destroying the owner destroy the part?
4. Put behaviour with the data it needs — the class that already has the information should own the computation.
5. The **god class** (one class doing everything) and the **anaemic model** (classes of only getters and setters with all logic in a service).
6. Clarify (5); entities (5); relationships (5); responsibilities (5); patterns where they fit (5); code (20).
7. To test Open/Closed empirically — whether the new requirement is a new class or an edit to working code.
8. Name the specific race in this design and a concrete mechanism: "two gates could allocate the same spot, so I'd guard it with an atomic check-and-set."
9. One reason to change, meaning one axis of change with its own stakeholder. Test: describe the class in one sentence; if you need "and", split it.
10. A `switch` or `if/elif` chain on a type code.
11. You cannot be open to every axis of change, and trying is over-engineering. Be open along the axis the requirements suggest will vary.
12. `setWidth(5); setHeight(4); assert area() == 20` holds for Rectangle and fails for Square (16). LSP is about behaviour, not taxonomy.
13. Preconditions may not be strengthened; postconditions may not be weakened; invariants must be preserved; no new exceptions the caller is unprepared for.
14. Implementations whose methods throw `UnsupportedOperationException` or silently do nothing.
15. A class constructing its own dependency. Symptoms: untestable without the real dependency, unswappable, and hidden dependencies not visible in the signature. The **high-level module** owns the abstraction, which is the "inversion".
16. Strategy; Observer; State; Factory; Command.
17. Because each new type value forces an edit to the same method — and usually to several such methods scattered elsewhere. Polymorphism moves the decision to construction time and makes new types additive.
18. They are the two behaviours most likely to vary — pricing schemes multiply, and allocation policy may change to cheapest-first or EV-aware. Putting both behind interfaces is what makes the minute-35 extension additive.
19. Two gates allocating the same spot. One process: an atomic check-and-set under a per-spot lock. Distributed: a conditional update (`WHERE vehicle IS NULL`) with the affected row count as the verdict.
20. Two new enum values, an entry in the `fits` preference table, and a new `ChargingPricing` strategy that composes with the existing ones. No existing class is modified.
21. Because conditionals make every new state an edit to the same method and render the transitions untraceable; with State, each state is a class owning its own behaviour and next transition.
22. Because a request at floor 5 going **down** is a different request from one at floor 5 going **up** — that distinction is what makes SCAN expressible.
23. Nearest-first can starve a distant request indefinitely; SCAN bounds waiting time. It is the same trade-off as SSTF versus SCAN in disk scheduling.
24. `Seat` is physical (row A, number 12, in screen 3); `ShowSeat` is that seat **for a specific show**, carrying status and price. Conflating them makes it impossible to represent the same seat free at 6 pm and booked at 9 pm.
25. Contention is per show; a global lock would serialise bookings for unrelated films and make the popular show's contention everyone's problem.
26. `UPDATE show_seats SET status='HELD' WHERE show_id=? AND seat_id IN (...) AND (status='AVAILABLE' OR (status='HELD' AND held_until < NOW()))` — then check the **affected row count** equals the number of seats requested; if not, roll back.
27. Otherwise an abandoned checkout locks seats forever. Having `is_free` treat a lapsed hold as free means correctness does not depend on the sweeper running promptly.
28. Floating point loses precision (`0.1 + 0.2 != 0.3`), and an expense app that loses a paisa per split is broken.
29. 3333, 3333, 3334 — the splits must sum exactly to the total, so the remainder is assigned deterministically to someone.
30. Reduce the pairwise debt graph to a single **net** per person (which always sums to zero), then repeatedly settle the largest creditor against the largest debtor. Each transaction zeroes at least one person, giving at most **n − 1** transactions.
31. Finding the true minimum requires identifying subsets whose balances cancel exactly, which is a set-partition problem.
32. Unlinking an arbitrary node in O(1) requires its `prev` pointer. Sentinel head and tail nodes eliminate every null check from the link/unlink code, which is where the bugs live.
33. Unlinking the evicted node from the list but forgetting to delete its key from the map — a memory leak plus stale lookups that corrupt the order.
34. A map from key to node, a map from frequency to a doubly linked list of nodes at that frequency, and a `min_freq` counter. Access moves the node from bucket f to f+1; eviction takes the tail of bucket `min_freq`, with LRU breaking ties inside a bucket.
35. Token bucket — real clients are idle then bursty, and it permits a burst up to the bucket capacity while bounding the long-run average.
36. Graded: it runs, extensibility, clean class design, readability, edge cases, a demonstrating `main`. Worth zero: UI, persistence, frameworks, authentication.
37. Not finishing a working system. Checkpoint at the halfway mark, and if the core flow is not working, cut a feature explicitly and record it in the README.
38. How to run it; the assumptions you made; what is out of scope and how you would add it.
