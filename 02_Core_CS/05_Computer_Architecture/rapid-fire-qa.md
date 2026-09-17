# Computer Architecture — Rapid Fire Q&A

**1. RISC vs CISC?** RISC has few fixed-length instructions with a load/store model and many registers, pushing complexity into the compiler. CISC has many variable-length instructions that can touch memory directly. Modern x86 decodes CISC into RISC-like micro-ops internally.

**2. What is pipelining and what limits it?** Overlapping instruction stages so k instructions are in flight at once. The clock is set by the slowest stage, and hazards plus the pipeline fill keep real speedup below the ideal k.

**3. Name the three hazards and their fixes.** Structural — duplicate the hardware. Data — forwarding, with a stall for load-use. Control — branch prediction and speculation.

**4. Which hazard does forwarding not fix?** Load-use. The loaded value is only ready after the memory stage, which is later than the consumer needs it, so one bubble is unavoidable.

**5. What does a branch mispredict cost?** Roughly the pipeline depth in cycles, because every speculatively fetched instruction is flushed. On a deep pipeline that is fifteen or twenty cycles.

**6. Why is cache-friendly code faster, concretely?** A 64-byte line holds sixteen 4-byte ints, so row-major traversal takes one miss per sixteen elements while column-major misses on every access. A sixteen-fold difference in miss count is a real, measurable slowdown.

**7. What are the three Cs?** Compulsory (first touch), capacity (working set too big), conflict (too many blocks mapping to one set). Associativity fixes conflict, a bigger cache fixes capacity, prefetching fixes compulsory.

**8. Give the AMAT formula.** Hit time plus miss rate times miss penalty; nested for multiple levels. It shows that misses, not hits, dominate average latency.

**9. Direct-mapped vs set-associative vs fully associative?** Direct-mapped is fastest to look up but suffers conflict misses; fully associative eliminates them but must compare every line; n-way is the practical compromise.

**10. Write-through vs write-back?** Write-through keeps memory always current at the cost of traffic; write-back defers the write until eviction, needing a dirty bit and complicating coherence.

**11. What is false sharing?** Two threads writing different variables that happen to share a cache line, so coherence traffic ping-pongs the line between cores. Padding the variables apart fixes it.

**12. State Amdahl's law and what it implies.** Speedup is `1/((1−P) + P/N)`, bounded by `1/(1−P)`. It means the serial fraction is a hard ceiling — restructure the algorithm before adding processors.

**13. Why does `0.1 + 0.2 != 0.3`?** Neither value is exactly representable in binary floating point, so each is rounded, and the rounded sum differs from the rounded 0.3. Compare with a tolerance instead.

**14. What is the IEEE 754 single-precision layout?** One sign bit, eight exponent bits with bias 127, twenty-three mantissa bits, with the leading 1 implicit.

**15. What is register renaming for?** It removes WAR and WAW name dependencies so an out-of-order processor can execute independent instructions in parallel; only true RAW dependencies remain.

**16. What is SIMD and where do you meet it?** One instruction operating on a vector of data — SSE/AVX on CPUs, and the entire GPU programming model. It is why numpy is orders of magnitude faster than a Python loop.

**17. How does this matter in your own code?** *(Have a concrete example: a loop reordered for locality, a numpy vectorisation that replaced a Python loop, or a branch you removed from an inner loop.)*
