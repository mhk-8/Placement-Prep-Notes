
# NVIDIA — Prep Plan, Question Bank and Debriefs

---

## 1. Topic priorities ⭐⭐

| Priority | Topic | Source | Status |
|---|---|---|---|
| **P1** | CUDA execution model, coalescing, divergence, occupancy, atomics | `../../07_Interviews/01_Technical_Round_Prep/01-GPU_and_Parallel_Computing_QA.md` | ☐ |
| **P1** | C++ depth — RAII, smart pointers, move semantics, virtual destructors, UB | `../../07_Interviews/01_Technical_Round_Prep/03-CS_Fundamentals_Rapid_Fire.md` §6 | ☐ |
| **P1** | Your three GPU projects, rehearsed to 5-minute depth | `../../07_Interviews/02_Project_Deep_Dives/` | ☐ |
| **P1** | Computer architecture — caches, bandwidth, false sharing, SIMD vs SIMT | `../../02_Core_CS/05_Computer_Architecture/` | ☐ |
| **P2** | DSA Medium — arrays, strings, graphs, DP ⚠️ **do not neglect this** | `../../01_DSA/` | ☐ |
| **P2** | Compiler fundamentals — phases, SSA, dataflow, register allocation | `../../07_Interviews/01_Technical_Round_Prep/03-CS_Fundamentals_Rapid_Fire.md` §2 | ☐ |
| **P2** | Tiled matrix multiplication, optimised step by step | GPU Q&A §5 | ☐ |
| **P3** | OS — virtual memory, processes, driver basics | `../../02_Core_CS/01_Operating_Systems/` | ☐ |
| **P3** | Numerical precision — fp32/fp16/bf16/tf32, FMA, non-associativity | `../../05_AI_ML/04_Deep_Learning/03-training-and-optimization.md` | ☐ |

⚠️ **The counter-intuitive priority:** your weakest link here is the **DSA round**, not the GPU
round. Do not over-prepare your strength and get eliminated before you reach it.

---

## 2. The two-week plan ⭐⭐⭐

| Date | Task | Done |
|---|---|---|
| D-14 | Run the research protocol; overwrite the volatile table; confirm role titles | ☐ |
| D-13 | GPU Q&A file end to end; answer all 14 questions aloud | ☐ |
| D-12 | Points-to project: 5-minute version aloud; draw the architecture from memory | ☐ |
| D-11 | C++ rapid-fire: 30 questions aloud, timed at 45 s each | ☐ |
| D-10 | DSA: 3 Medium problems, timed. Graphs focus | ☐ |
| D-9 | Architecture: caches, bandwidth, false sharing, pipelining, SIMT | ☐ |
| D-8 | SSSP project: 5-minute version; be able to derive why Dijkstra doesn't parallelise | ☐ |
| D-7 | Tiled matmul optimisation, written out from scratch | ☐ |
| D-6 | DSA: 3 Medium problems. DP focus ⚠️ | ☐ |
| D-5 | Compiler fundamentals; the Andersen vs Steensgaard comparison | ☐ |
| D-4 | **Mock: project deep dive with someone who is not a compiler person** ⭐ | ☐ |
| D-3 | **Mock: C++ / CUDA rapid-fire** | ☐ |
| D-2 | NVIDIA Developer Blog — read 2 posts; write down 2 questions to ask | ☐ |
| D-1 | Pre-interview routine only. Re-read §3 and §4 of the process file. Nothing new ⚠️ | ☐ |

---

## 3. Seeded question bank

Reported patterns for NVIDIA-style interviews. **Verify and extend from your own sources.**

### Coding (Medium, C/C++ preferred)
```
- Array and string manipulation with an in-place or O(1)-space constraint
- Linked list operations (reverse in k-groups, cycle detection, merge)
- Matrix problems — rotate, spiral, transpose in place
- Graph BFS/DFS, topological sort, connected components
- Bit manipulation — count set bits, single number, swap without temp
- Simple DP — LIS, coin change, grid paths
- "Now parallelise it" as a follow-up  ⭐⭐ your advantage — have a real answer
```

### C / C++ (expect depth)
```
- Pointer arithmetic; sizeof on arrays vs pointers; struct padding and alignment
- Virtual destructors, object slicing, vtable mechanics
- RAII; unique_ptr vs shared_ptr; move semantics and what std::move actually does
- const correctness; static; storage classes
- Memory leaks, dangling pointers, double free
- Undefined behaviour — and knowing that it IS undefined  ⭐
- Why vector often beats list (cache locality)
- Output-prediction snippets
```

### Architecture and systems
```
- Explain the memory hierarchy and typical latencies
- What is a cache line? What is false sharing and how do you fix it?  ⭐
- Pipelining and the three hazard types
- SIMD vs SIMT
- Virtual memory, TLB, page faults
- Endianness; alignment; volatile vs atomic
```

### CUDA ⭐⭐⭐ (your round — expect real depth)
```
- Block vs warp vs thread; how blocks map to SMs
- Memory coalescing: what, why it dominates, how to achieve it
- Warp divergence: cost model, and when it does NOT matter
- Occupancy: definition, limiters, and why more is not always better
- Shared memory bank conflicts and the [32][33] padding fix
- How to synchronise across a grid (you cannot — relaunch or cooperative groups)
- Reducing atomic contention: privatise then merge; warp-aggregated atomics
- Warp primitives: shuffle, ballot, popc; write a warp reduction
- Why CSR for graphs, and its weakness
- Optimise a naive matmul, step by step
- Streams, pinned memory, and why overlap needs both
- Unified memory: when to use it and when not to
- The roofline model
```

### Project deep dive (near-certain)
```
- "Explain points-to analysis to me as if I've never seen a compiler."  ⭐⭐⭐
- "Why is a GPU a bad fit for this, and why did you do it anyway?"
- "Why warp-per-node rather than thread-per-node?"
- "Walk me through the atomic contention problem and your fix."
- "What did Nsight actually tell you?" ⚠️ have a specific metric
- "How do you know the analysis is correct?"
- "What's your contribution versus the PInter paper's?" ⚠️
- "Why doesn't Dijkstra parallelise? Why does Δ-stepping?"
- "What's the memory bound on your Near/Far worklist, and why does it matter?"
```

### Behavioural
```
- Why NVIDIA? Why hardware-adjacent work rather than application software?
- Tell me about the hardest performance problem you've debugged
- Why the Mechanical → CS switch?
- A time you had to understand something deeply before you could start
```

---

## 4. Questions I collected myself ⭐⭐⭐

| Date | Source | Round | Question | Topic | Notes |
|---|---|---|---|---|---|
| | | | | | |
| | | | | | |
| | | | | | |
| | | | | | |

---

## 5. Debriefs ⭐⭐⭐

Full template: `../../07_Interviews/06_Post_Interview_Debriefs/Debrief_Template.md`

### Round 1 — `<date>` — `<type>`
```
INTERVIEWER        :
STRUCTURE / TIMING :
QUESTIONS ASKED (verbatim):
  1.
  2.
  3.
CODING PROBLEM     :
  My approach     :
  Correct approach (write it out NOW):
WHAT I FUMBLED, AND THE RIGHT ANSWER:
  1.
  2.
TONE / STYLE FOR THE NEXT ROUND:
OUTCOME            :
THREE ACTIONS:
  1.
  2.
  3.
```

### Round 2 — `<date>` — `<type>`
```
(same structure)
```

### Round 3 — `<date>` — `<type>`
```
(same structure)
```

---

## 6. Corrections to make to `00-process-and-oa.md`

```
□
□
```

---

## 7. The 30-second pre-interview refresh ⭐

```
□ Warp = 32 threads, the unit of execution; block → one SM, has shared memory
□ Coalescing: consecutive threads → consecutive addresses. Almost everything is memory-bound
□ Divergence: both paths serialise, only WITHIN a warp
□ Occupancy hides latency; more is not always better (register spilling)
□ Bank conflicts: 32 banks; pad [32][33]
□ No grid-wide sync — relaunch or cooperative groups
□ Atomic contention → privatise in shared memory, one merge per block
□ My opening sentence: "irregular, data-dependent workload where the graph rewrites itself"
□ Attribution: PInter's 96% is theirs; the GPU mapping is mine
```
