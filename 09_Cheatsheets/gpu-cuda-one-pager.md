
# GPU, CUDA and Parallel Computing — One Pager

> **Use:** NVIDIA, Qualcomm, Samsung R&D, and any ML-infrastructure role. ⭐
> **This sheet is your differentiator.** Almost no campus candidate can answer these questions,
> and your SSSP and points-to-analysis projects are direct evidence. The failure mode is not
> knowing this material — it is failing to *articulate* it crisply. Read it aloud.

---

## 1. The execution model ⭐⭐⭐

```
grid  →  blocks (cooperative thread array)  →  warps (32 threads)  →  threads
                                               ↑ the real unit of execution ⭐

Thread  : own registers, own program counter (Volta+ independent thread scheduling)
Warp    : 32 threads issued together in lockstep (SIMT)
Block   : scheduled on ONE SM; threads can use shared memory and __syncthreads()
Grid    : all blocks of a kernel launch. Blocks are INDEPENDENT and may run in any order ⚠️
SM      : streaming multiprocessor — warp schedulers, register file, shared memory, L1
```

```cuda
int tid = blockIdx.x * blockDim.x + threadIdx.x;      // the global index, every time
if (tid < n) { ... }                                  // ⚠️ ALWAYS bound-check: grid is padded
kernel<<<gridDim, blockDim, sharedBytes, stream>>>(args);
int blocks = (n + threads - 1) / threads;             // ceiling division
```

> ⭐ **Say "warp", not "thread", when discussing divergence, coalescing and occupancy.** It is
> the single clearest signal that you have actually written CUDA rather than read about it.

---

## 2. The memory hierarchy ⭐⭐⭐

| Space | Scope | Latency | Size | Managed by |
|---|---|---|---|---|
| **Registers** | thread | ~1 cycle | 64 K 32-bit per SM | compiler ⚠️ spills to local |
| **Shared memory** | block | ~20-30 cycles | 48-228 KB/SM | **you** ⭐ |
| L1 / texture cache | SM | ~30 cycles | shared budget with smem | hardware |
| L2 cache | device | ~200 cycles | MBs | hardware |
| **Global memory** | device | **300-600 cycles** ⚠️ | GBs | you (`cudaMalloc`) |
| Constant memory | device, read-only | cached, broadcast | 64 KB | you |
| Local memory | thread | global-speed ⚠️ | — | compiler (register spill) |
| Host memory | CPU | PCIe ~32 GB/s ⚠️ | — | you |

```
Global memory latency is NOT avoided — it is HIDDEN by having enough concurrent warps. ⭐
That is what occupancy is for.
```

---

## 3. The four performance rules ⭐⭐⭐

### (a) Coalescing
```
A warp's 32 accesses are served in 32B/64B/128B transactions. If consecutive threads touch
consecutive addresses, one transaction serves the warp. If they stride, you pay up to 32
transactions for the same data. ⭐

GOOD : a[tid]                    (stride 1, aligned)   → 1-4 transactions
BAD  : a[tid * 32]               (stride 32)           → 32 transactions ⚠️
FIX  : Structure-of-Arrays instead of Array-of-Structures; pad rows; transpose through
       shared memory.
```
> **The CPU analogy to give:** this is cache-line utilisation. You pay for a wide transaction
> either way, so you want every byte you fetch to be used.

### (b) Warp divergence
```
Threads in a warp that take different branches are SERIALISED — both paths execute with the
inactive lanes masked off. Cost ≈ sum of both paths. ⚠️

BAD  : if (tid % 2) ...          → every warp diverges
GOOD : if (tid / 32 % 2) ...     → divergence at warp granularity = no divergence ⭐
Also : data-dependent loop trip counts cause the whole warp to wait for its slowest lane
       (load imbalance within a warp).
```

### (c) Occupancy
```
Occupancy = active warps per SM / maximum warps per SM.
LIMITED BY: registers per thread · shared memory per block · block size · block-slots per SM.
Higher occupancy → more warps to hide memory latency.

⚠️ Higher occupancy is NOT automatically faster. A kernel with enough ILP and few memory
stalls can run best at 30-50% occupancy. Saying this is a senior-level answer. ⭐
Tools: --ptxas-options=-v for register counts, the occupancy calculator,
       cudaOccupancyMaxActiveBlocksPerMultiprocessor.
```

### (d) Bank conflicts
```
Shared memory has 32 banks of 4 bytes. If two threads in a warp hit DIFFERENT addresses in the
SAME bank, the accesses serialise (n-way conflict). Same address = broadcast, free. ⭐
Classic case: __shared__ float t[32][32]; t[threadIdx.x][0] → all 32 threads hit bank 0.
FIX: pad to [32][33] — shifts each row by one bank. ⭐ know this fix by heart
```

---

## 4. Synchronisation and atomics ⭐⭐

```cuda
__syncthreads()                 // barrier WITHIN a block. ⚠️ all threads must reach it —
                                // calling it inside a divergent branch is undefined
__syncwarp(mask)                // warp-level barrier (Volta+ independent scheduling)
__threadfence()                 // memory ordering
atomicAdd / atomicCAS / atomicMin / atomicExch   // serialise contending threads ⚠️
cudaDeviceSynchronize()         // host waits for the device

NO GLOBAL BARRIER inside a kernel. ⭐ To synchronise across blocks you launch another kernel
(or use cooperative groups / grid.sync() where supported). This is a frequent question and the
reason iterative graph algorithms are structured as a loop of kernel launches. ⚠️
```

### Reducing atomic contention ⭐
```
1. Reduce within the warp with __shfl_down_sync (no memory traffic at all)
2. Then one atomic per warp into shared memory
3. Then one atomic per block into global memory
→ 32× to 1024× fewer global atomics. This is the standard pattern and exactly what a
  GPU worklist/frontier needs. ⭐
```

### Warp primitives (worth naming — few candidates can)
```cuda
__shfl_sync, __shfl_down_sync, __shfl_xor_sync    // register-to-register exchange within a warp
__ballot_sync        // 32-bit mask of which lanes satisfy a predicate
__activemask, __popc // active lanes; population count → warp-aggregated atomics ⭐
__all_sync / __any_sync
```

---

## 5. The canonical kernel patterns ⭐⭐

### Reduction (sum)
```
Tree reduction in shared memory, stride halving:
for (int s = blockDim.x/2; s > 0; s >>= 1) { if (tid < s) sdata[tid] += sdata[tid+s];
                                             __syncthreads(); }
⚠️ The naive version (stride doubling with tid % (2*s)) diverges and has bank conflicts.
Being able to state WHY the strided-halving version is better is the whole question. ⭐
Final warp: unroll with __shfl_down_sync and drop the barriers.
```

### Tiled matrix multiply
```
Load a TILE of A and B into shared memory, __syncthreads(), compute partial products,
__syncthreads(), advance the tile. Reduces global traffic by a factor of the tile width ⭐
→ this is the textbook demonstration of arithmetic intensity.
```

### Scan / prefix sum
```
Hillis-Steele (work-inefficient, O(n log n) work) vs Blelloch (work-efficient, O(n) work,
up-sweep + down-sweep). ⭐ Scan is the primitive behind stream compaction and building
frontiers/worklists.
```

### Sparse matrix-vector (CSR) — ⭐ your projects
```
row_ptr[V+1], col_idx[E], (val[E]). Thread-per-row is simple but load-imbalanced when degrees
are skewed ⚠️; warp-per-row or merge-based balancing fixes it. CSR is coalesced along col_idx
and is why graph work on GPUs uses it. ⭐
```

### Graph traversal / frontier
```
Level-synchronous BFS: one kernel per level, frontier in global memory, atomics to append.
Push vs pull (direction-optimising BFS) depending on frontier size ⭐
Δ-stepping for SSSP: buckets of distance range Δ — more parallelism than Dijkstra's strict
priority order while doing less redundant work than Bellman-Ford ⭐ your project's core idea
Worklist / fixpoint iteration: iterate kernel launches until no node changes (a converged flag)
→ this is exactly the structure of your points-to analysis.
```

---

## 6. Streams, transfers and the CPU-GPU boundary ⭐

```
PCIe is the bottleneck (~32 GB/s vs 1-3 TB/s on-device). Minimise transfers; keep data resident ⚠️
cudaMemcpyAsync + pinned (page-locked) host memory → overlaps with compute
STREAMS: independent command queues → overlap H2D copy / kernel / D2H copy ⭐
Unified memory (cudaMallocManaged): convenience, with page-fault migration cost
Events (cudaEventRecord/Elapsed) for timing — ⚠️ never time a kernel without synchronising
```

---

## 7. Analysis and tooling ⭐

```
ROOFLINE : attainable FLOP/s = min(peak compute, arithmetic intensity × memory bandwidth)
           AI = FLOPs / bytes moved. Compute the AI of your kernel, place it on the roof,
           and you know whether to optimise math or memory. ⭐ Use this phrasing.
EFFECTIVE BANDWIDTH = (bytes read + bytes written) / time. Compare with the device peak ⭐
→ "My kernel achieved 780 GB/s of a 900 GB/s peak, so it is memory-bound and near-optimal"
  is the sentence that wins a GPU interview. Have your own real numbers ready.

Nsight Compute  : per-kernel counters — occupancy, memory throughput, warp stall reasons ⭐
Nsight Systems  : timeline — transfer/compute overlap, gaps, CPU-side bottlenecks
nvprof (legacy) · cuda-memcheck / compute-sanitizer for races and OOB
cudaGetLastError() after every launch ⚠️ launches fail asynchronously and silently
```

📐 **Amdahl:** `S = 1/(s + p/N)` — serial fraction s caps speedup at 1/s.
📐 **Gustafson:** `S = s + p·N` — scaled speedup when the problem grows with the machine.
📐 **Little's law for latency hiding:** concurrency needed = latency × throughput.
> ⭐ These three are the theory questions that accompany the practical ones.

---

## 8. Likely interview questions and the one-line answers ⭐⭐⭐

| Question | Answer |
|---|---|
| What is a warp? | 32 threads issued together; the hardware's scheduling unit |
| Coalescing? | Consecutive threads → consecutive addresses → one wide transaction per warp |
| Warp divergence? | Divergent branches in a warp serialise with lane masking; cost is the sum of paths |
| Occupancy, and is more better? | Active warps / max warps; it hides latency, but past ~50% the returns vanish and register pressure can make it counterproductive ⭐ |
| Shared memory vs L1? | Same physical SRAM, partitioned; shared is explicitly managed, L1 is automatic |
| Bank conflict and fix? | Same bank, different addresses, within a warp → serialised. Pad `[32][33]` |
| Global barrier in a kernel? | Does not exist — launch another kernel, or cooperative groups ⭐ |
| Reduce atomic contention? | Warp shuffle → shared-memory atomic → one global atomic per block |
| `__syncthreads()` in a divergent branch? | Undefined behaviour — all threads of the block must reach it |
| Memory-bound or compute-bound? | Compute arithmetic intensity, compare with machine balance (roofline) |
| SIMT vs SIMD? | SIMT has per-thread state and divergence handling; SIMD is one instruction over a fixed vector |
| Why is PCIe a problem? | 32 GB/s vs 1-3 TB/s on-device; keep data resident, overlap with streams |
| Tensor cores? | Fixed-function matrix-multiply-accumulate units for mixed precision (FP16/BF16/TF32 in, FP32 accumulate) |
| CUDA vs OpenCL vs HIP vs SYCL? | Vendor-specific and mature vs portable; HIP/SYCL for portability |
| Why is your graph kernel slow? | Irregular access (poor coalescing), load imbalance across degrees, atomic contention on the frontier, and low arithmetic intensity ⭐ |

---

## 9. Your own project soundbites ⭐⭐⭐ (fill in the real numbers)

```
POINTS-TO ANALYSIS (Andersen-style, GPU)
  Opening line: "An irregular, data-dependent workload where the graph rewrites itself during
  traversal — new edges appear as the analysis discovers new points-to facts."
  Then: worklist/fixpoint structure · CSR with dynamic insertion · the rule that made it
  parallel · validated exactly against Soot · speedup ______ × on ______ benchmarks.

PARALLEL SSSP (Δ-stepping)
  Why Δ-stepping: Dijkstra's strict priority order has almost no parallelism; Bellman-Ford has
  plenty but does redundant work. Δ buckets trade a little redundancy for a lot of parallelism. ⭐
  Then: bucket management · frontier via atomics · effective bandwidth ______ GB/s of ______ peak.

IMAGE PREPROCESSING PIPELINE
  Memory-layout reasoning (NCHW vs NHWC), the align-corners subtlety, throughput ______ img/s.

Have ONE number per project you can state without hesitating. A GPU claim with no measured
number is not a claim. ⭐
```

---

## Recall questions
1. What is a warp, and why does it matter for both divergence and coalescing?
2. Why is high occupancy not always better?
3. How do you synchronise across blocks inside a kernel?
4. Give the three-level pattern for reducing global atomic contention.
5. `__shared__ float t[32][32]; t[threadIdx.x][0]` — what is wrong and what is the fix?
6. Define arithmetic intensity and state the roofline model.
7. Why does Δ-stepping parallelise better than Dijkstra?
8. State Amdahl's law and what Gustafson's adds to it.
