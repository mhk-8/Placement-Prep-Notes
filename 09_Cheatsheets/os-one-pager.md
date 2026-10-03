
# Operating Systems — One Pager

> **Use:** every core-CS round. OS is the highest-yield fundamentals subject for your target
> companies (NVIDIA, Qualcomm, Samsung, Microsoft all probe it), and it connects directly to
> your GPU and compiler work. ⭐

---

## 1. Process vs thread ⭐⭐⭐

| | Process | Thread |
|---|---|---|
| Address space | Own, isolated | **Shared** with siblings |
| Creation cost | High (page tables, PCB) | Low |
| Context switch | Expensive (TLB flush, page-table swap) | Cheap (registers + stack pointer) |
| Communication | IPC: pipes, shared memory, sockets, message queues | Shared variables ⚠️ needs synchronisation |
| Crash isolation | One crash does not kill others | One crash kills the process |
| Shares | — | Code, data, heap, open files. **Not** stack, registers, PC ⭐ |

```
PCB holds: PID · state · program counter · registers · memory limits/page table ·
           open-file table · scheduling info · accounting
Thread has its own: stack · registers · program counter · thread ID
```

**Process states:** new → ready ⇄ running → terminated; running → waiting (I/O) → ready.

**fork():** returns 0 in the child, the child's PID in the parent, −1 on failure. Copy-on-write
means pages are shared read-only until written. ⭐
**Zombie:** terminated child whose parent has not `wait()`ed — its PCB lingers.
**Orphan:** parent died first; re-parented to `init`/`systemd`.

---

## 2. Scheduling ⭐⭐

| Algorithm | Preemptive | Starvation | Notes |
|---|---|---|---|
| FCFS | ❌ | ❌ | **Convoy effect** — one long job delays everything ⚠️ |
| SJF | ❌ | ✅ long jobs | Provably optimal average waiting time, but burst times are unknown |
| SRTF | ✅ | ✅ | Preemptive SJF |
| Priority | either | ✅ | Fix with **ageing** |
| **Round Robin** | ✅ | ❌ | Fair; quantum too small → switch overhead dominates ⭐ |
| Multilevel queue | ✅ | possible | Fixed queues per class |
| **MLFQ** | ✅ | ❌ with boosting | Practical default; demotes CPU-bound, favours interactive ⭐ |
| CFS (Linux) | ✅ | ❌ | Red-black tree on **virtual runtime**; picks the smallest vruntime ⭐ |

```
Turnaround = completion − arrival        Waiting = turnaround − burst
Response   = first CPU − arrival         Throughput = jobs / time
```
> ⭐ **Convoy effect** and **ageing** are the two terms that get asked by name.

---

## 3. Synchronisation ⭐⭐⭐

```
RACE CONDITION  : outcome depends on thread interleaving.
CRITICAL SECTION: code touching shared state.
Correct solution requires: Mutual exclusion · Progress · Bounded waiting.
```

| Primitive | Semantics |
|---|---|
| **Mutex** | Binary lock, ownership — only the locker unlocks |
| **Semaphore** | Counter; `wait/P` decrements and blocks at 0, `signal/V` increments. Counting or binary |
| **Spinlock** | Busy-waits. Correct only when the critical section is shorter than a context switch ⭐ |
| **Monitor / condition variable** | Mutex + wait/notify; `wait` releases the lock atomically ⭐ |
| Read-write lock | Many readers or one writer; writer starvation possible ⚠️ |
| Barrier | All n threads must arrive before any proceeds ⭐ `__syncthreads()` is this |
| Atomic / CAS | Lock-free `compare_and_swap`; the basis of lock-free structures |

> **Mutex vs binary semaphore (asked constantly):** a mutex has *ownership* and is released by
> the same thread; a binary semaphore is just a signal and can be posted by any thread. Use a
> semaphore to signal between threads, a mutex to protect data. ⭐

### Producer-consumer (bounded buffer)
```c
sem_t empty = N, full = 0;  mutex m;
producer: wait(empty); lock(m);   /* insert */   unlock(m); signal(full);
consumer: wait(full);  lock(m);   /* remove */   unlock(m); signal(empty);
// ⚠️ Swapping the wait(empty) and lock(m) order causes deadlock. Know why.
```

### Other classic problems
```
Reader-writer     : readers share; first reader locks the writer out, last reader releases
Dining philosophers: deadlock if all grab left first. Fixes: odd/even asymmetry, an arbiter,
                     or allow at most n−1 to sit
```

---

## 4. Deadlock ⭐⭐⭐

**Coffman's four necessary conditions (all must hold):**
```
1. Mutual exclusion   2. Hold and wait   3. No preemption   4. Circular wait
```

| Strategy | How |
|---|---|
| **Prevention** | Break one condition: request all resources at once; allow preemption; impose a **global lock ordering** ⭐ the practical answer |
| **Avoidance** | **Banker's algorithm** — grant only if a safe sequence still exists |
| **Detection** | Resource-allocation graph; a cycle = deadlock (with single-instance resources) |
| **Recovery** | Kill a victim process, or roll back |

> **Livelock:** threads keep changing state but make no progress. **Starvation:** a thread
> never gets the resource. Neither is deadlock — the distinction is a frequent follow-up. ⭐

**Banker's algorithm:** keep `Available`, `Max`, `Allocation`; `Need = Max − Allocation`.
A state is safe if some ordering of processes can each finish with the currently available
resources plus what earlier processes release.

---

## 5. Memory management ⭐⭐

```
Logical/virtual address → [MMU + page table] → physical address
Page size typically 4 KB. Page number = addr >> 12; offset = addr & 0xFFF.
```

| Concept | Key point |
|---|---|
| **Paging** | Fixed-size frames; causes **internal** fragmentation only |
| **Segmentation** | Variable-size logical units; causes **external** fragmentation |
| **TLB** | Cache of page-table entries. Hit → 1 memory access; miss → walk the table ⭐ |
| Multi-level page table | 4-level on x86-64; saves space for sparse address spaces |
| Inverted page table | One entry per frame, not per page |
| **Demand paging** | Load a page only on first access |
| **Page fault** | Trap → OS finds the page on disk → evicts if needed → updates PTE → restarts the instruction |
| **Thrashing** | More time paging than executing. Cause: over-committed memory / too-small working set. Fix: reduce multiprogramming, working-set model, page-fault-frequency control ⭐ |
| Copy-on-write | `fork()` shares pages read-only; duplicate on first write |
| Memory-mapped file | `mmap` — file pages in the address space |
| Dirty bit | Avoid writing back unmodified pages |

**Effective access time** with TLB hit ratio *h*, TLB time *t*, memory time *m*:
📐 `EAT = h(t + m) + (1 − h)(t + 2m)` — one extra memory access for the page-table walk.

### Page-replacement algorithms
| Algorithm | Note |
|---|---|
| FIFO | Suffers **Belady's anomaly** — more frames can mean more faults ⚠️ asked by name |
| **Optimal (OPT)** | Evict the page used furthest in the future. Unimplementable; the benchmark |
| **LRU** | Stack algorithm, no Belady anomaly. Exact LRU needs a counter or list per access |
| LRU approximations | Second chance / clock with a reference bit ⭐ what real kernels use |
| LFU / MFU | Rarely good alone |

### Allocation strategies
`First fit` (fast) · `Best fit` (leaves tiny holes) · `Worst fit` · `Next fit`.
**Compaction** fixes external fragmentation; it needs relocatable addresses.

---

## 6. Virtual memory and the cache hierarchy ⭐ (your systems edge)

```
registers  ~0.3 ns   |  L1  ~1 ns  (32-64 KB)   |  L2  ~4 ns  |  L3 ~15-40 ns
DRAM       ~80-100 ns|  NVMe SSD ~100 µs        |  HDD seek ~10 ms
```

| Term | Meaning |
|---|---|
| Spatial locality | Nearby addresses soon → why cache lines are 64 B |
| Temporal locality | Same address soon → why caching works at all |
| Cache line | 64 B typically. A single `int` read pulls 16 ints ⭐ |
| **False sharing** | Two threads write different variables in the *same line* → line ping-pongs between cores. Fix: pad/align to 64 B ⚠️ high-value answer |
| Write-through vs write-back | Immediate vs on eviction (needs a dirty bit) |
| Mapping | Direct · set-associative (n-way) · fully associative |
| 3 C's of misses | Compulsory · Capacity · Conflict ⭐ |

> ⭐ **Connect this to your CUDA work when asked:** coalesced global-memory access on a GPU is
> the same principle as cache-line utilisation on a CPU — you are paying for a wide transaction
> either way, so you want every byte you fetch to be used.

---

## 7. I/O, files and disks

```
System call: user mode → trap/syscall instruction → kernel mode → return. Costs ~100s of ns.
Why two modes: protection. Privileged instructions and device access are kernel-only.
Interrupt vs trap: asynchronous (device) vs synchronous (software, e.g. divide by zero) ⭐
DMA: device transfers to memory without per-byte CPU involvement
Blocking vs non-blocking vs async I/O; select/poll/epoll for multiplexing ⭐
```

| File allocation | Pros / cons |
|---|---|
| Contiguous | Fast sequential + random; external fragmentation, hard to grow |
| Linked | No fragmentation; terrible random access |
| **Indexed (inode)** | Random access; index block overhead. Unix uses direct + single/double/triple indirect ⭐ |

**Disk scheduling:** FCFS · SSTF (starvation) · SCAN · **C-SCAN** · LOOK · C-LOOK.
**RAID:** 0 striping (no redundancy) · 1 mirroring · 5 distributed parity (1 failure) ·
6 double parity (2 failures) · 10 mirror+stripe.

---

## 8. Linux / practical ⭐

```
ps · top/htop · kill -9 · nice · strace · lsof · df/du · free · vmstat · iostat
grep/awk/sed · xargs · chmod 755 (rwxr-xr-x) · soft vs hard link
> redirect · 2>&1 · | pipe · & background · nohup
Shared memory (shm) · pipes (unidirectional) · named pipes/FIFO · sockets · signals
IPC speed: shared memory fastest (no copy); message passing is safer ⭐
gdb · valgrind (leaks, invalid reads) · perf (cache misses, IPC) ⭐ relevant to your profile
```

---

## Recall questions
1. What do threads of a process share, and what do they not?
2. Mutex vs binary semaphore — name the real difference.
3. State Coffman's four conditions and one practical way to break each.
4. What is Belady's anomaly, and which algorithm suffers it?
5. Write the EAT formula with a TLB hit ratio.
6. What is false sharing and how do you fix it?
7. Why does swapping `wait(empty)` and `lock(m)` in producer-consumer deadlock?
8. Distinguish deadlock, livelock and starvation.
