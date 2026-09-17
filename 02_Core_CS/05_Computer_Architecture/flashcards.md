# Computer Architecture — Flashcards

## Questions

1. Give the pipeline speedup formula and its limit as n grows.
2. Why does a real pipeline never reach k× speedup?
3. Name the three hazard types with one fix each.
4. Which hazard cannot be forwarded away, and why?
5. What does a branch mispredict cost?
6. Give the three cache address-decomposition formulas.
7. 16 KB, 32-byte blocks, direct mapped, 32-bit address — give the split.
8. What changes in that split at 4-way associativity, and why?
9. Name the three Cs and the fix for each.
10. Give AMAT for one and two levels.
11. Write-through vs write-back — one advantage each.
12. State Amdahl's law and its limit.
13. What P is needed for a 4× maximum speedup?
14. Give the IEEE 754 single- and double-precision layouts with biases.
15. Why does row-major traversal beat column-major in C, with numbers?
16. What is false sharing and how is it fixed?
17. What is register renaming for?

---

## Answers

1. `S = n·k / (k + n − 1)`, approaching **k** as n → ∞.
2. The pipeline must fill (`k − 1` extra cycles) and hazards insert stalls; an unbalanced pipeline also wastes the difference between the slowest stage and the others.
3. Structural — duplicate the unit or split instruction and data caches. Data — forwarding (plus a stall for load-use). Control — branch prediction and speculation.
4. Load-use. The value is only available after the memory stage, which is later than the consumer's execute stage, so at least one bubble is required.
5. Approximately the pipeline depth in cycles, since all speculatively fetched instructions are flushed.
6. `offset = log₂(block size)`; `sets = cache size / (block size × associativity)`; `index = log₂(sets)`; `tag = address bits − index − offset`.
7. Offset 5, index 9, tag 18.
8. Sets drop from 512 to 128, so index becomes 7 and tag becomes 20. Associativity moves bits from the index into the tag.
9. Compulsory — prefetching or larger blocks. Capacity — a larger cache. Conflict — higher associativity (zero in a fully associative cache).
10. `AMAT = hit + miss rate × penalty`; two-level: `Hit_L1 + MR_L1 × (Hit_L2 + MR_L2 × Penalty_mem)`.
11. Write-through keeps memory authoritative and simplifies coherence; write-back cuts memory traffic sharply for repeated writes.
12. `Speedup = 1/((1 − P) + P/N)`, with limit `1/(1 − P)`.
13. `1/(1 − P) ≥ 4` ⇒ **P ≥ 0.75**.
14. Single: 1 sign, 8 exponent (bias 127), 23 mantissa. Double: 1, 11 (bias 1023), 52. The leading mantissa 1 is implicit in both.
15. C stores arrays row-major, so consecutive row elements share a cache line — with 64-byte lines and 4-byte ints that is one miss per 16 elements, against one miss per element going down a column: a 16× difference.
16. Two threads writing distinct variables that share a cache line, causing the line to bounce between cores under the coherence protocol. Pad or align the variables onto separate lines.
17. Eliminating WAR and WAW name dependencies so that out-of-order execution is limited only by true RAW dependencies.
