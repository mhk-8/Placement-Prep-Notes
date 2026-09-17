# Operating Systems — Concepts

## 1. Core idea in 3 lines
An OS is a resource manager: it multiplexes one CPU across many processes, one physical memory across many address spaces, and a few devices across everyone. Almost every OS concept is an answer to "how do we share X safely and fairly?". The concurrency material also feeds directly into system design and into any interview question about your own multithreaded code.

---

## 2. Processes and threads

**Process** = a program in execution, with its own address space, open-file table and PID. **Thread** = a unit of execution within a process, sharing the address space, heap, globals and file descriptors, but owning a separate stack, registers and program counter.

| | Process | Thread |
|---|---|---|
| Address space | private | shared |
| Creation cost | high (fork, page tables) | low |
| Context switch | expensive (TLB flush, page-table swap) | cheap |
| Communication | IPC (pipes, shared memory, sockets) | shared variables |
| Failure isolation | one crash does not kill others | one crash kills the process |

**PCB (Process Control Block)** holds: PID, process state, program counter, CPU registers, scheduling information, memory-management information (page tables, base/limit), accounting data, and I/O status.

**Context switch** = save the current PCB, load the next one. Pure overhead — no useful work happens. Cost is dominated by cache and TLB pollution rather than by the register saves themselves.

**Process states:** new → ready → running → (waiting) → ready → … → terminated. Only *running → waiting* is caused by the process itself (an I/O request or a wait); *running → ready* is caused by the scheduler (preemption or a timer interrupt).

**fork()** returns 0 in the child and the child's PID in the parent, so both continue from the same point. **exec()** replaces the process image, keeping the PID. **Zombie**: a child that has terminated but whose parent has not yet called `wait()`, so its exit status lingers in the process table. **Orphan**: a child whose parent terminated first; it is re-parented to init/systemd, which reaps it.

**User-level vs kernel-level threads:** user-level threads are fast to switch but a single blocking system call blocks the whole process; kernel-level threads are scheduled independently but cost more per switch. Modern systems use 1:1 kernel threads.

---

## 3. CPU scheduling

**Goals** (mutually conflicting): maximise CPU utilisation and throughput; minimise turnaround, waiting and response time; ensure fairness and avoid starvation.

**Definitions — memorise exactly:**
- Arrival time (AT): when the process enters the ready queue
- Burst time (BT): CPU time required
- Completion time (CT): when the process finishes
- **Turnaround time (TAT) = CT − AT**
- **Waiting time (WT) = TAT − BT**
- Response time = first time on the CPU − AT

| Algorithm | Preemptive? | Selection | Notes |
|---|---|---|---|
| FCFS | No | earliest arrival | Simple; suffers the **convoy effect** — one long job delays everyone |
| SJF | No | shortest burst | Provably optimal average waiting time; needs the burst known in advance |
| SRTF | Yes | shortest remaining | Optimal but starves long processes |
| Priority | Either | highest priority | **Starvation**, solved by **ageing** (raise priority with waiting time) |
| Round Robin | Yes | cyclic, quantum q | Best response time; q too small → switch overhead dominates, q too large → degenerates to FCFS |
| MLFQ | Yes | multiple queues with feedback | Approximates SJF without knowing burst times; new jobs start high and get demoted |

**Round-robin subtlety that appears in OAs:** when a process's quantum expires at the same instant another arrives, the *arriving* process is conventionally queued **before** the preempted one. Question-setters exploit this; state your convention.

---

## 4. Concurrency

**Race condition:** the outcome depends on the interleaving of threads. The classic is `count++`, which is really load-modify-store — three instructions that another thread can interleave with.

**Critical section problem** — any solution must satisfy:
1. **Mutual exclusion** — at most one process inside
2. **Progress** — if no one is inside, selection cannot be postponed indefinitely
3. **Bounded waiting** — a bound exists on how many others may enter first

**Peterson's solution** uses `flag[2]` and `turn` and satisfies all three for two processes, but relies on sequential consistency that modern CPUs do not provide without memory barriers.

| Primitive | What it is | Use |
|---|---|---|
| **Mutex** | binary lock with **ownership** — only the locker may unlock | protecting a critical section |
| **Binary semaphore** | counter capped at 1, no ownership — any thread may signal | signalling between threads |
| **Counting semaphore** | counter over N resources | resource pools, bounded buffers |
| **Monitor** | a language construct: mutual exclusion plus condition variables | Java `synchronized`, `wait`/`notify` |
| **Spinlock** | busy-waits instead of sleeping | very short critical sections, kernel code |

**Mutex vs semaphore** is the most-asked concurrency question. The answer: a mutex has ownership and is for mutual exclusion; a semaphore is a counter for signalling and resource counting, with no ownership, so a semaphore can be signalled by a thread that never waited on it.

**Classic problems:**
- **Producer–consumer (bounded buffer):** semaphores `empty` (init N), `full` (init 0), plus a `mutex`. The order matters — acquiring `mutex` before `empty` deadlocks.
- **Readers–writers:** multiple readers or one writer; naive solutions starve writers.
- **Dining philosophers:** five philosophers, five forks. Deadlocks if all grab left first. Fixes: allow at most four to sit, make one philosopher left-handed (asymmetry), or require both forks atomically.

---

## 5. Deadlock

**Four necessary conditions — all must hold simultaneously:**
1. **Mutual exclusion** — a resource is non-shareable
2. **Hold and wait** — a process holds one resource while requesting another
3. **No preemption** — resources cannot be forcibly taken
4. **Circular wait** — a cycle exists in the wait-for graph

**Handling strategies:**
- **Prevention** — break one of the four. Most practical: impose a total ordering on resources, which breaks circular wait.
- **Avoidance** — the **Banker's algorithm**: grant a request only if the resulting state is *safe*, meaning a sequence exists in which every process can finish. Needs maximum demands declared in advance, which is rarely realistic.
- **Detection and recovery** — allow deadlock, detect cycles in the wait-for graph, then kill or roll back a process.
- **Ostrich algorithm** — ignore it. What Linux and Windows actually do for user processes.

**Deadlock vs starvation vs livelock:** deadlock is a cyclic wait where nothing progresses; starvation is one process indefinitely postponed while others proceed; livelock is processes actively changing state in response to each other without making progress.

---

## 6. Memory management

**Logical (virtual) address** is generated by the CPU; **physical address** is what the memory unit sees. The **MMU** translates between them at runtime.

**Contiguous allocation** fits processes into holes: first fit (fastest), best fit (smallest adequate hole — leaves tiny unusable fragments), worst fit (largest hole). **External fragmentation** is free memory that exists but is not contiguous; **internal fragmentation** is space wasted inside an allocated block.

**Paging** divides physical memory into fixed **frames** and logical memory into equal-sized **pages**. A logical address splits into `(page number, offset)`; the page table maps page → frame. Paging eliminates external fragmentation entirely, at the cost of internal fragmentation in the last page (on average half a page per process).

For a page size of 2ⁿ bytes, the low **n bits** of the address are the offset and the rest is the page number. This is the fact behind most paging numericals.

**TLB** is a small associative cache of recent page-table entries. With hit ratio h, one memory access costs
`EAT = h(TLB + m) + (1 − h)(TLB + 2m)`
where m is one memory access — two accesses on a miss because you read the page table and then the data.

**Multi-level paging** shrinks the page table itself: a 32-bit address space with 4 KB pages needs 2²⁰ entries (4 MB per process) in a flat table, so two-level paging pages the page table. **Inverted page tables** keep one entry per *frame* instead.

**Segmentation** divides memory by logical unit (code, stack, heap) with variable sizes, giving external fragmentation but a user-meaningful view. Segmented paging combines both.

**Demand paging** loads a page only when referenced; a reference to a non-resident page raises a **page fault**, and the OS fetches it from disk. **Effective access time = (1 − p)·m + p·(page fault service time)**, and because a fault costs milliseconds against nanoseconds for memory, even p = 0.001 degrades performance by orders of magnitude.

---

## 7. Page replacement

| Algorithm | Rule | Notes |
|---|---|---|
| **FIFO** | evict the oldest loaded page | Simple; suffers **Belady's anomaly** |
| **Optimal (OPT)** | evict the page used furthest in the future | Unimplementable; the benchmark |
| **LRU** | evict the least recently used | Good approximation of OPT; needs a stack or counters |
| **LFU** | evict the least frequently used | An early-hot page can never be evicted |
| **Clock / second chance** | FIFO with a reference bit | The practical approximation of LRU |

**Belady's anomaly:** more frames can cause *more* page faults. It occurs in FIFO and not in LRU or OPT, because LRU and OPT are **stack algorithms** — the set of pages resident with n frames is always a subset of the set resident with n+1 frames. This exact fact is a perennial MCQ.

**Thrashing:** the system spends more time paging than executing, because the sum of working sets exceeds physical memory. Symptoms: CPU utilisation collapses while disk activity saturates. Fixes: the **working-set model** (keep each process's recently-referenced set resident) or **page-fault frequency** control; ultimately, reduce the degree of multiprogramming or add memory.

---

## 8. File systems and I/O

**Inode** holds a file's metadata — permissions, owner, timestamps, size, and pointers to data blocks — but **not the filename**, which lives in the directory entry. That separation is what makes hard links possible.

**Allocation methods:** contiguous (fast sequential access, external fragmentation), linked (no fragmentation, no random access), **indexed** (an index block of pointers — what inodes use, with single, double and triple indirect blocks for large files).

**Journaling** writes intended changes to a log before applying them, so a crash can be recovered by replaying or discarding the journal.

**IPC mechanisms:** pipes (unidirectional, related processes), named pipes/FIFOs, message queues, **shared memory** (fastest — no kernel copy, but needs explicit synchronisation), and sockets (works across machines).

---

## 9. Recall questions

1. What exactly do threads share, and what do they not?
2. Give the formulas for turnaround and waiting time.
3. Why is SJF optimal, and why can it not be implemented directly?
4. What are the trade-offs in choosing the RR time quantum?
5. State the three requirements of a critical-section solution.
6. Mutex vs semaphore — give the distinction in one sentence.
7. Why does acquiring the mutex before the `empty` semaphore deadlock the producer–consumer?
8. State the four deadlock conditions and which one resource ordering breaks.
9. Give the TLB effective-access-time formula and explain the two memory accesses.
10. What is Belady's anomaly, which algorithms suffer it, and why not LRU?
