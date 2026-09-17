# Computer Architecture — Concepts

## 1. Core idea in 3 lines
Architecture is about the gap between what a program asks for and what silicon can deliver: instructions are executed in stages so several can overlap (pipelining), and data is cached because memory is a hundred times slower than the CPU. For software roles the payoff is understanding *why* cache-friendly code is faster and *why* branches are expensive. For OAs it is three formula families: pipeline speedup, cache arithmetic and Amdahl's law.

---

## 2. Number representation

**Unsigned n bits:** range 0 … 2ⁿ − 1.
**Two's complement n bits:** range −2ⁿ⁻¹ … 2ⁿ⁻¹ − 1. Negate by inverting all bits and adding 1; the most significant bit is the sign. It is used universally because addition and subtraction need no special cases and there is exactly one representation of zero.

**IEEE 754 single precision (32 bits):** 1 sign bit, **8 exponent bits with bias 127**, 23 mantissa bits. The value is `(−1)^s × 1.mantissa × 2^(exp − 127)`, where the leading 1 is implicit and therefore not stored. Double precision is 1 / 11 (bias 1023) / 52.

Special values: exponent all zeros → denormals and zero; exponent all ones → infinity (mantissa 0) or NaN (mantissa non-zero). This is why `0.1 + 0.2 != 0.3` — neither operand is exactly representable in binary.

---

## 3. Instruction set architecture

**RISC vs CISC**

| | RISC | CISC |
|---|---|---|
| Instructions | few, fixed length, simple | many, variable length, complex |
| Memory access | load/store only | most instructions may touch memory |
| Registers | many | fewer |
| Cycles per instruction | ~1, uniform | variable |
| Complexity is in | the compiler | the hardware/microcode |
| Examples | ARM, RISC-V, MIPS | x86 |

Modern x86 chips decode CISC instructions into RISC-like micro-operations internally, so the distinction is now mostly about the *interface*, not the implementation.

**Addressing modes:** immediate, register, direct, indirect, register indirect, indexed, base + displacement, PC-relative.

**The instruction cycle:** fetch → decode → execute → memory access → write back.

---

## 4. Pipelining

Splitting the instruction cycle into k stages lets k instructions be in flight simultaneously. The clock period becomes the **slowest** stage plus latch overhead, so an unbalanced pipeline wastes capacity.

**The formulas:**
```
Non-pipelined time for n instructions = n × k × Tc
Pipelined time                        = (k + n − 1) × Tc
Speedup  S = n·k / (k + n − 1)
Maximum speedup (n → ∞) = k
```

**Hazards — the reason real speedup falls short of k:**

| Hazard | Cause | Fixes |
|---|---|---|
| **Structural** | two instructions need the same hardware unit | duplicate the unit; separate instruction and data caches |
| **Data** | an instruction needs a result not yet written back | **forwarding/bypassing**, stalls, compiler reordering |
| **Control** | a branch's target is unknown until it resolves | branch prediction, delayed branch, speculative execution |

**Forwarding** routes an ALU result straight to the next instruction's input rather than waiting for write-back, eliminating most data hazards. The one it cannot eliminate is a **load-use hazard** — a load's value is not available until the memory stage, so at least one stall bubble is unavoidable.

**Branch prediction** matters enormously: a mispredict flushes the pipeline, costing roughly the pipeline depth in cycles. This is why sorting an array before branching on its values can make a loop dramatically faster — the branch becomes predictable. It is a great concrete answer when asked "why is cache/branch behaviour worth knowing as a software engineer".

**Data-hazard types:** RAW (read after write) is the true dependency and the only one that matters in a simple in-order pipeline; WAR and WAW are name dependencies that appear with out-of-order execution and are removed by register renaming.

---

## 5. Cache and the memory hierarchy

**Locality** is the whole justification: **temporal** (a recently used item will likely be used again) and **spatial** (neighbours of a used item will likely be used). Caches exploit both — temporal by retaining, spatial by fetching whole blocks.

**Address decomposition:**
```
| tag | index | block offset |

block offset bits = log2(block size in bytes)
index bits        = log2(number of SETS)
tag bits          = address bits − index − offset

number of sets = cache size / (block size × associativity)
```

**Mapping:**
- **Direct-mapped** — one possible location; simple and fast, but conflict misses are common.
- **Fully associative** — any location; no conflict misses, but comparison is expensive.
- **n-way set associative** — the practical compromise; n candidate lines per set.

**The three Cs of misses:** **compulsory** (first access — unavoidable, reduced by prefetching and bigger blocks), **capacity** (working set exceeds the cache), **conflict** (too many blocks map to the same set — reduced by higher associativity).

**Write policies:** write-through (write to cache and memory; simple, consistent, more traffic) versus **write-back** (write to cache, mark dirty, flush on eviction; less traffic, more complex). On a write miss: **write-allocate** (fetch the block first, pairs naturally with write-back) or write-around.

**AMAT** — the one cache formula to memorise:
```
AMAT = hit time + miss rate × miss penalty

Two-level:
AMAT = Hit_L1 + MissRate_L1 × (Hit_L2 + MissRate_L2 × Penalty_memory)
```

**Practical consequences worth saying in an interview:** row-major traversal of a 2-D array is far faster than column-major in C/C++ because it follows spatial locality; structure-of-arrays beats array-of-structures when you touch one field of many elements; and **false sharing** — two threads writing different variables that share a cache line — silently destroys multithreaded scaling.

---

## 6. Parallelism and Amdahl's law

```
Speedup = 1 / ( (1 − P) + P/N )

P = the parallelisable fraction, N = the number of processors
Maximum speedup as N → ∞  =  1 / (1 − P)
```

If 40% of a program is parallelisable, no amount of hardware gets past 1.67×. This is the argument against assuming that adding cores fixes performance, and it applies equally to system design — optimise the serial bottleneck first.

**Gustafson's law** is the optimistic counterpart: as problem sizes grow, the parallel portion often grows with them, so speedup scales better than Amdahl suggests for scalable workloads.

**Levels of parallelism:** instruction-level (pipelining, superscalar, out-of-order), data-level (SIMD/vector), thread-level (multicore), request-level (distributed).

**Flynn's taxonomy:** SISD, SIMD (GPUs, vector units), MISD (essentially unused), MIMD (multicore, clusters).

---

## 7. Recall questions

1. Give the IEEE 754 single-precision layout and the bias.
2. Why does `0.1 + 0.2 != 0.3`?
3. Give the pipeline speedup formula and its limit.
4. Name the three hazard types and one fix for each.
5. Which data hazard cannot be removed by forwarding, and why?
6. Give the address decomposition formulas for a set-associative cache.
7. Name the three Cs of cache misses and how each is reduced.
8. Write-through vs write-back — one advantage each.
9. Give AMAT for one level and for two levels.
10. State Amdahl's law and its limiting value.
11. Why is row-major traversal faster than column-major in C?
12. What is false sharing?
