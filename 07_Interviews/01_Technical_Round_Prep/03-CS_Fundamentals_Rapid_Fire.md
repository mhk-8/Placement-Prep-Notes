
# CS Fundamentals — Rapid-Fire Round

> **What this round is.** 15-30 minutes of short questions across OS, DBMS, networks, OOP,
> C++ and — for you specifically — **compilers**. Answers should be **30-60 seconds**, not three
> minutes. The grader is checking breadth and precision, not depth.
>
> Depth lives in `../../02_Core_CS/` and `../../06_Online_Assessments/05_MCQ_Core_CS_Banks/`.
> This file is the **spoken-answer** version: the one-paragraph response plus the follow-up you
> will get.

---

## 1. How to answer a rapid-fire question ⭐⭐⭐

```
STRUCTURE (30-45 seconds):
  1. The definition, in one sentence
  2. The distinction or the "why", in one sentence
  3. ONE concrete example
  4. STOP.  ← the hardest part
```

⚠️ **Over-answering is the main failure mode here.** A three-minute answer to "what is a deadlock?"
tells the interviewer you cannot judge what a question is worth, and it burns the time they had
budgeted for six more questions. Answer, then stop; if they want more they will ask.

⭐ **If you do not know:** *"I'm not sure — I know it's related to X. Can I reason about it?"* Then
reason. An honest, reasoned attempt scores far above a confident wrong answer, because the round is
partly a calibration test.

---

## 2. Compilers ⭐⭐⭐ — your resume invites these

**You list an Andersen-style points-to analysis as your M.Tech project.** Any interviewer with a
systems background will ask compiler questions. This is the section to over-prepare.

<details><summary>"Walk me through the phases of a compiler."</summary>

```
Lexical analysis   → tokens              (regular languages, DFA)
Syntax analysis    → parse tree / AST    (context-free grammar, LL/LR)
Semantic analysis  → typed AST, symbol table (type checking, scope resolution)
IR generation      → three-address code / SSA
Optimisation       → machine-independent (constant folding, DCE, CSE, loop transforms)
Code generation    → target instructions (instruction selection, scheduling, register allocation)
Machine-dependent optimisation → peephole
```
Say it in one breath and stop. Then expect "which phase does X belong to?"
</details>

<details><summary>"What is SSA form and why is it useful?"</summary>

Static Single Assignment: every variable is assigned exactly once, and φ-functions merge values at
control-flow join points. It makes def-use chains explicit and trivial to follow, which turns
several optimisations — constant propagation, dead code elimination, global value numbering — into
simple graph traversals rather than iterative dataflow. Essentially every modern compiler (LLVM,
GCC's GIMPLE, JVM JIT) uses it. ⭐
</details>

<details><summary>"What is dataflow analysis? Give an example."</summary>

A framework for computing facts at every program point by propagating them along the control-flow
graph to a fixpoint. Defined by a lattice of facts, a transfer function per statement, and a meet
operator at joins. Examples: reaching definitions and available expressions (forward), live
variables (backward). **My points-to analysis is a monotone dataflow fixpoint** — the sets only
grow and are bounded, which is what guarantees termination. ⭐ Connect it to your project.
</details>

<details><summary>"Flow-sensitive vs context-sensitive analysis?"</summary>

**Flow-sensitive** respects statement order within a procedure (so `p = &a; p = &b;` knows `p`
points only to `b` at the end). **Context-sensitive** distinguishes different call sites of the
same function, so a helper called from two places doesn't merge their information. Both increase
precision and cost substantially. Andersen's basic formulation is neither — which is exactly the
limitation I state for my project.
</details>

<details><summary>"Andersen vs Steensgaard?"</summary>

Andersen is inclusion-based (`a = b` gives `pts(b) ⊆ pts(a)`), `O(n³)`, more precise. Steensgaard is
unification-based (`a = b` merges the two sets), near-linear via union-find, less precise because it
over-merges. The classic precision/scalability trade-off. See
`../02_Project_Deep_Dives/01-Points_To_Analysis_on_GPU.md` §4.
</details>

<details><summary>"How does a JIT differ from an AOT compiler?"</summary>

AOT compiles before execution with no runtime information. A JIT compiles at runtime and can use
**profile data** — it can inline a virtual call that is monomorphic in practice, speculate on a
type and deoptimise if the speculation fails, and specialise on actual values. The cost is
compilation latency, which is why JITs are tiered (interpret → quick compile → optimise the hot
paths). ⭐
</details>

<details><summary>"What is register allocation and why is it hard?"</summary>

Assigning an unbounded number of virtual registers to a fixed set of physical ones. It is modelled
as **graph colouring** on the interference graph (variables live at the same time interfere), which
is NP-complete, so compilers use heuristics — Chaitin-Briggs colouring or linear scan in JITs where
compile time matters. When colouring fails, a variable is **spilled** to the stack.
</details>

---

## 3. Operating systems ⭐⭐

<details><summary>"Process vs thread?"</summary>

A process has its own address space; threads within a process share code, data, heap and open file
descriptors but each has its own **stack, registers and program counter**. Threads are cheaper to
create and switch (no page-table swap or TLB flush), but one thread crashing can take down the whole
process, and shared state needs synchronisation.
</details>

<details><summary>"What is a deadlock and how do you handle it?"</summary>

Four conditions must hold simultaneously: mutual exclusion, hold and wait, no preemption, circular
wait. Handling: **prevention** (break one condition structurally — a global lock ordering breaks
circular wait), **avoidance** (Banker's algorithm), **detection and recovery** (find a cycle in the
wait-for graph, abort a victim), or **ignore it**, which is what Linux and Windows largely do for
user processes.
</details>

<details><summary>"Explain virtual memory."</summary>

Each process gets its own virtual address space, translated to physical frames through page tables,
with the TLB caching recent translations. It gives isolation, lets a process be larger than
physical memory (demand paging), and enables sharing (shared libraries mapped once). A reference to
a non-resident page triggers a page fault; the OS loads it and possibly evicts another using LRU or
an approximation like the clock algorithm.
</details>

<details><summary>"What is thrashing?"</summary>

When the degree of multiprogramming is so high that no process has enough frames for its working
set, so every process page-faults almost immediately after being scheduled. CPU utilisation
collapses while disk activity saturates. ⚠️ The dangerous feedback loop is that the OS sees low CPU
utilisation and admits *more* processes. The fix is to reduce the degree of multiprogramming.
</details>

<details><summary>"Mutex vs semaphore vs spinlock?"</summary>

A **mutex** has an owner — only the locking thread may unlock it, which enables priority
inheritance. A **semaphore** is a counter with no ownership; any thread may signal it, and a
counting semaphore controls access to `n` identical resources. A **spinlock** busy-waits instead of
sleeping, which is correct only for very short critical sections on a multiprocessor — on a
uniprocessor it wastes the whole quantum.
</details>

<details><summary>"What happens when you run ./a.out?"</summary>

Shell forks; the child calls `exec`, which replaces its address space with the new program image
(the loader maps the text and data segments and sets up the stack and heap); the dynamic linker
resolves shared-library symbols; control transfers to `_start`, which calls `main`. The parent
`wait`s for the child's exit status. ⭐ A good answer names fork, exec, the loader and the dynamic
linker.
</details>

---

## 4. DBMS ⭐⭐

<details><summary>"Explain normalisation up to BCNF."</summary>

1NF: atomic attributes. 2NF: no partial dependency on part of a composite key. 3NF: no transitive
dependency. BCNF: for every non-trivial functional dependency `X → Y`, `X` must be a super key.
Mnemonic: *the key, the whole key, and nothing but the key*.
⭐ The follow-up worth pre-empting: a BCNF decomposition is always lossless but may not preserve all
functional dependencies, whereas a 3NF decomposition can be both.
</details>

<details><summary>"What are ACID properties?"</summary>

Atomicity (all or nothing, via undo logging), Consistency (constraints hold before and after),
Isolation (concurrent transactions do not interfere, via locking or MVCC), Durability (committed
changes survive a crash, via a write-ahead log).
</details>

<details><summary>"Explain isolation levels."</summary>

```
READ UNCOMMITTED : allows dirty reads
READ COMMITTED   : prevents dirty reads; non-repeatable reads and phantoms still possible
REPEATABLE READ  : prevents non-repeatable reads; phantoms possible (InnoDB prevents them
                   with next-key locking — a well-known exception)
SERIALIZABLE     : prevents all three
```
</details>

<details><summary>"When would an index NOT be used?"</summary>

When a function is applied to the column (`WHERE YEAR(dt) = 2025`); with a leading wildcard
(`LIKE '%son'`); on implicit type conversion; when the query returns a large fraction of the table
so a full scan is cheaper; and when a composite index's **leftmost prefix** is not in the predicate.
⭐ The leftmost-prefix rule is the one that gets asked.
</details>

<details><summary>"B+ tree vs hash index?"</summary>

A hash index supports equality only, in `O(1)` average, because hashing destroys ordering. A B+
tree keeps keys sorted and links the leaves, so it supports ranges, `ORDER BY` and prefix matching —
which is why it is the default. In a B+ tree, records live only in the leaves, giving higher fan-out
and therefore fewer disk reads. ⭐
</details>

---

## 5. Computer networks ⭐

<details><summary>"TCP vs UDP?"</summary>

TCP is connection-oriented with a three-way handshake, guarantees delivery and ordering, and has
flow and congestion control, at a 20-byte minimum header. UDP is connectionless with an 8-byte
fixed header and no guarantees at all. TCP for HTTP, SSH and file transfer; UDP for DNS, DHCP, VoIP
and gaming, where loss is preferable to delay.
</details>

<details><summary>"Walk me through what happens when you type a URL."</summary>

Browser cache → OS cache → DNS resolution (recursive resolver, root, TLD, authoritative) → TCP
three-way handshake to the resolved IP → TLS handshake for HTTPS (certificate validation, key
exchange, symmetric session key) → HTTP request → server processes and responds → browser parses
HTML, fetches subresources, builds the DOM and renders. ⭐ Keep it to this level; the interviewer
will pick a step to drill into.
</details>

<details><summary>"Explain the three-way handshake and why teardown takes four steps."</summary>

SYN, SYN-ACK, ACK — both sides must synchronise sequence numbers, and the server combines its SYN
with the ACK of the client's. Teardown is FIN, ACK, FIN, ACK because each direction closes
independently; a half-close is legal, so each side sends its own FIN.
</details>

---

## 6. C++ ⭐⭐ — high probability given your resume

<details><summary>"When do you need a virtual destructor?"</summary>

Whenever an object may be deleted through a base-class pointer. Without `virtual`, only the base
destructor runs and the derived class's resources leak — it is undefined behaviour. The rule: if a
class is intended as a polymorphic base, its destructor is virtual.
</details>

<details><summary>"What is object slicing?"</summary>

Assigning a derived object to a base object **by value** copies only the base subobject; the derived
data and the polymorphic behaviour are lost. Avoid it by using references or pointers for
polymorphic types — which is also why you store `vector<unique_ptr<Base>>` rather than
`vector<Base>`.
</details>

<details><summary>"Explain RAII."</summary>

Resource Acquisition Is Initialisation: a resource's lifetime is tied to an object's lifetime, so
the destructor releases it automatically — including during stack unwinding from an exception. It
is why `unique_ptr`, `lock_guard`, `fstream` and `vector` are safe, and why raw `new`/`delete` and
manual `lock`/`unlock` are not. ⭐ The single most important idiom in C++.
</details>

<details><summary>"unique_ptr vs shared_ptr?"</summary>

`unique_ptr` is exclusive ownership with zero overhead — move-only. `shared_ptr` is shared
ownership via an atomic reference count, which costs memory and atomic operations, and which can
leak through **reference cycles** — broken with `weak_ptr`. Default to `unique_ptr`; reach for
`shared_ptr` only when ownership genuinely is shared.
</details>

<details><summary>"What is move semantics and why does it matter?"</summary>

An rvalue reference (`T&&`) lets a constructor or assignment **steal** the resources of a temporary
rather than copying them — the moved-from object is left valid but unspecified. It turns an `O(n)`
copy of a `vector` into an `O(1)` pointer transfer. `std::move` is just a cast to an rvalue
reference; it does not itself move anything. ⭐
</details>

<details><summary>"vector vs list — which is faster for insertion in the middle?"</summary>

The textbook answer is `list`, `O(1)` versus `O(n)`. The **real** answer is that `vector` usually
wins anyway for anything but very large elements, because finding the insertion point in a `list`
is a pointer chase with a cache miss at every node, while `vector`'s `memmove` is sequential and
cache-friendly. ⭐ Giving the real answer, and naming cache locality as the reason, is a strong
signal — especially for a systems role.
</details>

<details><summary>"What does the compiler do that you should know about?"</summary>

Return value optimisation and copy elision (guaranteed for prvalues since C++17), inlining,
auto-vectorisation, and reordering permitted by the as-if rule. Relevant for performance work: `-O2`
vs `-O3`, and that `volatile` is not a threading primitive — `std::atomic` is.
</details>

---

## 7. OOP ⭐

<details><summary>"Overloading vs overriding?"</summary>

Overloading: same name, **different** signature, resolved at compile time (static binding).
Overriding: same signature in a derived class, resolved at run time via the vtable (dynamic
binding). ⚠️ Static methods cannot be overridden — they are *hidden*, and the call resolves by the
reference type. Fields behave the same way.
</details>

<details><summary>"Abstract class vs interface?"</summary>

An abstract class can hold state, concrete methods and a constructor, and a class can inherit from
only one. An interface declares capability with no instance state, and a class can implement many.
The heuristic: an abstract class models *what something is*; an interface models *what something can
do*.
</details>

<details><summary>"Explain SOLID."</summary>

Single responsibility, Open/closed, Liskov substitution, Interface segregation, Dependency
inversion. If asked for one, give **Liskov** with the Rectangle/Square example — a `Square` that
inherits `Rectangle` breaks any code that sets width and height independently, which is why "is-a"
in English is not the same as "is substitutable for" in code. ⭐
</details>

---

## 8. Computer architecture ⭐⭐ — likely for hardware/HPC companies

<details><summary>"Explain the memory hierarchy."</summary>

Registers → L1 → L2 → L3 → DRAM → SSD → disk, increasing in size and latency by roughly an order of
magnitude at each step. The whole point is **locality**: temporal (reuse soon) and spatial (nearby
addresses soon). A cache miss to DRAM costs a few hundred cycles, which is why cache-friendly data
layout often matters more than algorithmic constant factors. ⭐
</details>

<details><summary>"What is a cache line, and what is false sharing?"</summary>

A cache line is the unit of transfer, typically 64 bytes. **False sharing** is when two cores write
to *different* variables that happen to sit on the same line — the line ping-pongs between the
caches even though there is no real data dependency. The fix is padding or alignment to a cache
line. ⭐ A classic parallel-programming question and directly relevant to your background.
</details>

<details><summary>"Explain pipelining and the hazards."</summary>

Overlapping instruction stages (fetch, decode, execute, memory, writeback) so one instruction
completes per cycle in steady state. Hazards: **structural** (resource conflict), **data** (RAW,
solved by forwarding or stalling), and **control** (branches, solved by prediction and speculation
with a misprediction penalty of the pipeline depth).
</details>

<details><summary>"SIMD vs SIMT?"</summary>

SIMD applies one instruction to a fixed-width vector register, with vectorisation and masking
explicit. SIMT presents a per-thread scalar programming model and the hardware groups threads into
warps, handling divergence by masking. SIMT is far easier for irregular workloads, at the cost of
the divergence penalty being implicit. ⭐ Connect it to your CUDA work.
</details>

---

## 9. The pre-interview 20-minute drill ⭐

```
 5 min : OS — process/thread, deadlock, virtual memory, thrashing
 4 min : DBMS — normal forms, ACID, isolation levels, when an index isn't used
 3 min : Networks — TCP/UDP, the URL walkthrough, handshake
 4 min : C++ — virtual destructor, RAII, smart pointers, move semantics, vector vs list
 4 min : Compilers — phases, SSA, dataflow, Andersen vs Steensgaard  ⭐ your resume invites these
```

Say each answer **out loud**, timed at 45 seconds. Reading them silently does not build the
retrieval speed this round requires.

---

## Recall questions

1. What is the four-part structure of a rapid-fire answer, and what is the hardest part?
2. Name the compiler phases in order.
3. What is SSA and what does it make easy?
4. Give the four Coffman conditions and one way to break each.
5. State the isolation-level/anomaly table.
6. When is a virtual destructor required?
7. Why does `vector` often beat `list` for middle insertion?
8. What is false sharing and how do you fix it?
