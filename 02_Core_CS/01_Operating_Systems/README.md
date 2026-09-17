# Operating Systems

> Folder: `02_Core_CS/01_Operating_Systems`

## Why this matters
The most-asked core subject after DBMS. Concurrency questions also feed into system design.

## Must-know checklist
- [ ] Process vs thread; PCB; context switch cost
- [ ] Process states and scheduling: FCFS, SJF, SRTF, RR, priority, MLFQ (+ compute AT/WT/TAT)
- [ ] Concurrency: race conditions, critical section, mutex vs semaphore, monitors
- [ ] Deadlock: four conditions, prevention, avoidance (Banker's), detection, recovery
- [ ] Memory: paging, segmentation, virtual memory, TLB, page faults
- [ ] Page replacement: FIFO, LRU, Optimal, Belady's anomaly
- [ ] File systems, inodes, journaling
- [ ] IPC: pipes, shared memory, message queues
- [ ] Fork/exec, zombie and orphan processes

## Online-assessment angle
Numerical questions dominate: scheduling tables, page-fault counts, effective access time with TLB hit ratio. Practise the arithmetic under time.

## Interview angle
Classic: 'explain what happens when you run a program', 'mutex vs semaphore', 'how does virtual memory work', producer-consumer code.

## Suggested files in this folder
- `concepts.md`
- `numericals.md`
- `rapid-fire-qa.md`
- `diagrams.md`
- `flashcards.md`

---
Revision rule: after every session, move anything you got wrong into `/10_Mistake_Log_and_Revision/`.
