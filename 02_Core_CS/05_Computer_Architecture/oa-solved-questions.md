# Computer Architecture — Solved OA Questions

> 15 questions in OA style. Architecture MCQs cluster tightly around pipelining, cache arithmetic and Amdahl, so this set covers most of what you will actually meet.

---

**Q1.** A 5-stage pipeline with a 2 ns clock executes 100 instructions. The speedup over a non-pipelined implementation is
(a) 5.00  (b) 4.81  (c) 4.00  (d) 2.50

<details><summary>Answer</summary>

**(b) 4.81.**

```
Non-pipelined = 100 × 5 × 2 = 1000 ns
Pipelined     = (5 + 100 − 1) × 2 = 208 ns
Speedup       = 1000/208 = 4.81
```
(a) is the *maximum* speedup, approached only as n → ∞. The gap is the `k − 1` pipeline fill.
</details>

---

**Q2.** Which hazard **cannot** be eliminated by forwarding?
(a) RAW on an ALU result  (b) Load-use  (c) WAR  (d) Structural

<details><summary>Answer</summary>

**(b) load-use.**

A load's value is not available until the end of the memory stage, which is *after* the point where the next instruction needs it in its execute stage. No amount of bypassing can move data backwards in time, so at least one stall bubble is mandatory. Compilers mitigate it by scheduling an independent instruction into that slot.

(a) forwarding handles ALU-to-ALU RAW perfectly. (c) WAR is a name dependency, resolved by register renaming. (d) structural hazards need duplicated hardware, not forwarding.
</details>

---

**Q3.** A 16 KB direct-mapped cache with 32-byte blocks and 32-bit addresses. The tag is
(a) 16 bits  (b) 18 bits  (c) 20 bits  (d) 22 bits

<details><summary>Answer</summary>

**(b) 18 bits.**

```
offset = log2(32) = 5
blocks = 16 KB / 32 B = 512, direct mapped ⇒ 512 sets
index  = log2(512) = 9
tag    = 32 − 9 − 5 = 18
```
Do the three steps in that order every time — offset, index, tag — and this family of questions becomes mechanical.
</details>

---

**Q4.** Making the same cache 4-way set associative changes the tag to
(a) 18 bits  (b) 19 bits  (c) 20 bits  (d) 16 bits

<details><summary>Answer</summary>

**(c) 20 bits.**

Sets = 512/4 = 128 → index = 7 bits → tag = 32 − 7 − 5 = 20.

**The rule:** associativity moves bits from the index to the tag. Doubling the associativity halves the number of sets, removing one index bit and adding one tag bit.
</details>

---

**Q5.** Hit time 1 ns, miss rate 5%, miss penalty 100 ns. AMAT is
(a) 1.05 ns  (b) 5 ns  (c) 6 ns  (d) 101 ns

<details><summary>Answer</summary>

**(c) 6 ns.**

`AMAT = 1 + 0.05 × 100 = 6 ns`. A 5% miss rate makes the average access six times the hit time — which is the whole reason cache design obsesses over the last percent of miss rate.

(a) multiplies rather than adds; (d) ignores the miss rate.
</details>

---

**Q6.** Belady's anomaly, in cache terms, is most analogous to a problem with
(a) LRU replacement  (b) FIFO replacement  (c) write-back  (d) set associativity

<details><summary>Answer</summary>

**(b) FIFO.**

The same stack-algorithm argument applies: LRU and Optimal guarantee that a larger cache's resident set contains the smaller one's, so more capacity can never cause more misses. FIFO gives no such guarantee.
</details>

---

**Q7.** Which cache miss type is reduced by **increasing associativity**?
(a) Compulsory  (b) Capacity  (c) Conflict  (d) All three

<details><summary>Answer</summary>

**(c) Conflict.**

Conflict misses come from too many blocks mapping to the same set; more ways per set means more can coexist. **Compulsory** misses are first-touch and need prefetching or larger blocks. **Capacity** misses need a larger cache.

A fully associative cache has **zero** conflict misses by definition — a useful sanity check when reasoning about the three Cs.
</details>

---

**Q8.** 40% of a program is parallelisable. With 8 processors, Amdahl's law gives a speedup of
(a) 1.54  (b) 1.67  (c) 3.20  (d) 8.00

<details><summary>Answer</summary>

**(a) 1.54.**

`1/(0.6 + 0.4/8) = 1/0.65 = 1.54`.

(b) 1.67 is the **limit** with infinitely many processors, `1/(1 − P)`. Questions frequently ask for one and list the other, so read which is wanted.
</details>

---

**Q9.** In IEEE 754 single precision, the exponent field uses a bias of
(a) 64  (b) 127  (c) 128  (d) 1023

<details><summary>Answer</summary>

**(b) 127.**

Single: 8 exponent bits, bias **127**. Double: 11 exponent bits, bias **1023**. The bias lets exponents be compared as unsigned integers, which is why float comparison can use integer hardware.

(c) 128 is the classic off-by-one distractor.
</details>

---

**Q10.** Write-back caches compared to write-through
(a) generate more memory traffic
(b) generate less memory traffic but need a dirty bit and complicate coherence
(c) are always faster to read
(d) do not need a valid bit

<details><summary>Answer</summary>

**(b).**

Write-back defers the memory write until eviction, so repeated writes to the same line cost one memory write instead of many. The price is a dirty bit per line, a more complex eviction path, and harder cache coherence in a multiprocessor — memory is no longer authoritative.
</details>

---

**Q11.** RISC architectures typically
(a) have variable-length instructions
(b) allow memory operands in most instructions
(c) use fixed-length instructions and a load/store model
(d) have fewer registers than CISC

<details><summary>Answer</summary>

**(c).**

Fixed-length instructions simplify decoding and pipelining, and only load and store touch memory, which keeps the other instructions to a single cycle. RISC also typically has **more** registers, so (d) is backwards; (a) and (b) describe CISC.
</details>

---

**Q12.** Traversing a 2-D array column-major in C is slower than row-major because
(a) the compiler cannot optimise it
(b) each access touches a different cache line, destroying spatial locality
(c) column indices are larger
(d) it uses more registers

<details><summary>Answer</summary>

**(b).**

C stores arrays row-major, so consecutive elements of a row share a cache line. Walking down a column strides by the row length, touching a new line every time. With 64-byte lines and 4-byte ints, that is a 16× difference in miss count.
</details>

---

**Q13.** False sharing occurs when
(a) two threads read the same variable
(b) two threads write different variables that occupy the same cache line
(c) a cache line is evicted too early
(d) two processes share memory

<details><summary>Answer</summary>

**(b).**

The variables are logically independent, but coherence operates at cache-line granularity, so each write invalidates the other core's copy and the line ping-pongs between caches. The fix is padding or aligning the variables to separate lines.

It is a genuinely good thing to mention in a systems interview, because it explains multithreaded code that gets *slower* with more threads.
</details>

---

**Q14.** A branch mispredict on a deeply pipelined processor costs roughly
(a) 1 cycle  (b) the pipeline depth in cycles  (c) the cache miss penalty  (d) nothing

<details><summary>Answer</summary>

**(b) the pipeline depth.**

Every speculatively fetched instruction after the branch must be flushed, so the penalty scales with how far ahead the processor had run. On a 15–20 stage pipeline that is 15–20 wasted cycles — which is why a predictable branch can make a loop several times faster.
</details>

---

**Q15.** Which is **not** one of the three Cs of cache misses?
(a) Compulsory  (b) Capacity  (c) Conflict  (d) Coherence

<details><summary>Answer</summary>

**(d) Coherence.**

The classic three are compulsory, capacity and conflict. Coherence misses are sometimes added as a **fourth C** for multiprocessors — misses caused by another core invalidating your line — so if a question offers "four Cs" it is referring to that extension.
</details>

---

## Scoring

| Score /15 | Reading |
|---|---|
| 13+ | Architecture is OA-ready |
| 10–12 | Drill the cache-arithmetic family |
| 6–9 | Re-read `concepts.md` §4 and §5 |
| < 6 | Work `numericals.md` end to end |
