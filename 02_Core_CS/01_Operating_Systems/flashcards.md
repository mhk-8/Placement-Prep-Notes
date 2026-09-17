# Operating Systems — Flashcards

> Cover the answers, write yours on paper, then check. This is the D+1 / D+3 / D+7 / D+21 material.

## Questions

1. What do threads share and what is private to each?
2. Give the formulas for turnaround time and waiting time.
3. Which scheduling algorithm minimises average waiting time, and why can it not be implemented?
4. What is the convoy effect and which algorithm suffers it?
5. What happens to Round Robin as the quantum grows very large, and as it shrinks toward zero?
6. Which scheduling algorithms can starve a process, and what is the cure?
7. State the three requirements of any critical-section solution.
8. Mutex vs semaphore, in one sentence.
9. In producer–consumer, why must `wait(empty)` precede `wait(mutex)`?
10. State the four necessary conditions for deadlock.
11. Which deadlock condition does resource ordering break?
12. Give the inequality for when deadlock is impossible with n processes each needing m resources.
13. What does "safe state" mean in the Banker's algorithm?
14. How many bits of offset does a 4 KB page give, and how large is a flat page table for a 32-bit space with 4-byte entries?
15. Give the TLB effective-access-time formula and explain why a miss costs two memory accesses.
16. Give the demand-paging EAT formula.
17. What is Belady's anomaly, which algorithms show it, and why are LRU and OPT immune?
18. Which fragmentation does paging suffer, and which does segmentation suffer?
19. What is thrashing, what does it look like from the outside, and how is it fixed?
20. What does an inode contain, and what does it deliberately not contain?
21. Zombie vs orphan?
22. After `n` calls to `fork()`, how many processes exist and how many are new?
23. What is copy-on-write and which common pattern does it optimise?
24. What is priority inversion and how is it solved?
25. Name the four disk-scheduling algorithms and the trade-off between SSTF and SCAN.

---

## Answers

1. Shared: address space, heap, globals/statics, code, open-file table. Private: stack, registers, program counter, thread ID.
2. TAT = CT − AT. WT = TAT − BT.
3. SJF. Burst times are not known in advance; MLFQ approximates it by demoting jobs that prove to be long.
4. One long job at the head of the queue delaying many short ones. FCFS.
5. Large quantum → no preemption occurs, so it degenerates to FCFS. Tiny quantum → ideal fair sharing in theory, but context-switch overhead consumes the CPU.
6. SRTF and priority scheduling. Ageing — raising a process's priority the longer it has waited.
7. Mutual exclusion, progress, and bounded waiting.
8. A mutex has ownership and provides mutual exclusion; a semaphore is an ownerless counter used for signalling and resource counting.
9. Otherwise a producer can acquire the mutex and then block on a full buffer while holding it, so no consumer can enter to drain it — hold and wait, hence deadlock.
10. Mutual exclusion, hold and wait, no preemption, circular wait.
11. Circular wait.
12. Deadlock is impossible when total resources R ≥ n(m − 1) + 1.
13. A sequence exists in which every process can obtain its remaining need from the available resources plus what its predecessors release, and therefore finish.
14. 4 KB = 2¹² → 12 offset bits. 2²⁰ entries × 4 B = 4 MB per process.
15. EAT = h(TLB + m) + (1 − h)(TLB + 2m). A miss requires one memory access to read the page-table entry and a second to fetch the data.
16. EAT = (1 − p)·(memory access) + p·(page-fault service time).
17. More frames causing more page faults. FIFO shows it; LRU and OPT are stack algorithms, so their resident set with n frames is always a subset of the set with n+1 frames.
18. Paging: internal only. Segmentation: external only.
19. The combined working sets exceed physical memory, so the system pages constantly. Externally: CPU utilisation collapses while disk activity saturates. Fixed by the working-set model or page-fault-frequency control, i.e. lowering the degree of multiprogramming.
20. Contains permissions, owner, timestamps, size, link count and block pointers. Does not contain the filename, which lives in the directory entry.
21. A zombie has terminated but its parent has not yet reaped its exit status. An orphan's parent terminated first, so it is re-parented to init, which reaps it.
22. 2ⁿ processes exist; 2ⁿ − 1 are new.
23. Parent and child share pages read-only after `fork`, copying a page only when one writes to it. It makes the fork-then-exec pattern almost free.
24. A high-priority task blocked on a lock held by a low-priority task that is itself preempted by a medium-priority one. Solved by priority inheritance — the holder temporarily assumes the waiter's priority.
25. FCFS, SSTF, SCAN, C-SCAN. SSTF minimises total head movement but can starve distant requests; SCAN bounds the waiting time by sweeping to the end, at the cost of more movement.
