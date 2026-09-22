
# GPU and Parallel Computing — Interview Q&A

> **Why this file exists.** Your resume says CUDA C++ in the *first line* of skills, lists three
> GPU projects and names Nsight Compute. **You will be asked about GPUs in every SDE interview**,
> whether or not the role is HPC. This is your differentiator, and it is also the area where being
> caught out would do the most damage — a candidate who lists CUDA prominently and cannot explain
> coalescing loses more credibility than one who never mentioned it.
>
> Treat this file as mandatory revision before any systems interview.

---

## 1. The execution model ⭐⭐⭐

```
GRID  ──►  BLOCKS  ──►  WARPS (32 threads)  ──►  THREADS

Grid    : all threads of one kernel launch
Block   : scheduled onto ONE SM; threads in a block can use SHARED MEMORY and __syncthreads()
Warp    : 32 threads executing in LOCKSTEP (SIMT). The real unit of execution ⭐
Thread  : has its own registers and program counter (independent PC since Volta)
```

**The hardware mapping:**
```
GPU → many SMs (Streaming Multiprocessors)
SM  → warp schedulers, CUDA cores, register file, shared memory / L1
A block is assigned to exactly one SM and never migrates. Multiple blocks may share an SM,
limited by registers, shared memory, and the maximum threads per SM.
```

⭐ **The single most important sentence:** *"The warp is the unit of execution, not the thread."*
Almost every CUDA performance question — divergence, coalescing, occupancy — follows from that.

**Memory hierarchy, fastest to slowest:**

| Memory | Scope | Latency | Notes |
|---|---|---|---|
| Registers | Thread | ~1 cycle | Spills to local memory (which is *global* memory) ⚠️ |
| **Shared memory** | Block | ~20-30 cycles | Programmer-managed cache; watch bank conflicts |
| L1 / texture cache | SM | ~30 cycles | Texture path gives free bilinear filtering |
| L2 | Device | ~200 cycles | |
| **Global memory** | Device | ~400-800 cycles | The thing you are almost always bound by ⭐ |
| Constant memory | Device (cached) | fast if uniform | Broadcast to a whole warp in one read |
| Host memory | CPU | PCIe, very slow | Pinned memory + streams to overlap |

---

## 2. The three performance concepts you must own ⭐⭐⭐

### (a) Memory coalescing
```
When the 32 threads of a warp access CONSECUTIVE, ALIGNED addresses, the hardware services
them in a small number of wide transactions (e.g. 4 × 32-byte sectors for 128 contiguous bytes).
Scattered accesses require one transaction each — wasting most of the bandwidth.

COALESCED   : data[threadIdx.x]           → thread 0 reads byte 0, thread 1 reads byte 4, ...
UNCOALESCED : data[threadIdx.x * stride]  → each thread touches a different cache line
            : data[indices[threadIdx.x]]  → indirect / gather, inherently scattered
```
⭐ **Why it dominates:** almost all real kernels are memory-bound, not compute-bound. Modern GPUs
have far more FLOPS than bandwidth, so the arithmetic is usually free and the data movement is the
entire cost. Fixing coalescing is typically the largest single speedup available.

**Your example:** in the SSSP and points-to projects, CSR gives coalesced neighbour-list reads; the
warp-per-node assignment is precisely what makes the bit-vector words consecutive across lanes.

### (b) Warp divergence
```
if (threadIdx.x % 2 == 0)  A();  else  B();

Within a warp, BOTH paths execute serially with the inactive lanes masked off.
Cost ≈ time(A) + time(B), not max(time(A), time(B)).
```
⚠️ **Divergence only matters *within* a warp.** A branch where the whole warp takes the same path
is free. So `if (blockIdx.x % 2)` costs nothing; `if (threadIdx.x % 2)` costs double.

**Fixes:** restructure so branches align with warp boundaries; use arithmetic instead of branching
where cheap; sort or bucket work so similar items land in the same warp; compact the frontier so
only active items occupy lanes (which is what you did in both graph projects). ⭐

### (c) Occupancy
```
Occupancy = active warps per SM / maximum warps per SM

Limited by:  registers per thread × threads per block
             shared memory per block
             threads per block, and blocks per SM
```
⚠️ **Higher occupancy is NOT always better** — this is the nuance that separates a real answer from
a memorised one. Occupancy exists to *hide memory latency* by giving the scheduler other warps to
run. Past the point where latency is hidden, more occupancy buys nothing, and reducing registers to
chase occupancy can make each thread slower. A kernel with 50% occupancy and good instruction-level
parallelism often beats one at 100% with register spilling. ⭐⭐

---

## 3. Synchronisation and atomics ⭐⭐

```
__syncthreads()     : barrier across a BLOCK. ⚠️ ALL threads in the block must reach it —
                      calling it inside a divergent branch is undefined behaviour and a
                      classic deadlock bug.
__syncwarp()        : barrier within a warp (needed since Volta's independent thread scheduling)
Grid-wide sync      : NOT available in a plain kernel. Either launch a new kernel (the launch
                      boundary is the sync) or use cooperative groups. ⭐ common question
atomicAdd/Min/CAS   : read-modify-write on global or shared memory, serialised per address
```

**Reducing atomic contention — the standard pattern ⭐⭐⭐:**
```
PROBLEM : many threads atomically updating the SAME address ⇒ fully serialised

FIX (privatise then merge):
   1. Each block accumulates into a SHARED-memory copy (cheap atomics, block-local)
   2. One atomic per block merges into global memory
   ⇒ contention drops by a factor of the number of blocks

Also: warp-aggregated atomics — use __ballot_sync + __popc so one lane performs a single
      atomic for the whole warp, then __shfl_sync broadcasts the base offset back.
```
⭐ This is exactly the fix you applied in the points-to project. Name the pattern, not just the fix.

---

## 4. Warp-level primitives ⭐⭐

```
__shfl_sync(mask, var, srcLane)      : read another lane's register directly — no shared memory
__shfl_down_sync(mask, var, delta)   : the building block of a warp reduction
__ballot_sync(mask, predicate)       : 32-bit mask of which lanes satisfy the predicate
__popc(x)                            : population count — used with ballot for a prefix offset
__activemask()                       : currently active lanes
```

**Warp reduction — be able to write this:**
```cuda
for (int offset = 16; offset > 0; offset >>= 1)
    val += __shfl_down_sync(0xffffffff, val, offset);
// lane 0 now holds the sum of all 32 lanes, with no shared memory and no __syncthreads()
```

**Frontier compaction with ballot + popc ⭐** — what you used in both graph projects:
```
mask   = __ballot_sync(0xffffffff, isActive);   // which lanes have work
rank   = __popc(mask & ((1u << laneId) - 1));   // this lane's index among active lanes
base   = atomicAdd(&queueSize, __popc(mask));   // ONE atomic per warp, done by lane 0
if (isActive) queue[base + rank] = myNode;
```

---

## 5. CUDA question bank ⭐⭐⭐

<details><summary>Q: What is the difference between a block and a warp?</summary>

A **block** is a programmer-defined group of threads scheduled onto one SM, able to share memory
and synchronise with `__syncthreads()`. A **warp** is a hardware group of exactly 32 threads that
execute in lockstep (SIMT) — it is the actual scheduling and execution unit. A block is divided
into `ceil(blockDim/32)` warps.

⭐ Consequence: a block size that is not a multiple of 32 wastes lanes. A 100-thread block occupies
4 warps (128 lanes) with 28 lanes permanently idle.
</details>

<details><summary>Q: What is memory coalescing and how do you achieve it?</summary>

See §2(a). Achieve it by arranging the data so that consecutive `threadIdx.x` values touch
consecutive addresses: structure-of-arrays rather than array-of-structures, row-major access with
the fastest-varying index on `threadIdx.x`, and padding to avoid misalignment. For an
array-of-structures layout, each thread reading `arr[i].field` strides by the struct size and
wastes most of each transaction — converting to SoA is the standard fix. ⭐
</details>

<details><summary>Q: Explain shared memory bank conflicts.</summary>

Shared memory is divided into 32 banks of 4-byte words. Bank = `(address / 4) % 32`. If two threads
in a warp access **different addresses in the same bank**, the accesses serialise. If they access
the **same address**, it is broadcast (free).

Classic case: a 2-D tile `tile[32][32]` accessed column-wise — every thread hits the same bank, a
32-way conflict. **Fix: pad to `tile[32][33]`**, which shifts each row by one word so a column
spans all 32 banks. ⭐ This is the single most-asked shared-memory question.
</details>

<details><summary>Q: What is warp divergence and when does it NOT matter?</summary>

See §2(b). It does not matter when the branch condition is uniform across the warp — e.g. branching
on `blockIdx`, on a kernel parameter, or on `threadIdx.x / 32`. It also does not matter for very
short branches the compiler converts to predicated execution.
</details>

<details><summary>Q: How do you synchronise across the whole grid?</summary>

You cannot, in a standard kernel — blocks may not all be resident simultaneously, so a grid-wide
barrier can deadlock. The two options are: **end the kernel and launch another** (the launch
boundary is an implicit device-wide sync), or use **cooperative groups** (`grid_group::sync()`),
which requires launching with `cudaLaunchCooperativeKernel` and constrains the grid to a
co-resident size.

⭐ Your graph projects use the first: each round is a kernel launch, which is what makes the
frontier-based BSP structure natural.
</details>

<details><summary>Q: What is occupancy, and is more always better?</summary>

See §2(c). No — more is not always better, and saying so is the differentiator. Occupancy hides
memory latency; once latency is hidden, additional warps add nothing, and cutting registers per
thread to raise occupancy can cause spilling to local (global) memory, which is far worse.
</details>

<details><summary>Q: What is the difference between __syncthreads() and __syncwarp()?</summary>

`__syncthreads()` is a barrier plus a memory fence across the whole **block**; `__syncwarp()` is
across a **warp**. Before Volta, threads in a warp were implicitly synchronised, so warp-level code
often omitted synchronisation; Volta's independent thread scheduling broke that assumption, which
is why `__syncwarp()` and the `_sync` suffix on all warp primitives exist. ⭐
</details>

<details><summary>Q: When would you use constant memory?</summary>

For small, read-only data that **every thread in a warp reads at the same address** — filter
coefficients, normalisation constants, configuration. It is cached and broadcasts to the whole warp
in a single read. ⚠️ If threads read *different* addresses, constant memory serialises and is worse
than global.
</details>

<details><summary>Q: What is a CUDA stream and why use it?</summary>

A stream is an ordered queue of operations. Work in different streams can overlap. The main use is
overlapping H2D transfer, kernel execution and D2H transfer so the PCIe bus and the SMs are both
busy. ⚠️ Overlap requires **pinned (page-locked) host memory** — with pageable memory the driver
must stage through an internal buffer and cannot overlap. That caveat is the follow-up. ⭐
</details>

<details><summary>Q: How do you profile a CUDA kernel, and what do you look at?</summary>

`nsight-compute` (`ncu`) for kernel-level metrics and `nsight-systems` (`nsys`) for the timeline.
The metrics that matter, in order: achieved occupancy, memory throughput against the device peak
(are you bandwidth-bound?), warp execution efficiency (divergence), L1/L2 hit rates, and for
atomic-heavy kernels the atomic throughput. The first question is always **"am I compute-bound or
memory-bound?"** — a roofline view answers it, and everything else follows from the answer.
</details>

<details><summary>Q: Why is CSR the standard GPU graph format?</summary>

Three arrays: `row_offsets[V+1]`, `col_indices[E]`, `values[E]`. It is compact (`O(V+E)` rather than
`O(V²)`), neighbour lists are contiguous so reads coalesce, and the offsets allow `O(1)` lookup of
a vertex's degree and neighbour range. The weakness is that it is **static** — inserting an edge
requires rebuilding — which is exactly the difficulty in your points-to project, where complex
constraints add edges during solving. ⭐
</details>

<details><summary>Q: What is the roofline model?</summary>

A plot of achievable performance against **arithmetic intensity** (FLOPs per byte of memory
traffic). The "roof" has two parts: a sloped bandwidth-limited region and a flat compute-limited
ceiling. Where your kernel sits tells you which resource to optimise. Most graph and preprocessing
kernels have very low arithmetic intensity and sit firmly in the bandwidth-limited region — which
is why coalescing, not instruction count, is what you tune. ⭐
</details>

<details><summary>Q: Unified memory — what is it and when would you avoid it?</summary>

`cudaMallocManaged` gives a single pointer valid on host and device, with the driver migrating
pages on demand. It is excellent for prototyping and for irregular access patterns where you cannot
predict what is needed. Avoid it in a performance-critical loop where you know the access pattern —
explicit transfers plus prefetching (`cudaMemPrefetchAsync`) avoid page-fault overhead. It also
hides transfers, which makes profiling harder.
</details>

<details><summary>Q: Explain the difference between SIMD and SIMT.</summary>

SIMD applies one instruction to a fixed-width vector register; the programmer or compiler manages
the vectorisation explicitly, and divergence must be handled with masks. SIMT presents a
**per-thread programming model** — you write scalar code for one thread — and the hardware groups
threads into warps and handles divergence by masking. The practical difference: SIMT is far easier
to program for irregular workloads, at the cost of the divergence penalty being implicit rather
than visible. ⭐
</details>

<details><summary>Q: How would you optimise a naive matrix multiplication kernel?</summary>

The standard progression, and a very common question:
```
1. Naive: one thread per output element, reads a full row and column from global memory.
          Arithmetic intensity O(1) — badly memory-bound.
2. TILED: cooperatively load tiles of A and B into SHARED memory, with __syncthreads()
          between load and compute. Each global element is now reused by the whole tile,
          raising arithmetic intensity by the tile width.  ⭐ the key step
3. Register tiling: each thread computes a small block of outputs (e.g. 4×4) so operands
          are reused from registers.
4. Avoid bank conflicts with padding; use vectorised loads (float4).
5. In production: use cuBLAS. It will beat hand-written code and you should say so.
```
</details>

---

## 6. Parallel computing concepts beyond CUDA ⭐⭐

Expect these in any systems interview, GPU or not.

```
AMDAHL'S LAW    : speedup ≤ 1 / (s + (1−s)/p),  s = serial fraction
                  With s = 5% and infinite processors, the ceiling is 20×.  ⭐
GUSTAFSON'S LAW : the counter-argument — in practice you scale the PROBLEM with the machine,
                  so the serial fraction shrinks as N grows. Both are worth naming.
STRONG SCALING  : fixed problem, more processors → limited by Amdahl
WEAK SCALING    : problem grows with processors → more achievable
RACE CONDITION  : outcome depends on interleaving of concurrent accesses
DEADLOCK        : the four Coffman conditions (see ../../02_Core_CS/)
FALSE SHARING   : two cores write different variables on the SAME cache line ⇒ the line
                  ping-pongs between caches. Fix: pad to a cache line. ⭐ classic CPU question
MEMORY MODELS   : sequential consistency vs relaxed; why you need fences/atomics
BSP             : bulk-synchronous parallel — compute, communicate, barrier. This is exactly
                  the structure of your frontier-based graph kernels. ⭐
```

---

## 7. Questions *you* should be ready to be asked about *your* projects ⭐⭐⭐

These are near-certain given your resume. Full answers are in `../02_Project_Deep_Dives/`.

```
□ "Why is a GPU a good/bad fit for points-to analysis?"        → 01-Points_To §9
□ "Why warp-per-node rather than thread-per-node?"             → 01-Points_To §6
□ "How did you fix the atomic contention?"                     → privatise then merge, §3 above
□ "Why does Δ-stepping parallelise when Dijkstra doesn't?"     → 02-Parallel_SSSP §7
□ "Why is atomicMin safe without a lock?"                      → commutative + associative ⇒
                                                                  order-independent
□ "Why CSR?"                                                   → §5 above
□ "What did Nsight Compute tell you?"                          → be specific, not vague ⚠️
□ "What's the bottleneck in your kernel — compute or memory?"  → memory, and know why
```

⚠️ **"What did the profiler tell you?" is the question that separates people who profiled from
people who ran the profiler.** Have a specific metric and a specific number, or say honestly what
you looked at and what you concluded.

---

## 8. The 30-second refresh before a systems interview

```
□ Warp = 32 threads, the unit of execution. Block = scheduled on one SM, has shared memory.
□ Coalescing: consecutive threads → consecutive addresses. Almost all kernels are memory-bound.
□ Divergence: both paths run serially, but only WITHIN a warp.
□ Occupancy hides latency; more is not always better (register spilling).
□ Bank conflicts: 32 banks; pad [32][33] for column access.
□ No grid-wide sync — relaunch the kernel or use cooperative groups.
□ Atomic contention → privatise in shared memory, one merge per block; or warp-aggregate.
□ ballot + popc = frontier compaction.
□ Amdahl's law; false sharing; BSP.
```
