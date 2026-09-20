
# Operating Systems — Theory and MCQ Bank

> **Why it matters.** OS is the second-most-weighted core-CS MCQ topic. The questions cluster
> tightly around: process vs thread, scheduling algorithms (with numerical Gantt-chart questions),
> deadlock, synchronisation and virtual memory / page replacement.

---

# Part 1 — Theory

## 1.1 Process vs thread ⭐⭐⭐

| Aspect | Process | Thread |
|---|---|---|
| Definition | A program in execution | A lightweight unit of execution within a process |
| Address space | **Own, isolated** | **Shared** with sibling threads ⭐ |
| Shares | Nothing by default | Code, data, heap, open files, signals |
| Owns privately | Everything | **Stack, registers, program counter, thread ID** ⭐ |
| Creation cost | High (fork, copy page tables) | Low |
| Context switch cost | High (TLB flush, page table switch) | Low ⭐ |
| Communication | IPC — pipes, shared memory, sockets, message queues | Shared memory directly |
| Failure isolation | One process crashing does not kill others | One thread crashing can kill the whole process ⚠️ |

**Process states:**
```
                admit             dispatch            exit
   NEW ────────────────► READY ─────────────► RUNNING ──────────► TERMINATED
                          ▲                     │
                          │  I/O complete       │ I/O or event wait
                          └──────── WAITING ◄───┘
                                              (interrupt: RUNNING → READY)
```

**The PCB (Process Control Block)** stores: PID, state, program counter, CPU registers, scheduling
information, memory-management information (page tables/base-limit), accounting, and the I/O status
including open file descriptors.

⚠️ **`fork()` returns twice**: 0 in the child, the child's PID in the parent, and −1 on failure.
A classic question: `fork(); fork(); fork();` creates `2³ − 1 = 7` child processes (8 processes
total). ⭐

## 1.2 CPU scheduling ⭐⭐⭐

| Algorithm | Preemptive? | Key property | Problem |
|---|---|---|---|
| **FCFS** | No | Simple, fair in arrival order | **Convoy effect** — one long job delays everyone ⭐ |
| **SJF** | No | **Provably optimal average waiting time** ⭐ | Needs burst-time knowledge; starves long jobs |
| **SRTF** (preemptive SJF) | Yes | Optimal among preemptive | Starvation; high context-switch overhead |
| **Priority** | Either | Handles importance | Starvation → fix with **ageing** ⭐ |
| **Round Robin** | Yes | Fair, good response time | Performance depends entirely on the quantum ⭐ |
| **Multilevel Queue** | Yes | Separate queues by job class | Rigid |
| **Multilevel Feedback Queue** | Yes | Jobs move between queues; most general | Complex to tune |

**Definitions to have exact ⭐⭐:**
```
Turnaround time (TAT) = Completion time − Arrival time
Waiting time (WT)     = Turnaround time − Burst time
Response time         = First CPU allocation − Arrival time     ⚠️ not completion
Throughput            = Processes completed per unit time
```

**Round-robin quantum trade-off ⭐:**
```
q → ∞  ⇒  RR degenerates to FCFS
q → 0  ⇒  huge context-switch overhead (processor sharing in the limit)
Rule of thumb: 80% of CPU bursts should be shorter than the quantum.
```

## 1.3 Deadlock ⭐⭐⭐

**The four Coffman conditions — all must hold simultaneously:**
```
1. MUTUAL EXCLUSION  — at least one resource is non-shareable
2. HOLD AND WAIT     — a process holds one resource while waiting for another
3. NO PREEMPTION     — resources cannot be forcibly taken
4. CIRCULAR WAIT     — a cycle exists in the wait-for graph
```

**Handling strategies:**

| Strategy | How |
|---|---|
| **Prevention** | Break one of the four conditions structurally (e.g. impose a global ordering on resource acquisition to break circular wait ⭐) |
| **Avoidance** | **Banker's algorithm** — grant a request only if the resulting state is *safe* |
| **Detection and recovery** | Allow deadlock, detect cycles in the wait-for graph, then abort or roll back a victim |
| **Ignore** (the ostrich algorithm) | What Linux and Windows largely do for user processes ⭐ |

⚠️ **A cycle in a resource-allocation graph means deadlock only if there is one instance per
resource type.** With multiple instances a cycle is necessary but **not sufficient** — a standard
exam distinction. ⭐⭐

**Safe state:** a state is safe if there exists a sequence in which every process can obtain its
maximum remaining need and finish. Safe ⇒ no deadlock. Unsafe ≠ deadlock, but deadlock is possible.

## 1.4 Synchronisation ⭐⭐

**The critical-section problem requires three properties:**
```
1. MUTUAL EXCLUSION — at most one process in the critical section
2. PROGRESS         — if no one is inside, selection of who enters cannot be postponed indefinitely
3. BOUNDED WAITING  — a bound exists on how many others may enter before a waiting process does
```

| Primitive | Notes |
|---|---|
| **Mutex / binary semaphore** | Lock-unlock; ownership matters (only the locker may unlock) |
| **Counting semaphore** | `wait()/P()` decrements and blocks at 0; `signal()/V()` increments |
| **Spinlock** | Busy-waits; correct only for very short critical sections on multiprocessors ⚠️ |
| **Monitor** | A language construct bundling data + procedures + condition variables |
| **Condition variable** | `wait()`, `signal()`, `broadcast()` — used inside a monitor/mutex |

**Classic problems to be able to name:** producer-consumer (bounded buffer), readers-writers
(and its writer-starvation variant), dining philosophers (deadlock and starvation), sleeping barber.

⭐ **Dining philosophers solutions:** allow at most `n−1` philosophers to sit; require picking up
both forks atomically; or make one philosopher **left-handed** (asymmetric ordering — this breaks
circular wait, the same idea as resource ordering).

⚠️ **Race condition** = the outcome depends on the interleaving of concurrent accesses to shared
data. The classic: `count++` is not atomic — it is load, increment, store.

## 1.5 Memory management ⭐⭐⭐

| Concept | Summary |
|---|---|
| **Paging** | Fixed-size pages ↔ frames. Causes **internal** fragmentation. No external fragmentation ⭐ |
| **Segmentation** | Variable-size logical segments. Causes **external** fragmentation |
| **Virtual memory** | Execution with only part of the process resident; demand paging on a page fault |
| **TLB** | A cache of page-table entries; a TLB hit avoids one memory reference ⭐ |
| **Page fault** | Referenced page is not in memory → OS loads it from disk (a **major** fault) |
| **Thrashing** | More time paging than executing; caused by too high a degree of multiprogramming ⚠️ |
| **Working set** | The set of pages a process is actively using; keeping it resident avoids thrashing |

**Fragmentation ⭐⭐:**
```
INTERNAL : wasted space INSIDE an allocated block (paging: the last partial page)
EXTERNAL : free memory exists but is non-contiguous (segmentation, variable partitions)
           → fixed by compaction, or avoided by paging
```

**Page-replacement algorithms ⭐⭐⭐:**

| Algorithm | Rule | Notes |
|---|---|---|
| **FIFO** | Evict the oldest-loaded page | Suffers **Belady's anomaly** ⚠️ — more frames can cause *more* faults |
| **Optimal (OPT/MIN)** | Evict the page used furthest in the future | Theoretical benchmark only |
| **LRU** | Evict the least recently used | Good approximation of OPT; no Belady's anomaly (it is a **stack algorithm**) ⭐ |
| **Clock / Second-chance** | FIFO plus a reference bit | Practical LRU approximation |
| **LFU / MFU** | By use count | Rarely used alone |

⚠️ **Belady's anomaly occurs in FIFO, not in LRU or OPT.** Stack algorithms (LRU, OPT) are immune
because the set of pages with `m` frames is always a subset of the set with `m+1` frames.

**Effective access time with paging:**
```
EAT = (1 − p) × memory access time + p × page fault service time
   where p = page fault rate

With a TLB:  EAT = h(t + m) + (1 − h)(t + 2m)
   h = TLB hit ratio, t = TLB access time, m = memory access time
   (2m on a miss: one access for the page table, one for the data)
```

## 1.6 File systems and I/O (brief)
```
File allocation : contiguous (fast, external fragmentation) | linked (no random access)
                  | INDEXED (inode — the Unix approach) ⭐
Directory       : single-level, two-level, tree, acyclic graph
Disk scheduling : FCFS | SSTF (starvation) | SCAN/elevator | C-SCAN | LOOK | C-LOOK ⭐
RAID            : 0 striping (no redundancy) | 1 mirroring | 5 striping + distributed parity
                  | 6 double parity | 10 mirror + stripe
inode holds     : metadata + direct, single/double/triple indirect block pointers
                  ⚠️ NOT the file name — that lives in the directory entry ⭐
Hard link       : another directory entry pointing to the SAME inode (same filesystem only)
Soft/symlink    : a file containing a path; can cross filesystems; breaks if the target is deleted
```

---

# Part 2 — MCQ Bank (20 questions with full explanations)

---

**Q1.** Which of the following is **not** shared between threads of the same process?
```
(a) Code section    (b) Data section    (c) Stack    (d) Open file descriptors
```
<details><summary>Answer: (c) Stack</summary>

Each thread needs its **own stack** because each has its own chain of function calls, local
variables and return addresses. It also owns its registers, program counter and thread ID.

**Why the others are shared:** the code, the global/static data, the heap and the file-descriptor
table all belong to the *process* and are visible to every thread in it. That sharing is precisely
why threads are cheap and also why they need synchronisation. ⭐
</details>

---

**Q2.** Which scheduling algorithm gives the **minimum average waiting time** for a given set of
processes?
```
(a) FCFS    (b) SJF    (c) Round Robin    (d) Priority
```
<details><summary>Answer: (b) SJF</summary>

Shortest Job First is **provably optimal** for average waiting time. Intuition: moving a shorter
job ahead of a longer one reduces the shorter job's wait by more than it increases the longer job's
wait, because the wait is shared across all subsequent jobs.

**Why not the others:** FCFS suffers the convoy effect; Round Robin optimises *response* time at
the cost of average waiting time; Priority optimises importance, not waiting time.

⚠️ SJF's practical problem is that burst times are unknown in advance — they must be estimated
(commonly by exponential averaging of past bursts).
</details>

---

**Q3.** Processes arrive at time 0 with burst times 6, 8, 7 and 3 (P1, P2, P3, P4). Under
**non-preemptive SJF**, what is the average waiting time?
```
(a) 7.0    (b) 7.75    (c) 8.0    (d) 6.5
```
<details><summary>Answer: (a) 7.0</summary>

Order by burst time: **P4(3) → P1(6) → P3(7) → P2(8)**.
```
Gantt:  | P4 | P1  | P3   | P2    |
        0    3     9      16      24

Waiting times (all arrived at 0, so WT = start time):
  P4 = 0
  P1 = 3
  P3 = 9
  P2 = 16
Average = (0 + 3 + 9 + 16)/4 = 28/4 = 7.0
```
**Cross-check with FCFS** (order P1, P2, P3, P4): waits 0, 6, 14, 21 → average 10.25.
SJF is better, as the theory predicts. ⭐
</details>

---

**Q4.** Which condition is **not** required for deadlock?
```
(a) Mutual exclusion    (b) Hold and wait    (c) Preemption    (d) Circular wait
```
<details><summary>Answer: (c) Preemption</summary>

The fourth Coffman condition is **NO preemption** — resources cannot be forcibly taken away. If
preemption *were* allowed, deadlock could be broken by seizing a resource, so preemption is the
opposite of a deadlock requirement.

The four conditions are: mutual exclusion, hold and wait, **no** preemption, circular wait.
</details>

---

**Q5.** A cycle in a resource-allocation graph:
```
(a) always indicates deadlock
(b) indicates deadlock only if each resource type has exactly one instance
(c) never indicates deadlock
(d) indicates starvation
```
<details><summary>Answer: (b)</summary>

With **single-instance** resource types, a cycle is both necessary and sufficient for deadlock.
With **multiple instances**, a cycle is necessary but not sufficient — another process holding an
instance of the same type may release it and break the cycle.

⭐ This distinction is one of the most common OS MCQ traps.
</details>

---

**Q6.** Belady's anomaly can occur in which page-replacement algorithm?
```
(a) LRU    (b) Optimal    (c) FIFO    (d) All of these
```
<details><summary>Answer: (c) FIFO</summary>

Belady's anomaly — increasing the number of frames *increases* the number of page faults — occurs
in FIFO.

**Why not LRU or OPT:** both are **stack algorithms**, meaning the set of pages resident with `m`
frames is always a subset of the set resident with `m+1` frames. That subset property makes the
anomaly impossible.

Classic demonstrating string: `1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5` — FIFO gives 9 faults with 3
frames but 10 with 4 frames. ⭐
</details>

---

**Q7.** Internal fragmentation occurs in:
```
(a) paging    (b) segmentation    (c) both    (d) neither
```
<details><summary>Answer: (a) paging</summary>

Paging allocates **fixed-size** frames, so the final page of a process is usually only partly
used — that unused remainder inside an allocated frame is internal fragmentation. On average it is
half a page per process.

**Why not segmentation:** segments are sized to fit the logical unit, so there is no waste *inside*
a segment. Segmentation instead suffers **external** fragmentation — free holes scattered between
allocated segments.
</details>

---

**Q8.** What does the `fork()` system call return in the child process?
```
(a) The child's PID    (b) 0    (c) The parent's PID    (d) −1
```
<details><summary>Answer: (b) 0</summary>

```
In the child  : 0
In the parent : the child's PID (> 0)
On failure    : −1 (in the parent; no child is created)
```
This asymmetry is how a program distinguishes the two execution paths after the single call
returns twice.

⭐ Related: `n` consecutive `fork()` calls produce `2ⁿ` total processes, i.e. `2ⁿ − 1` children.
</details>

---

**Q9.** Thrashing occurs when:
```
(a) the CPU is idle
(b) processes spend more time paging than executing
(c) too few processes are in memory
(d) the disk is full
```
<details><summary>Answer: (b)</summary>

Thrashing is the pathological state in which the degree of multiprogramming is so high that no
process has enough frames for its **working set**, so every process faults almost immediately after
being scheduled. CPU utilisation collapses while disk activity saturates.

⚠️ The dangerous feedback loop: the OS observes low CPU utilisation and *admits more processes*,
which makes the problem worse. The fix is to **reduce** the degree of multiprogramming, or use a
working-set or page-fault-frequency model to control admission.
</details>

---

**Q10.** Which is the correct sequence of state transitions for a process that issues an I/O
request and later completes?
```
(a) Running -> Ready -> Waiting -> Running
(b) Running -> Waiting -> Ready -> Running
(c) Waiting -> Running -> Ready -> Running
(d) Ready -> Waiting -> Running -> Ready
```
<details><summary>Answer: (b)</summary>

A running process that issues a blocking I/O request moves to **WAITING** (it cannot use the CPU
until the device responds). When the I/O completes, an interrupt moves it to **READY** -- note that
it does **not** go straight back to RUNNING, because the CPU may be busy with another process. The
scheduler then dispatches it to **RUNNING**.

```
RUNNING --(I/O request)--> WAITING --(I/O complete)--> READY --(dispatch)--> RUNNING
```

**Why not (a):** a process does not enter READY before WAITING; the I/O request blocks it directly.
**Why not (c)/(d):** both begin from a state the process cannot be in when it issues the request.

⭐ The related transition worth knowing: `RUNNING -> READY` happens on a **timer interrupt**
(quantum expiry) or when a higher-priority process arrives -- that is preemption, and it is the
only way a process leaves RUNNING without blocking or terminating.
</details>

---

**Q11.** A binary semaphore differs from a counting semaphore in that:
```
(a) it can take only the values 0 and 1
(b) it cannot be used for mutual exclusion
(c) it is always faster
(d) it does not block
```
<details><summary>Answer: (a)</summary>

A binary semaphore's value is restricted to `{0, 1}`, making it suitable for mutual exclusion over
a single resource. A counting semaphore ranges over non-negative integers and controls access to
`n` identical instances of a resource.

**Why not (b):** mutual exclusion is precisely its main use.
**Why not (d):** both block when the value would go negative.

⚠️ A **mutex** is subtly different from a binary semaphore: a mutex has an **owner** and only the
owning thread may unlock it, whereas any thread may signal a semaphore. That ownership enables
priority inheritance.
</details>

---

**Q12.** With a page size of 4 KB and a 32-bit logical address, how many bits are used for the
page offset?
```
(a) 10    (b) 12    (c) 16    (d) 20
```
<details><summary>Answer: (b) 12</summary>

```
Page size = 4 KB = 4096 bytes = 2¹² bytes
⇒ offset needs 12 bits
⇒ page number = 32 − 12 = 20 bits
⇒ number of pages in the logical address space = 2²⁰ = 1,048,576
```
⭐ The general rule: `offset bits = log₂(page size)`. Every paging numerical starts here.
</details>

---

**Q13.** Which disk-scheduling algorithm can cause **starvation**?
```
(a) FCFS    (b) SSTF    (c) SCAN    (d) C-SCAN
```
<details><summary>Answer: (b) SSTF</summary>

Shortest Seek Time First always serves the nearest request. A stream of requests near the current
head position can indefinitely postpone a request far away — starvation.

**Why not the others:** FCFS is strictly fair by arrival order. SCAN (the elevator algorithm) and
C-SCAN sweep across the whole disk, so every request is served within one sweep, bounding the wait.
</details>

---

**Q14.** In a Unix file system, the file **name** is stored in:
```
(a) the inode    (b) the directory entry    (c) the superblock    (d) the data block
```
<details><summary>Answer: (b) the directory entry</summary>

The **inode** stores metadata — permissions, owner, size, timestamps, link count and block pointers
— but **not the name**. The name lives in the directory entry, which maps a name to an inode number.

⭐ This separation is exactly what makes **hard links** possible: two directory entries with
different names can point to the same inode, and the file persists until the link count drops to 0.
A **symbolic link**, by contrast, is a small file whose contents are a path.
</details>

---

**Q15.** Which of these is a **non-preemptive** scheduling algorithm?
```
(a) Round Robin    (b) SRTF    (c) FCFS    (d) Preemptive priority
```
<details><summary>Answer: (c) FCFS</summary>

Once a process gets the CPU under FCFS it keeps it until it terminates or blocks for I/O — no
timer interrupt takes it away. That is exactly why FCFS suffers the **convoy effect**: a single
CPU-bound job at the head of the queue delays every short job behind it.

Round Robin preempts at the quantum; SRTF preempts when a shorter job arrives; preemptive priority
preempts on a higher-priority arrival.
</details>

---

**Q16.** The Banker's algorithm is used for:
```
(a) deadlock detection    (b) deadlock avoidance    (c) deadlock prevention    (d) memory allocation
```
<details><summary>Answer: (b) deadlock avoidance</summary>

The Banker's algorithm requires each process to declare its **maximum** resource need in advance.
Before granting any request, it simulates the allocation and checks whether a **safe sequence**
still exists; if not, the request is made to wait.

**The distinctions ⭐:**
```
PREVENTION : structurally break one of the four Coffman conditions (e.g. resource ordering)
AVOIDANCE  : allow the conditions but refuse allocations that lead to an unsafe state (Banker's)
DETECTION  : allow deadlock, find cycles, recover by aborting or rolling back
```
</details>

---

**Q17.** A TLB is:
```
(a) a cache for page-table entries
(b) a type of page-replacement algorithm
(c) a region of the disk used for swapping
(d) a table of open files
```
<details><summary>Answer: (a)</summary>

The Translation Lookaside Buffer is a small, fully-associative hardware cache of recent
virtual-to-physical page mappings. On a hit, address translation costs no extra memory reference;
on a miss, the page table must be walked (one or more extra memory accesses).

⚠️ Because the TLB caches mappings for the *current* address space, a process context switch
generally requires a TLB flush (unless address-space identifiers are supported) — which is a major
reason process context switches cost more than thread switches. ⭐
</details>

---

**Q18.** Consider the reference string `7, 0, 1, 2, 0, 3, 0, 4` with **3 frames** and **LRU**
replacement. How many page faults occur?
```
(a) 5    (b) 6    (c) 7    (d) 8
```
<details><summary>Answer: (b) 6</summary>

```
Ref  Frames (LRU order: oldest → newest)      Fault?
 7   [7]                                       F (1)
 0   [7, 0]                                    F (2)
 1   [7, 0, 1]                                 F (3)
 2   evict 7 (LRU) → [0, 1, 2]                 F (4)
 0   hit → [1, 2, 0]                           -
 3   evict 1 (LRU) → [2, 0, 3]                 F (5)
 0   hit → [2, 3, 0]                           -
 4   evict 2 (LRU) → [3, 0, 4]                 F (6)

Total = 6 page faults
```
⭐ **Method:** maintain the frame list in recency order, moving a page to the end on every hit.
Evicting is then always "remove the front". Drawing this table is far more reliable than tracking
it mentally.
</details>

---

**Q19.** Which IPC mechanism allows **unrelated** processes to communicate?
```
(a) Unnamed pipe    (b) Named pipe (FIFO)    (c) Thread-local storage    (d) Registers
```
<details><summary>Answer: (b) Named pipe (FIFO)</summary>

A **named pipe** exists as an entry in the filesystem, so any process with the path and permissions
can open it — no parent-child relationship is needed.

**Why not (a):** an unnamed pipe's file descriptors must be **inherited** across `fork()`, so it
works only between related processes.
**Why not (c)/(d):** thread-local storage is private to one thread; registers are per-CPU-context.

Other IPC mechanisms: shared memory (fastest — no kernel copy after setup), message queues,
sockets (works across machines), and signals (notification only). ⭐
</details>

---

**Q20.** Given a memory access time of 100 ns, a TLB access time of 20 ns and a TLB hit ratio of
80%, what is the effective memory access time?
```
(a) 120 ns    (b) 140 ns    (c) 160 ns    (d) 100 ns
```
<details><summary>Answer: (b) 140 ns</summary>

```
TLB HIT  (80%): TLB lookup + one memory access for the data
               = 20 + 100 = 120 ns
TLB MISS (20%): TLB lookup + one memory access for the PAGE TABLE + one for the data
               = 20 + 100 + 100 = 220 ns

EAT = 0.80 × 120 + 0.20 × 220
    = 96 + 44
    = 140 ns
```
⚠️ The most common error is forgetting the **second** memory access on a miss (the page-table
walk). Always write out both branches explicitly before averaging. ⭐
</details>

---

## Scoring

| Correct | Read as |
|---|---|
| 18-20 | OS is interview-ready |
| 14-17 | Redo the scheduling and paging numericals |
| 10-13 | Re-read Part 1, especially deadlock and memory |
| < 10 | One full day here |

---

## Recall questions

1. List what threads share and what they own privately.
2. Define TAT, WT and response time precisely.
3. State the four Coffman conditions and how each can be broken.
4. When is a cycle in a resource-allocation graph insufficient for deadlock?
5. Contrast internal and external fragmentation with the mechanism causing each.
6. Which algorithms suffer Belady's anomaly and why are LRU and OPT immune?
7. Write the EAT formula with a TLB and explain the two memory accesses on a miss.
8. What does an inode contain, and what does it not?
9. Distinguish prevention, avoidance and detection of deadlock.
10. Why is a process context switch more expensive than a thread switch?
