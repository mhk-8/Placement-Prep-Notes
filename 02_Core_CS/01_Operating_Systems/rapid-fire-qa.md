# Operating Systems — Rapid Fire Q&A

> Interview answers in **three sentences or fewer**, in your own words. Read these aloud — the spoken version is what gets graded.
> Rewrite any answer that sounds like a textbook. An answer you have phrased yourself survives follow-ups; a memorised one does not.

---

**1. What is an operating system?**
A resource manager and an abstraction layer. It multiplexes the CPU, memory and devices among competing processes, and it hides hardware detail behind uniform interfaces like files and virtual memory.

**2. Process vs thread?**
A process owns an address space; a thread is a scheduling unit inside one. Threads share the heap, globals and file descriptors but have private stacks and registers, which makes them cheap to create and switch but means one bad pointer corrupts everyone.

**3. What happens when you run a program?**
The shell forks, the child calls exec, and the loader maps the executable's text and data segments into a fresh address space. The OS creates a PCB, puts the process in the ready queue, and the scheduler eventually dispatches it; pages are faulted in on demand as execution touches them.

**4. What is in a PCB?**
Process ID and state, the saved program counter and registers, scheduling information, memory-management data such as page-table pointers, accounting information, and the open-file table.

**5. What exactly does a context switch cost?**
Saving and restoring registers is cheap; the real cost is the cold cache and, for a process switch, the TLB flush and page-table change. That is why thread switches within a process are substantially cheaper than process switches.

**6. Why is SJF optimal, and why can't we use it?**
It minimises average waiting time because putting shorter jobs first reduces the total accumulated wait. It is unimplementable because burst lengths are not known in advance; MLFQ approximates it by demoting jobs that turn out to be long.

**7. How do you choose a time quantum?**
Long enough that context-switch overhead stays a small fraction of it, short enough that interactive response feels immediate — typically 10–100 ms. The usual rule of thumb is that around 80% of bursts should complete within one quantum.

**8. What is a race condition?**
When the result depends on the interleaving of concurrent accesses to shared state. The canonical case is `count++`, which is a load, an increment and a store, so two threads can both read the old value.

**9. Mutex vs semaphore?**
A mutex has ownership — only the thread that locked it may unlock it — and exists for mutual exclusion. A semaphore is a counter with no owner, used for signalling and for counting a pool of resources.

**10. What is a monitor?**
A language-level construct that bundles data with the methods that operate on it and guarantees only one thread is inside at a time, with condition variables for waiting. Java's `synchronized` plus `wait`/`notify` is the common example.

**11. When would you use a spinlock?**
When the expected wait is shorter than two context switches and you are on a multicore machine — typically short kernel critical sections. On a single core a spinlock is actively harmful, since the spinner prevents the holder from running.

**12. Explain the producer–consumer problem.**
A bounded buffer shared by producers and consumers, synchronised with an `empty` semaphore, a `full` semaphore and a mutex. The ordering matters: take the counting semaphore before the mutex, or a full buffer deadlocks the producer while it holds the lock.

**13. What are the four deadlock conditions?**
Mutual exclusion, hold and wait, no preemption, and circular wait. All four must hold simultaneously, so breaking any one prevents deadlock — imposing a global ordering on resource acquisition is the practical choice.

**14. Deadlock vs starvation vs livelock?**
Deadlock is a cyclic wait where nothing proceeds. Starvation is one process indefinitely postponed while others make progress. Livelock is processes actively changing state in response to one another without any of them advancing.

**15. What does a real OS do about deadlock?**
Mostly nothing — the ostrich algorithm. Detection and recovery is expensive, avoidance requires declaring maximum demands in advance, so Linux and Windows leave user-level deadlock to the application.

**16. Explain virtual memory.**
Each process gets its own linear address space that the MMU translates to physical frames, so processes are isolated and can use more address space than there is RAM. Pages are brought in on demand and evicted when memory is tight.

**17. Why is paging better than contiguous allocation?**
Fixed-size frames mean any free frame fits any page, so external fragmentation disappears entirely. The cost is internal fragmentation in the last page and the extra memory access for translation, which the TLB largely hides.

**18. What is a TLB and why does it matter?**
A small associative cache of recent page-table entries. Without it every memory reference would need a second reference to read the page table, roughly doubling memory latency; with a 98% hit rate the overhead becomes a few percent.

**19. Why multi-level page tables?**
A flat table for a 32-bit space with 4 KB pages is 4 MB per process, most of it never touched. Multi-level paging pages the page table itself, so only the levels covering actually-used regions are resident.

**20. What happens on a page fault?**
The MMU traps to the kernel, which checks whether the reference is legal, finds a free frame or evicts one, schedules the disk read, blocks the process, and restarts the faulting instruction once the page is in. It costs milliseconds against nanoseconds for a hit, which is why fault rates must stay minuscule.

**21. LRU vs FIFO vs Optimal?**
Optimal evicts the page used furthest in the future and is the unachievable benchmark. LRU approximates it using recency and behaves well because programs exhibit temporal locality. FIFO is cheapest and worst, and is the only one of the three that suffers Belady's anomaly.

**22. What is Belady's anomaly?**
More frames producing more page faults. It happens in FIFO but never in LRU or Optimal, because those are stack algorithms whose resident set with n frames is always a subset of the set with n+1.

**23. What is thrashing and how do you fix it?**
The system pages so heavily that almost no execution happens, because the combined working sets exceed physical memory. The fixes are the working-set model or page-fault-frequency control, both of which amount to reducing the degree of multiprogramming.

**24. Internal vs external fragmentation?**
Internal is space wasted inside an allocated block — the tail of the last page. External is free memory that exists but is not contiguous enough to satisfy a request, which is what paging eliminates.

**25. What is an inode?**
The on-disk structure holding a file's metadata and block pointers. It does not hold the filename, which is why several directory entries can hard-link to one inode.

**26. Explain the memory layout of a process.**
Text at the bottom, then initialised data, then BSS, then the heap growing upward, and the stack growing down from the top with free space between them. Threads share all of it except their stacks.

**27. How do processes communicate?**
Pipes and FIFOs for byte streams, message queues for discrete messages, shared memory when speed matters, and sockets when the peers may be on different machines. Shared memory is fastest because it avoids kernel copies, but it needs explicit synchronisation.

**28. What is a system call, and how is it different from a function call?**
A controlled entry into kernel mode, invoked through a trap instruction rather than a jump. It is far more expensive than a function call because of the mode switch and the argument validation the kernel must perform.

**29. User mode vs kernel mode?**
A hardware privilege bit. Kernel mode can execute privileged instructions and touch any memory; user mode cannot, so any attempt traps. It is the mechanism that makes process isolation enforceable rather than merely conventional.

**30. What is an interrupt, and how does it differ from a trap?**
An interrupt is asynchronous and hardware-generated — a device signalling completion. A trap is synchronous and software-generated — a system call, a page fault, a divide by zero. Both vector through the same interrupt table.

**31. Preemptive vs non-preemptive scheduling?**
Preemptive schedulers can take the CPU away on a timer or a higher-priority arrival; non-preemptive ones wait for the process to block or exit. Preemption gives responsiveness and requires careful synchronisation of shared kernel data.

**32. What is priority inversion, and what fixes it?**
A high-priority task waits on a lock held by a low-priority task, which is itself preempted by a medium-priority task, so the high-priority task waits on the medium one. The fix is priority inheritance: the lock holder temporarily inherits the waiter's priority.

**33. What is copy-on-write?**
After `fork`, parent and child share pages marked read-only; a write traps and the kernel copies just that page. It makes `fork` followed immediately by `exec` almost free, which is the common case.

**34. What is demand paging's effect on the degree of multiprogramming?**
It raises it, because each process needs only its working set resident rather than its whole image, so more processes fit in memory and CPU utilisation goes up.

**35. Have you used threads in your own work?**
*(Answer from your projects. Name the synchronisation primitive you used, one bug you hit, and how you found it — a concrete debugging story here is worth more than any definition above.)*
