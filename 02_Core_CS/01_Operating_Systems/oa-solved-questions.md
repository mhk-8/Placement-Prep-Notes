# Operating Systems — Solved OA Questions

> 25 questions in the exact style of MNC online assessments, with the reasoning for **every** option — because in an MCQ the distractors are the lesson.
> **How to use this:** cover the answers, give yourself **45 seconds** per conceptual question and **2 minutes** per numerical. Mark anything you guessed, even if you got it right, and log it.

---

## Set A — Processes and threads

**Q1.** Which of the following is **not** shared between threads of the same process?
(a) Heap  (b) Global variables  (c) Stack  (d) Open file descriptors

<details><summary>Answer</summary>

**(c) Stack.**

Each thread needs its own call stack because each has an independent chain of function calls, local variables and return addresses. The heap, globals/statics, code segment and the open-file table all live in the shared address space.
(a) and (b) are in the shared address space. (d) the file-descriptor table belongs to the process, not the thread — which is why one thread closing an fd affects all of them.

**Trap:** the register set and program counter are also per-thread, and are sometimes the intended answer. If both "stack" and "registers" appear, the question is usually asking for the memory region, i.e. the stack.
</details>

---

**Q2.** A process executes the following code. How many **new** processes are created?
```c
fork();
fork();
fork();
```
(a) 3  (b) 7  (c) 8  (d) 4

<details><summary>Answer</summary>

**(b) 7.**

After n calls to `fork()` there are 2ⁿ total processes. Here 2³ = 8 processes **exist**, of which 1 is the original, so **7 are new**.

(a) 3 counts the calls, not the processes. (c) 8 is the total number of processes, not the number *created* — this is the classic misread. (d) 4 would be the answer for two forks.

**Rule:** "how many processes exist" → 2ⁿ. "How many are created/child processes" → 2ⁿ − 1. Read which one is being asked.
</details>

---

**Q3.** A zombie process is one that
(a) has terminated but whose parent has not yet read its exit status
(b) is waiting indefinitely for a resource
(c) has lost its parent and been adopted by init
(d) is consuming CPU without doing useful work

<details><summary>Answer</summary>

**(a).**

The child has exited, but its entry stays in the process table until the parent calls `wait()` to collect the exit status. Until then it is a zombie — it holds no memory or CPU, only a PID and a table entry.

(b) is a blocked or deadlocked process. (c) is an **orphan**, which init reaps immediately, so orphans do *not* become long-lived zombies. (d) is a spinning or livelocked process.
</details>

---

**Q4.** Which transition in the process state diagram is **impossible**?
(a) Running → Ready  (b) Running → Waiting  (c) Waiting → Running  (d) Waiting → Ready

<details><summary>Answer</summary>

**(c) Waiting → Running.**

When the event a process was waiting for completes, it becomes *eligible* to run but must be selected by the scheduler, so it moves to **Ready** first.

(a) preemption or quantum expiry. (b) an I/O request or `wait()`. (d) I/O completion.

**The underlying idea:** only the scheduler can put a process on the CPU, and it selects from the ready queue.
</details>

---

## Set B — Scheduling

**Q5.** Processes P1(AT=0, BT=5), P2(AT=1, BT=3), P3(AT=2, BT=1) under **SRTF**. What is the average waiting time?
(a) 1.67  (b) 2.33  (c) 3.00  (d) 4.00

<details><summary>Answer</summary>

**(a) 1.67.**

- t=0: only P1 → run P1
- t=1: P2 remaining 3 < P1 remaining 4 → preempt, run P2
- t=2: P3 remaining 1 < P2 remaining 2 → preempt, run P3
- t=3: P3 done. Ready: P1(4), P2(2) → run P2 to t=5
- t=5: run P1 to t=9

```
 0  1  2  3     5           9
 |P1|P2|P3|--P2-|-----P1----|
```

| P | AT | BT | CT | TAT = CT-AT | WT = TAT-BT |
|---|---|---|---|---|---|
| P1 | 0 | 5 | 9 | 9 | 4 |
| P2 | 1 | 3 | 5 | 4 | 1 |
| P3 | 2 | 1 | 3 | 1 | 0 |

Average WT = (4 + 1 + 0)/3 = **1.67**. (Average TAT = 14/3 = 4.67.)

**Where the distractors come from:** 2.33 is what you get computing WT as `CT - BT` instead of `TAT - BT`. 4.00 is P1's waiting time mistaken for the average. Writing both formulas above the table prevents both errors.
</details>

---

**Q6.** Which statement about Round Robin is **false**?
(a) As the quantum tends to infinity, RR behaves like FCFS
(b) As the quantum tends to zero, RR approximates processor sharing but overhead dominates
(c) RR guarantees the minimum average waiting time
(d) RR has better response time than FCFS

<details><summary>Answer</summary>

**(c) is false.**

**SJF** provably minimises average waiting time, not RR. RR optimises *response* time and fairness, generally at the cost of a worse average turnaround time.

(a) true — with a quantum longer than every burst, no preemption ever occurs. (b) true — infinitesimal quanta approach ideal fair sharing, but context-switch overhead consumes the CPU. (d) true — that is RR's purpose.
</details>

---

**Q7.** Which scheduling algorithm can cause **starvation**?
(a) FCFS  (b) Round Robin  (c) SRTF  (d) Both (b) and (c)

<details><summary>Answer</summary>

**(c) SRTF.**

A long job is preempted every time a shorter one arrives, so under a steady stream of short arrivals it may never complete. Priority scheduling has the same problem, and the standard cure for both is **ageing** — raising a process's priority the longer it waits.

(a) FCFS is starvation-free: every process eventually reaches the head of the queue. (b) RR is starvation-free: every process receives a quantum each cycle. (d) is wrong precisely because RR does not starve.
</details>

---

**Q8.** Non-preemptive SJF with P1(AT=0, BT=8), P2(AT=1, BT=4), P3(AT=2, BT=2). Average turnaround time?
(a) 8.67  (b) 9.67  (c) 10.33  (d) 11.00

<details><summary>Answer</summary>

**(b) 9.67.**

At t=0 only P1 has arrived, and non-preemptive SJF **cannot interrupt a running job** — so P1 runs 0–8 even though two shorter jobs arrive meanwhile. That is the whole point of the question.
At t=8 the ready set is {P2(4), P3(2)} → P3 runs 8–10, then P2 runs 10–14.

```
 0              8      10          14
 |------P1------|--P3--|----P2-----|
```

| P | AT | BT | CT | TAT |
|---|---|---|---|---|
| P1 | 0 | 8 | 8 | 8 |
| P2 | 1 | 4 | 14 | 13 |
| P3 | 2 | 2 | 10 | 8 |

Average TAT = (8 + 13 + 8)/3 = 29/3 = **9.67**

**Distractor (a) 8.67** is the answer under *preemptive* SJF (SRTF), where P1 is interrupted at t=1. If a question does not say which variant, the default reading of "SJF" is non-preemptive — but say so aloud in an interview.
</details>

---

## Set C — Concurrency and deadlock

**Q9.** The difference between a mutex and a binary semaphore is that
(a) a mutex can be signalled by any thread, a semaphore only by its owner
(b) a mutex has ownership; only the locking thread may unlock it
(c) they are identical in every respect
(d) a semaphore cannot be used for mutual exclusion

<details><summary>Answer</summary>

**(b).**

A mutex is owned by the thread that locks it, and only that thread may unlock it — which is what allows priority inheritance and deadlock detection. A binary semaphore is just a counter capped at 1, so any thread may signal it, making it suitable for *signalling* between threads.

(a) is the statement reversed. (c) is false — the ownership semantics differ. (d) is false: a binary semaphore *can* provide mutual exclusion, it simply provides fewer guarantees while doing so.
</details>

---

**Q10.** In the producer–consumer solution, the producer executes `wait(mutex)` before `wait(empty)`. What happens?
(a) Nothing — the order is irrelevant
(b) The buffer may overflow
(c) Deadlock when the buffer is full
(d) The consumer starves but the system keeps running

<details><summary>Answer</summary>

**(c) Deadlock when the buffer is full.**

The producer acquires `mutex`, then blocks on `wait(empty)` because there is no free slot. It is now asleep **holding the mutex**. The consumer cannot enter its critical section to remove an item, so no slot is ever freed. Classic hold-and-wait.

(a) is exactly the misconception being tested. (b) cannot happen — `empty` still bounds the buffer. (d) understates it: the whole system halts, not just the consumer.

**Rule:** always acquire the counting semaphore **before** the mutex, and release in the reverse order.
</details>

---

**Q11.** Which is **not** a necessary condition for deadlock?
(a) Mutual exclusion  (b) Hold and wait  (c) Preemption  (d) Circular wait

<details><summary>Answer</summary>

**(c) Preemption.**

The condition is **no preemption**. If resources *can* be forcibly taken back, deadlock cannot persist.

This question is almost always asked with the word inverted — read the option carefully. The four conditions are: mutual exclusion, hold and wait, **no** preemption, circular wait.
</details>

---

**Q12.** A system has 3 processes and 4 identical resources. Each process needs a maximum of 2 resources. Can deadlock occur?
(a) Yes  (b) No  (c) Only if all request simultaneously  (d) Insufficient information

<details><summary>Answer</summary>

**(b) No.**

Worst case: every process holds 1 resource, using 3 of the 4. One resource remains free, so some process can obtain its second resource, finish and release both. The chain then unwinds.

**The general rule:** with n processes each needing a maximum of m resources, deadlock is impossible when the total resources R ≥ n(m − 1) + 1. Here 3(2−1) + 1 = 4 ≤ 4 ✔.

Memorise that inequality — it generates a whole family of OA questions.
</details>

---

**Q13.** In the Banker's algorithm, a **safe state** means
(a) no deadlock is currently present
(b) there exists an ordering in which all processes can complete
(c) all processes have their maximum resources allocated
(d) no process is waiting

<details><summary>Answer</summary>

**(b).**

Safety means a *safe sequence* exists — an order in which each process's remaining need can be met from the currently available resources plus what its predecessors release.

(a) is weaker: an **unsafe** state is not necessarily deadlocked, it merely *may* lead to deadlock. That distinction is the point of the question. (c) and (d) are unrelated.
</details>

---

## Set D — Memory management

**Q14.** A system uses 32-bit logical addresses with 4 KB pages and 4-byte page-table entries. The size of a single-level page table per process is
(a) 1 MB  (b) 2 MB  (c) 4 MB  (d) 8 MB

<details><summary>Answer</summary>

**(c) 4 MB.**

4 KB = 2¹² → 12 offset bits → page number is 32 − 12 = 20 bits → 2²⁰ entries → 2²⁰ × 4 B = **4 MB**.

(a) is 2²⁰ × 1 B. (b) is 2²⁰ × 2 B. (d) uses 8-byte entries.

**Method:** offset bits from page size, page-number bits from the remainder, entries = 2^(page-number bits), size = entries × PTE size. Four steps, every time.
</details>

---

**Q15.** TLB access time is 20 ns, memory access is 100 ns, and the TLB hit ratio is 80%. What is the effective memory access time?
(a) 120 ns  (b) 140 ns  (c) 160 ns  (d) 180 ns

<details><summary>Answer</summary>

**(b) 140 ns.**

```
Hit  (80%): TLB + memory        = 20 + 100 = 120
Miss (20%): TLB + page table + data = 20 + 100 + 100 = 220
EAT = 0.8(120) + 0.2(220) = 96 + 44 = 140 ns
```

(a) is the hit cost alone. (c) and (d) come from double-counting the TLB lookup or from adding an extra memory access.

**The invariant:** a TLB miss costs **two** memory accesses — one for the page table, one for the data.
</details>

---

**Q16.** Belady's anomaly can occur in
(a) LRU  (b) FIFO  (c) Optimal  (d) All of the above

<details><summary>Answer</summary>

**(b) FIFO.**

LRU and OPT are **stack algorithms**: the set of pages resident with n frames is always a subset of the set resident with n+1 frames, so adding a frame can never introduce a new fault. FIFO has no such property, so more frames can produce more faults.

Concrete witness: the string `1 2 3 4 1 2 5 1 2 3 4 5` gives 9 faults with 3 frames and 10 with 4 (worked in `numericals.md` N6).
</details>

---

**Q17.** Which statement about internal and external fragmentation is correct?
(a) Paging suffers external fragmentation; segmentation suffers internal
(b) Paging suffers internal fragmentation; segmentation suffers external
(c) Both suffer both kinds
(d) Neither suffers fragmentation

<details><summary>Answer</summary>

**(b).**

**Paging** uses fixed-size frames, so free memory is always usable — no external fragmentation — but the last page of a process is partially empty, giving internal fragmentation of on average half a page per process.

**Segmentation** uses variable-size segments allocated contiguously, so free memory becomes a patchwork of unusable holes — external fragmentation — with no internal waste since a segment is exactly its needed size.

(a) is the statement swapped, and is the intended trap.
</details>

---

**Q18.** Thrashing is best characterised as
(a) high CPU utilisation with low disk activity
(b) low CPU utilisation with high paging activity
(c) a process holding a resource it does not need
(d) frequent context switches between two processes

<details><summary>Answer</summary>

**(b).**

The sum of the processes' working sets exceeds physical memory, so every process spends its time waiting for page faults. The CPU idles while the disk saturates.

The cruel part, and why the working-set model exists: the OS *observes* low CPU utilisation and responds by increasing the degree of multiprogramming, which makes thrashing worse.
</details>

---

## Set E — File systems and I/O

**Q19.** An inode stores all of the following **except**
(a) file size  (b) file permissions  (c) the filename  (d) pointers to data blocks

<details><summary>Answer</summary>

**(c) the filename.**

The filename lives in the **directory entry**, which maps a name to an inode number. That separation is exactly what makes hard links possible — several directory entries can name the same inode — and it is why the inode carries a link count.
</details>

---

**Q20.** Requests `98, 183, 37, 122, 14, 124, 65, 67` with the head at 53. Total head movement under **SSTF**?
(a) 208  (b) 236  (c) 331  (d) 640

<details><summary>Answer</summary>

**(b) 236.**

53 → 65 → 67 → 37 → 14 → 98 → 122 → 124 → 183
`12 + 2 + 30 + 23 + 84 + 24 + 2 + 59 = 236`

(c) 331 is SCAN. (d) 640 is FCFS. (a) is a distractor.

Recognise the three answers at a glance for this canonical request set: **FCFS 640, SSTF 236, SCAN 331, C-SCAN 382**.
</details>

---

## Set F — Mixed rapid fire

**Q21.** Which of these is **not** a valid IPC mechanism?
(a) Shared memory  (b) Message queues  (c) Semaphores  (d) Page tables

<details><summary>Answer</summary>
**(d) Page tables** — an address-translation structure, not a communication channel. Semaphores do count as IPC (they convey synchronisation information between processes).
</details>

**Q22.** The `exec()` family of system calls
(a) creates a new process  (b) replaces the current process image  (c) terminates the process  (d) duplicates the address space

<details><summary>Answer</summary>
**(b).** The PID is preserved; the code, data and stack are replaced. (a) and (d) describe `fork()`.
</details>

**Q23.** A spinlock is preferable to a mutex when
(a) the critical section is very long
(b) the expected wait is shorter than a context switch
(c) there is only one CPU core
(d) the resource is shared across processes

<details><summary>Answer</summary>
**(b).** Busy-waiting wastes cycles but avoids the two context switches a sleep-and-wake costs. On a single core (c) a spinlock is actively harmful, since the holder cannot run while the spinner burns its quantum.
</details>

**Q24.** Which is true of a context switch?
(a) It is performed by the process itself
(b) It always involves a TLB flush
(c) It does no useful work for any process
(d) It is faster between processes than between threads

<details><summary>Answer</summary>
**(c).** It is pure overhead. (a) it is performed by the kernel. (b) a thread switch within the same process needs no TLB flush, since the address space is unchanged. (d) is backwards.
</details>

**Q25.** Demand paging primarily improves
(a) CPU utilisation only
(b) the degree of multiprogramming
(c) disk throughput
(d) context-switch speed

<details><summary>Answer</summary>
**(b).** Because only the referenced pages are resident, each process occupies less physical memory, so more processes fit simultaneously — which in turn raises CPU utilisation as a *consequence*, not as the direct mechanism.
</details>

---

## Scoring

| Score /25 | Reading |
|---|---|
| 22+ | OS is OA-ready |
| 17–21 | Solid; drill the missed subtopics |
| 12–16 | Re-read `concepts.md`, then redo this set |
| < 12 | Study OS properly before attempting timed MCQs |

Log every miss — and every lucky guess — in `10_Mistake_Log_and_Revision/mistake-log.md` with a `concept` or `misread` tag.
