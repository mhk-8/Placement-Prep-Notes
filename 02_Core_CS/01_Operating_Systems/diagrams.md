# Operating Systems — Diagrams

> Draw each from memory on blank paper. If you cannot, you do not know the topic yet.

---

## 1. Process state diagram

```
                  admitted           dispatch              exit
     [NEW] ─────────────────> [READY] ────────> [RUNNING] ──────> [TERMINATED]
                                 ^                  │
                                 │  interrupt /     │  I/O or event wait
                                 │  quantum expiry  │
                                 │                  v
                                 └──────────── [WAITING]
                                   I/O or event completion
```

**The point to remember:** `RUNNING → WAITING` is voluntary (the process requested I/O); `RUNNING → READY` is involuntary (the scheduler preempted it). There is **no** direct `WAITING → RUNNING` transition — a woken process must go through READY.

---

## 2. Memory layout of a process

```
   high addresses
   ┌──────────────────┐
   │      STACK       │  local variables, return addresses, parameters
   │        │         │  grows DOWNWARD
   │        v         │
   ├──────────────────┤
   │                  │
   │   (free space)   │
   │                  │
   ├──────────────────┤
   │        ^         │
   │        │         │  grows UPWARD
   │       HEAP       │  malloc / new
   ├──────────────────┤
   │   BSS segment    │  uninitialised globals/statics (zeroed at load)
   ├──────────────────┤
   │  DATA segment    │  initialised globals/statics
   ├──────────────────┤
   │  TEXT segment    │  the program code (read-only, shareable)
   └──────────────────┘
   low addresses
```

Threads of the same process share everything here **except the stack** — each thread gets its own.

---

## 3. Address translation with paging and a TLB

```
  CPU generates logical address
  ┌──────────────┬─────────────┐
  │ page number p│  offset  d  │      page size 2^n → d is the low n bits
  └──────┬───────┴──────┬──────┘
         │              │
         v              │
    ┌─────────┐  hit    │
    │   TLB   │─────────┼──────> frame f
    └────┬────┘         │
         │ miss         │
         v              │
    ┌──────────────┐    │
    │  Page Table  │────┼──────> frame f   (+ one extra memory access)
    │  in memory   │    │
    └──────────────┘    │
                        v
              ┌──────────────┬─────────┐
              │   frame f    │offset d │  = physical address
              └──────────────┴─────────┘
```

`EAT = h(TLB + m) + (1 − h)(TLB + 2m)`

---

## 4. Resource allocation graph and deadlock

```
   DEADLOCK (cycle, one instance each)     NO DEADLOCK (cycle, but R2 has 2 instances)

      P1 ──request──> R1                       P1 ──request──> R1(••)
      ^                │                                        │
      │                assign                                assign
      │                v                                        v
      R2 <──assign── P2                                        P2
      ^                │                       P3 ──assign──── R2(••)
      └──request───────┘
```

- **Circle** = process, **rectangle** = resource, **dots inside** = instances.
- With **one instance per resource type**, a cycle ⟺ deadlock.
- With **multiple instances**, a cycle is necessary but **not sufficient**.

---

## 5. Gantt chart template (use this for every scheduling numerical)

```
Processes: P1(AT=0,BT=5)  P2(AT=1,BT=3)  P3(AT=2,BT=8)    — SRTF

 0     1           4              9                      17
 ├──P1─┼─────P2────┼──────P1──────┼──────────P3──────────┤

 Completion:  P2=4    P1=9    P3=17
 TAT = CT-AT: P1=9-0=9   P2=4-1=3    P3=17-2=15
 WT  = TAT-BT:P1=9-5=4   P2=3-3=0    P3=15-8=7
 Avg WT = (4+0+7)/3 = 3.67
```

**Always draw the chart, always write the two formulas underneath.** Mental arithmetic on scheduling questions is how marks are lost.

---

## 6. Producer–consumer with semaphores

```
   semaphore mutex = 1        // mutual exclusion on the buffer
   semaphore empty = N        // count of empty slots
   semaphore full  = 0        // count of filled slots

   PRODUCER                        CONSUMER
   ─────────                       ─────────
   wait(empty)   <-- FIRST         wait(full)    <-- FIRST
   wait(mutex)                     wait(mutex)
     add item to buffer              remove item from buffer
   signal(mutex)                   signal(mutex)
   signal(full)                    signal(empty)
```

**Swapping the first two waits deadlocks.** If the producer takes `mutex` and then blocks on a full buffer, the consumer can never enter to drain it.

---

## 7. Memory hierarchy

```
        ┌──────────────┐   ~1 ns        < 1 KB       registers
        │  Registers   │
        ├──────────────┤   ~1-4 ns      32-64 KB     L1 cache
        │   L1 cache   │
        ├──────────────┤   ~10 ns       256 KB-1 MB  L2 cache
        │   L2 cache   │
        ├──────────────┤   ~30-40 ns    8-32 MB      L3 cache (shared)
        │   L3 cache   │
        ├──────────────┤   ~100 ns      GBs          main memory (DRAM)
        │ Main memory  │
        ├──────────────┤   ~100 us      TBs          SSD
        │     SSD      │
        ├──────────────┤   ~10 ms       TBs          HDD
        │     HDD      │
        └──────────────┘
```

Each level down is roughly 10–100× slower and 10× cheaper per byte. This table is also the basis of the system-design latency numbers in `04_System_Design`.

---

## 8. Inode structure

```
   ┌──────────────────────────┐
   │ mode, uid, gid, size     │   metadata — NOT the filename
   │ atime, mtime, ctime      │
   │ link count               │
   ├──────────────────────────┤
   │ direct block ptr  0..11  │──> data blocks           (12 blocks)
   ├──────────────────────────┤
   │ single indirect          │──> block of ptrs ──> data
   ├──────────────────────────┤
   │ double indirect          │──> ptrs ──> ptrs ──> data
   ├──────────────────────────┤
   │ triple indirect          │──> ptrs ──> ptrs ──> ptrs ──> data
   └──────────────────────────┘
```

The filename lives in the **directory entry**, which maps name → inode number. That is why a hard link is just a second directory entry pointing at the same inode, and why the inode carries a link count.
