# Operating Systems — Worked Numericals

> The highest-yield file in this folder. OA numericals are mechanical once you have the procedure; the marks are lost to arithmetic slips and to confusing TAT with WT.
> **Always: draw the chart, write the formulas, then substitute.**

**The two formulas, every time:** `TAT = CT − AT` and `WT = TAT − BT`.

---

## N1. FCFS

**Q.** P1(AT=0, BT=4), P2(AT=1, BT=3), P3(AT=2, BT=1). Find average TAT and WT under FCFS.

```
 0        4         7      8
 ├───P1───┼───P2────┼──P3──┤
```

| P | AT | BT | CT | TAT = CT−AT | WT = TAT−BT |
|---|---|---|---|---|---|
| P1 | 0 | 4 | 4 | 4 | 0 |
| P2 | 1 | 3 | 7 | 6 | 3 |
| P3 | 2 | 1 | 8 | 6 | 5 |

**Average TAT = 16/3 = 5.33** · **Average WT = 8/3 = 2.67**

*Note the convoy effect: P3 needs only 1 unit but waits 5, because it queued behind a long job.*

---

## N2. SJF (non-preemptive)

**Q.** P1(0,7), P2(2,4), P3(4,1), P4(5,4). Average WT?

At t=0 only P1 is present, so it runs to completion at t=7 — **non-preemptive SJF cannot interrupt it**, which is the trap.
At t=7 the ready set is {P2(4), P3(1), P4(4)} → shortest is P3.
At t=8 the set is {P2(4), P4(4)} → tie, broken by arrival time → P2.

```
 0          7    8         12        16
 ├────P1────┼─P3─┼───P2────┼───P4────┤
```

| P | AT | BT | CT | TAT | WT |
|---|---|---|---|---|---|
| P1 | 0 | 7 | 7 | 7 | 0 |
| P2 | 2 | 4 | 12 | 10 | 6 |
| P3 | 4 | 1 | 8 | 4 | 3 |
| P4 | 5 | 4 | 16 | 11 | 7 |

**Average TAT = 32/4 = 8** · **Average WT = 16/4 = 4**

---

## N3. SRTF (preemptive SJF)

**Q.** P1(0,8), P2(1,4), P3(2,9), P4(3,5). Average WT?

Re-evaluate at **every arrival**:
- t=0: only P1 → run P1
- t=1: P2 remaining 4 < P1 remaining 7 → **preempt**, run P2
- t=2: P3 remaining 9 > P2 remaining 3 → continue P2
- t=3: P4 remaining 5 > P2 remaining 2 → continue P2
- t=5: P2 finishes. Ready: P1(7), P3(9), P4(5) → run P4
- t=10: P4 finishes. Ready: P1(7), P3(9) → run P1
- t=17: P1 finishes → run P3 to 26

```
 0   1        5        10          17            26
 ├P1─┼───P2───┼───P4───┼─────P1────┼──────P3─────┤
```

| P | AT | BT | CT | TAT | WT |
|---|---|---|---|---|---|
| P1 | 0 | 8 | 17 | 17 | 9 |
| P2 | 1 | 4 | 5 | 4 | 0 |
| P3 | 2 | 9 | 26 | 24 | 15 |
| P4 | 3 | 5 | 10 | 7 | 2 |

**Average TAT = 52/4 = 13** · **Average WT = 26/4 = 6.5**

*Compare with N2's shape: SRTF gives a better average but starves the long job P3.*

---

## N4. Round Robin, quantum = 2

**Q.** P1(0,5), P2(1,4), P3(2,2), P4(3,1). Average TAT?

**Convention used:** when a process is preempted at the same instant another arrives, the **arriving** process is queued first. State this — setters exploit the ambiguity.

| Time | Running | Queue after the slice |
|---|---|---|
| 0–2 | P1 (5→3) | P2, P3 arrive; then P1 requeued → [P2, P3, P1] |
| 2–4 | P2 (4→2) | P4 arrives; then P2 requeued → [P3, P1, P4, P2] |
| 4–6 | P3 (2→0) ✔ | [P1, P4, P2] |
| 6–8 | P1 (3→1) | [P4, P2, P1] |
| 8–9 | P4 (1→0) ✔ | [P2, P1] |
| 9–11 | P2 (2→0) ✔ | [P1] |
| 11–12 | P1 (1→0) ✔ | — |

| P | AT | BT | CT | TAT | WT |
|---|---|---|---|---|---|
| P1 | 0 | 5 | 12 | 12 | 7 |
| P2 | 1 | 4 | 11 | 10 | 6 |
| P3 | 2 | 2 | 6 | 4 | 2 |
| P4 | 3 | 1 | 9 | 6 | 5 |

**Average TAT = 32/4 = 8** · **Average WT = 20/4 = 5**

---

## N5. Page replacement — FIFO vs LRU vs OPT

**Q.** Reference string `7 0 1 2 0 3 0 4 2 3 0 3 2`, **3 frames**. Page faults under each policy?

**FIFO** (evict the oldest loaded):

| Ref | 7 | 0 | 1 | 2 | 0 | 3 | 0 | 4 | 2 | 3 | 0 | 3 | 2 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Frames | 7 | 7,0 | 7,0,1 | 0,1,2 | — | 1,2,3 | 2,3,0 | 3,0,4 | 0,4,2 | 4,2,3 | 2,3,0 | — | — |
| Fault? | F | F | F | F | hit | F | F | F | F | F | F | hit | hit |

**FIFO = 10 faults**

**LRU** (evict the least recently used):

| Ref | 7 | 0 | 1 | 2 | 0 | 3 | 0 | 4 | 2 | 3 | 0 | 3 | 2 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Fault? | F | F | F | F | hit | F | hit | F | F | F | F | hit | hit |

**LRU = 9 faults**

**OPT** (evict the page used furthest in the future):

| Ref | 7 | 0 | 1 | 2 | 0 | 3 | 0 | 4 | 2 | 3 | 0 | 3 | 2 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Fault? | F | F | F | F | hit | F | hit | F | hit | hit | F | hit | hit |

**OPT = 7 faults**

**Always OPT ≤ LRU ≤ FIFO in practice**, and OPT is the lower bound no algorithm can beat.

---

## N6. Belady's anomaly — demonstrate it

**Q.** String `1 2 3 4 1 2 5 1 2 3 4 5` under FIFO with 3 frames, then 4 frames.

**3 frames:** faults at refs 1,2,3,4,5,6,7,10,11 → **9 faults**
**4 frames:** faults at refs 1,2,3,4,7,8,9,10,11,12 → **10 faults**

**More frames gave more faults.** This is Belady's anomaly, and it occurs in **FIFO** but never in LRU or OPT, because those are *stack algorithms*: the resident set with n frames is always a subset of the resident set with n+1 frames.

---

## N7. TLB effective access time

**Q.** TLB lookup = 20 ns, one memory access = 100 ns, TLB hit ratio = 80%. Single-level page table. Find the EAT.

On a **hit**: TLB lookup + one memory access for the data = 20 + 100
On a **miss**: TLB lookup + one access to read the page table + one access for the data = 20 + 200

```
EAT = 0.8 × (20 + 100) + 0.2 × (20 + 200)
    = 0.8 × 120 + 0.2 × 220
    = 96 + 44
    = 140 ns
```

**Two conventions exist** and setters use both. Some questions say "TLB access time is negligible" or fold it into the memory access; read the wording and state your assumption. The invariant that is never negotiable: **a TLB miss costs two memory accesses, not one.**

---

## N8. Demand paging EAT and the tolerable fault rate

**Q.** Memory access = 200 ns, page-fault service = 8 ms, page-fault rate p = 0.001. Find the EAT and the slowdown.

```
EAT = (1 − p)·200 + p·8,000,000        (8 ms = 8×10^6 ns)
    = 0.999 × 200 + 0.001 × 8,000,000
    = 199.8 + 8000
    = 8199.8 ns ≈ 8.2 µs
```
**Slowdown = 8199.8 / 200 ≈ 41×** from a fault on one access in a thousand.

**Follow-up: what p keeps degradation under 10%?**
```
EAT ≤ 220  ⇒  200 + p(8,000,000 − 200) ≤ 220
           ⇒  p ≤ 20 / 7,999,800
           ⇒  p ≤ 2.5 × 10⁻⁶
```
Fewer than **one fault per 400,000 accesses**. This number is the whole argument for why locality of reference matters.

---

## N9. Paging — address decomposition and page-table size

**Q.** 32-bit logical address space, 4 KB page size, 4-byte page-table entries. Give the address split and the page-table size per process.

```
Page size 4 KB = 2^12  ⇒  offset = 12 bits
Page number = 32 − 12  =  20 bits
Number of pages = 2^20 = 1,048,576 entries
Page table size = 2^20 × 4 B = 4 MB per process
```

**Follow-up: two-level paging with an inner table that fits in one page.**
```
Entries per page = 4 KB / 4 B = 1024 = 2^10  ⇒  inner index = 10 bits
Remaining for the outer index = 20 − 10 = 10 bits

Split: | outer 10 | inner 10 | offset 12 |
```
Now only the outer table (4 KB) and the inner tables actually in use need to be resident — that is the entire point of multi-level paging.

---

## N10. Banker's algorithm

**Q.** 5 processes, resources A=10, B=5, C=7.

| P | Allocation (A B C) | Max (A B C) | Need = Max − Alloc |
|---|---|---|---|
| P0 | 0 1 0 | 7 5 3 | 7 4 3 |
| P1 | 2 0 0 | 3 2 2 | 1 2 2 |
| P2 | 3 0 2 | 9 0 2 | 6 0 0 |
| P3 | 2 1 1 | 2 2 2 | 0 1 1 |
| P4 | 0 0 2 | 4 3 3 | 4 3 1 |

**Available** = Total − Σ Allocation = (10,5,7) − (7,2,5) = **(3, 3, 2)**

**Is the state safe?** Repeatedly find a process whose Need ≤ Available, run it, and release its allocation.

| Step | Available | Process picked | Need ≤ Avail? | Available after release |
|---|---|---|---|---|
| 1 | (3,3,2) | P1, Need (1,2,2) | ✔ | (3,3,2)+(2,0,0) = (5,3,2) |
| 2 | (5,3,2) | P3, Need (0,1,1) | ✔ | (5,3,2)+(2,1,1) = (7,4,3) |
| 3 | (7,4,3) | P4, Need (4,3,1) | ✔ | (7,4,3)+(0,0,2) = (7,4,5) |
| 4 | (7,4,5) | P0, Need (7,4,3) | ✔ | (7,4,5)+(0,1,0) = (7,5,5) |
| 5 | (7,5,5) | P2, Need (6,0,0) | ✔ | (10,5,7) |

**Safe. Sequence: P1 → P3 → P4 → P0 → P2.**

**Follow-up: P1 requests (1, 0, 2). Grant it?**
1. Request ≤ Need(1,2,2)? ✔  2. Request ≤ Available(3,3,2)? ✔
3. Pretend to allocate: Available = (2,3,0), P1 Alloc = (3,0,2), P1 Need = (0,2,0)
4. Safety check from (2,3,0): P1 ✔ → (5,3,2); P3 ✔ → (7,4,3); P4 ✔ → (7,4,5); P0 ✔ → (7,5,5); P2 ✔
**Safe ⇒ grant the request.**

---

## N11. Disk scheduling

**Rules.** FCFS: serve in arrival order. SSTF: always the nearest request. SCAN (elevator): sweep to one end, reverse. C-SCAN: sweep to the end, jump back to 0, sweep again in the same direction.

**Q.** Queue `98, 183, 37, 122, 14, 124, 65, 67`, head at **53**, disk 0–199. Total head movement under each?

**FCFS:** 53→98→183→37→122→14→124→65→67
`45 + 85 + 146 + 85 + 108 + 110 + 59 + 2 = ` **640 cylinders**

**SSTF:** 53→65→67→37→14→98→122→124→183
`12 + 2 + 30 + 23 + 84 + 24 + 2 + 59 = ` **236 cylinders**

**SCAN** (moving toward higher cylinders first): 53→65→67→98→122→124→183→**199**→37→14
`(199 − 53) + (199 − 14) = 146 + 185 = ` **331 cylinders**

**C-SCAN:** 53→…→199, jump to 0, →14→37
`(199 − 53) + (199 − 0) + (37 − 0) = 146 + 199 + 37 = ` **382 cylinders**

SSTF wins on total movement but can starve distant requests; SCAN and C-SCAN bound the wait, and C-SCAN gives a more uniform wait at the cost of the return sweep.

---

## Practice set — answers at the bottom

1. P1(0,6), P2(1,8), P3(2,7), P4(3,3) under **FCFS**. Average WT?
2. Same processes under **SJF non-preemptive**. Average WT?
3. Reference string `1 2 3 4 5 1 2 3 4 5`, **3 frames**, **LRU**. Faults?
4. TLB hit ratio 90%, TLB 10 ns, memory 80 ns. EAT?
5. 64-bit address space with 8 KB pages — how many offset bits?
6. Memory access 100 ns, fault service 10 ms. What p gives EAT ≤ 200 ns?
7. Head at 100, queue `55, 58, 39, 18, 90, 160, 150, 38, 184`, disk 0–199. **SSTF** total movement?

<details>
<summary>Answers</summary>

1. CT = 6, 14, 21, 24 → TAT = 6, 13, 19, 21 → WT = 0, 5, 12, 18 → **avg 8.75**
2. Order P1(0–6), then ready {P2(8), P3(7), P4(3)} → P4(6–9), P3(9–16), P2(16–24). TAT = 6, 23, 14, 6 → WT = 0, 15, 7, 3 → **avg 6.25**
3. Every reference faults — the working set is 5 pages in 3 frames → **10 faults**
4. 0.9(10+80) + 0.1(10+160) = 81 + 17 = **98 ns**
5. 8 KB = 2¹³ → **13 offset bits**, 51 bits of page number
6. 100 + p(10,000,000 − 100) ≤ 200 ⇒ p ≤ 100/9,999,900 ≈ **1 × 10⁻⁵**
7. 100→90→58→55→39→38→18→150→160→184 = 10+32+3+16+1+20+132+10+24 = **248**

</details>
