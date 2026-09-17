# Computer Architecture — Worked Numericals

> Three formula families cover almost every architecture question in an OA: pipeline speedup, cache address arithmetic and AMAT, and Amdahl's law.

---

## N1. Pipeline speedup

**Q.** A 5-stage pipeline with a 2 ns clock executes 100 instructions. Compare with a non-pipelined implementation and find the speedup.

```
Non-pipelined: each instruction needs all 5 stages
             = 100 × 5 × 2 ns = 1000 ns

Pipelined:     (k + n − 1) × Tc = (5 + 100 − 1) × 2 = 104 × 2 = 208 ns

Speedup = 1000 / 208 = 4.81
Maximum possible speedup = k = 5
```

**Why 4.81 and not 5:** the first instruction still needs k cycles to fill the pipeline — the `k − 1` term. The larger n gets, the closer the speedup approaches k.

---

## N2. Pipeline with unequal stage times

**Q.** Stage delays are 150, 120, 160, 140 and 110 ps, with a 5 ps latch overhead per stage. Find the clock period and the speedup for a large n.

```
Clock period = max(stage delay) + latch = 160 + 5 = 165 ps
Non-pipelined time per instruction = sum of stages = 680 ps
                                   (no latch overhead when not pipelined)

Speedup (n → ∞) = 680 / 165 = 4.12
```
**The lesson:** the slowest stage sets the clock, so an unbalanced pipeline never reaches k. Splitting the 160 ps stage in two would help more than optimising any other stage.

---

## N3. Pipeline with stalls

**Q.** A 5-stage pipeline runs 1000 instructions. 20% are loads, and half of those cause a 1-cycle load-use stall. 15% are branches with a 20% mispredict rate, each mispredict costing 3 cycles. Find the average CPI and the speedup over a 5-cycle non-pipelined design.

```
Base CPI                = 1
Load-use stalls         = 0.20 × 0.50 × 1 = 0.10 cycles/instruction
Branch mispredict stalls= 0.15 × 0.20 × 3 = 0.09 cycles/instruction

Average CPI = 1 + 0.10 + 0.09 = 1.19
Speedup over non-pipelined = 5 / 1.19 = 4.20
```
This is the realistic version of N1, and it shows where the theoretical 5× actually goes.

---

## N4. Cache address decomposition — direct mapped

**Q.** 16 KB direct-mapped cache, 32-byte blocks, 32-bit addresses. Split the address.

```
Block offset = log2(32)      = 5 bits
Number of blocks = 16 KB / 32 B = 512
Direct mapped ⇒ sets = blocks = 512
Index        = log2(512)     = 9 bits
Tag          = 32 − 9 − 5    = 18 bits

| tag 18 | index 9 | offset 5 |
```

---

## N5. Same cache, 4-way set associative

**Q.** Same 16 KB cache and 32-byte blocks, now 4-way set associative.

```
Blocks       = 512  (unchanged — cache size and block size are the same)
Sets         = 512 / 4 = 128
Index        = log2(128)  = 7 bits
Offset       = 5 bits
Tag          = 32 − 7 − 5 = 20 bits

| tag 20 | index 7 | offset 5 |
```
**Associativity moves bits from the index into the tag.** Fewer sets means a coarser index and more tag bits — which is the storage overhead you pay for fewer conflict misses.

**Fully associative** would be the extreme: 0 index bits, tag = 32 − 5 = 27 bits, and every line compared in parallel.

---

## N6. AMAT, one level

**Q.** L1 hit time 1 ns, miss rate 5%, miss penalty 100 ns. Find AMAT.

```
AMAT = hit time + miss rate × miss penalty
     = 1 + 0.05 × 100
     = 6 ns
```
**The point to state:** a 5% miss rate makes average access **six times** the hit time. Cache performance is dominated by the misses, not the hits, which is why small miss-rate improvements matter so much.

---

## N7. AMAT, two levels

**Q.** L1: hit 1 ns, miss rate 5%. L2: hit 10 ns, **local** miss rate 20%. Memory: 200 ns.

```
AMAT = Hit_L1 + MR_L1 × (Hit_L2 + MR_L2 × Penalty_mem)
     = 1 + 0.05 × (10 + 0.20 × 200)
     = 1 + 0.05 × 50
     = 3.5 ns
```
**Watch the miss-rate convention.** *Local* miss rate is misses in L2 divided by accesses *to L2*; *global* miss rate is divided by all CPU accesses. Here global L2 miss rate = 0.05 × 0.20 = 1%. Questions switch between the two, so read carefully.

---

## N8. Hit ratio from a reference count

**Q.** A program makes 10,000 memory references with 400 misses. Hit time 2 ns, miss penalty 80 ns. Find the hit ratio and AMAT.

```
Miss rate  = 400 / 10,000 = 0.04  →  hit ratio = 96%
AMAT       = 2 + 0.04 × 80 = 2 + 3.2 = 5.2 ns
```

---

## N9. Amdahl's law

**Q.** 40% of a program is parallelisable. Find the speedup with 8 processors and the theoretical maximum.

```
Speedup(N) = 1 / ((1 − P) + P/N)
           = 1 / (0.60 + 0.40/8)
           = 1 / (0.60 + 0.05)
           = 1 / 0.65
           = 1.54

Maximum (N → ∞) = 1 / (1 − P) = 1 / 0.60 = 1.67
```
**The message:** eight processors buy a 1.54× speedup, and infinite processors buy 1.67×. The serial 60% is the ceiling, and no hardware removes it.

**Follow-up worth internalising:** to reach a 4× speedup you would need `1/((1−P)) ≥ 4`, i.e. P ≥ 75% — so the fix is restructuring the algorithm, not buying machines.

---

## N10. IEEE 754 conversion

**Q.** Represent −6.25 in IEEE 754 single precision.

```
Step 1  |−6.25| in binary = 110.01
Step 2  Normalise         = 1.1001 × 2^2
Step 3  Sign bit          = 1        (negative)
Step 4  Exponent          = 2 + 127 = 129 = 1000 0001
Step 5  Mantissa          = 1001 followed by 19 zeros
                            (the leading 1 is implicit, not stored)

  1 | 10000001 | 10010000000000000000000
  = 0xC0C80000
```
**Verification:** 1.1001₂ = 1 + 0.5 + 0.0625 = 1.5625; 1.5625 × 2² = 6.25 ✔, negative ✔.

---

## N11. Cache-friendly code

**Q.** A 1024×1024 array of 4-byte ints, 64-byte cache lines. How many cache misses does row-major traversal cause, versus column-major, assuming the cache is far smaller than the array?

```
Elements per cache line = 64 / 4 = 16

Row-major (a[i][j] with j innermost): consecutive elements share a line
  → 1 miss per 16 elements  → 1024 × 1024 / 16 = 65,536 misses

Column-major (a[i][j] with i innermost): each access jumps 4 KB
  → every access misses     → 1024 × 1024      = 1,048,576 misses

Ratio = 16×
```
This is the single most useful architecture fact for a software engineer, and it is a strong concrete answer to "why should an application developer care about caches".

---

## Practice set

1. A 4-stage pipeline, 1 ns clock, 200 instructions. Time and speedup?
2. 32 KB, 2-way set associative, 64-byte blocks, 32-bit address. Give the tag/index/offset split.
3. Hit time 1 ns, hit ratio 92%, miss penalty 120 ns. AMAT?
4. 70% of a program is parallelisable. Speedup with 4 processors? Maximum?
5. How many exponent bits and what bias does IEEE 754 double precision use?
6. A 5-stage pipeline where 30% of instructions are branches with a 25% mispredict rate costing 2 cycles. Average CPI?

<details><summary>Answers</summary>

1. `(4 + 200 − 1) × 1 = 203 ns` pipelined versus `200 × 4 = 800 ns` → speedup **3.94**.
2. Offset = log₂64 = **6**. Blocks = 32 KB/64 B = 512; sets = 512/2 = 256 → index = **8**. Tag = 32 − 8 − 6 = **18**.
3. Miss rate = 8% → AMAT = 1 + 0.08 × 120 = **10.6 ns**.
4. `1/(0.30 + 0.70/4)` = 1/0.475 = **2.11**; maximum = 1/0.30 = **3.33**.
5. **11 exponent bits, bias 1023** (and 52 mantissa bits).
6. CPI = 1 + 0.30 × 0.25 × 2 = **1.15**.

</details>
