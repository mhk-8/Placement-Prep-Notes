
# Qualcomm — Prep Plan, Question Bank and Debriefs

---

## 1. Topic priorities ⭐⭐

| Priority | Topic | Source | Status |
|---|---|---|---|
| **P1** | C pointers, arrays vs pointers, struct padding, function pointers | `../../03_Languages/CPP/` | ☐ |
| **P1** | **Bit manipulation** — set/clear/toggle, count, swap, endianness | `../../01_DSA/` bit manipulation | ☐ |
| **P1** | OS — processes/threads, scheduling, interrupts, virtual memory | `../../06_Online_Assessments/05_MCQ_Core_CS_Banks/Operating_Systems.md` | ☐ |
| **P1** | **Digital logic** ⚠️ your likely gap — flip-flops, muxes, K-maps, FSMs | `../../02_Core_CS/05_Computer_Architecture/` | ☐ |
| **P2** | Architecture — caches, pipelining, hazards, memory hierarchy | same | ☐ |
| **P2** | DSA Medium with memory constraints (in-place, fixed buffers) | `../../01_DSA/` | ☐ |
| **P2** | Embedded C idioms — `volatile`, memory-mapped registers, ISRs | see §3 | ☐ |
| **P3** | C++ specifics for C++-track roles | `../../07_Interviews/01_Technical_Round_Prep/03-CS_Fundamentals_Rapid_Fire.md` §6 | ☐ |

---

## 2. The two-week plan ⭐⭐⭐

| Date | Task | Done |
|---|---|---|
| D-14 | Research protocol; **confirm the language restriction** ⚠️; overwrite the volatile table | ☐ |
| D-13 | C pointer drills: 30 output-prediction snippets | ☐ |
| D-12 | Bit manipulation: 15 problems, all written in C | ☐ |
| D-11 | OS MCQ bank, all 20 questions | ☐ |
| D-10 | **Digital logic**: flip-flops, muxes, K-maps, FSMs — from scratch if needed | ☐ |
| D-9 | Architecture: caches, pipelining, hazards, false sharing | ☐ |
| D-8 | DSA: 3 Medium problems in C++, with in-place constraints | ☐ |
| D-7 | Embedded C: `volatile`, memory-mapped I/O, ISR constraints (what you cannot do in one) | ☐ |
| D-6 | Image preprocessing project: 5-minute version, memory-layout emphasis | ☐ |
| D-5 | DSA: 3 more Medium. Graphs and DP | ☐ |
| D-4 | **Mock: C/C++ rapid-fire + one coding problem** | ☐ |
| D-3 | Struct padding and alignment drills; endianness; `sizeof` puzzles | ☐ |
| D-2 | Qualcomm blog; prepare 2 questions; rehearse the no-internship answer | ☐ |
| D-1 | Pre-interview routine only. Nothing new ⚠️ | ☐ |

---

## 3. Seeded question bank

### C — expect depth ⭐⭐⭐
```
- Output prediction: pointer arithmetic, *(arr+i) vs arr[i], array decay
- sizeof on an array vs a pointer vs a struct (padding!)
- Struct padding and alignment — compute the size of a given struct  ⭐ very common
- What does `volatile` mean and when is it REQUIRED? (memory-mapped registers, ISR-modified
  variables, and ⚠️ NOT for thread synchronisation — that's atomics)
- Function pointers; callback patterns; arrays of function pointers
- `const` placement: const char*, char* const, const char* const
- Storage classes: auto, static, extern, register
- Macros vs inline functions vs const; the classic MAX(a,b) double-evaluation bug
- Dangling pointers, memory leaks, double free
- Endianness: detect it in C; why it matters for network and file formats
- Bit fields in structs
```

### Bit manipulation ⭐⭐
```
- Set / clear / toggle / test bit n
- Count set bits (naive, Brian Kernighan's n & (n-1), lookup table)
- Check if a number is a power of two
- Swap two numbers without a temporary
- Reverse the bits of an integer
- Find the single non-repeating element (XOR)
- Clear the lowest set bit; isolate the lowest set bit
- Multiply/divide by powers of two with shifts; ⚠️ the signed right-shift caveat
```

### OS and embedded
```
- Process vs thread; context switch cost
- What can you NOT do inside an interrupt service routine, and why?  ⭐
- Polling vs interrupts; interrupt latency
- Semaphore vs mutex vs spinlock; priority inversion and priority inheritance
- Deadlock: the four conditions and how to break them
- Virtual memory, TLB, page faults
- RTOS: hard vs soft real-time; rate-monotonic scheduling
- DMA — what it is and why it matters
```

### Digital logic ⚠️ (likely your gap)
```
- Combinational vs sequential logic
- Flip-flops: SR, D, JK, T; setup and hold time
- Multiplexers, decoders, encoders
- Karnaugh map simplification
- Design a finite state machine for a given sequence detector
- Synchronous vs asynchronous reset
- Metastability and clock domain crossing
- Ripple vs carry-lookahead adder
```

### Architecture
```
- Memory hierarchy and typical latencies
- Cache: direct-mapped vs set-associative vs fully associative; write-through vs write-back
- Cache line, false sharing  ⭐
- Pipelining and the three hazards; forwarding; branch prediction
- SIMD; and SIMT if they ask about GPUs
```

### Coding (Medium, C/C++)
```
- Array and string manipulation, in place
- Linked lists — reverse, cycle detection, merge
- Matrix traversal and rotation in place
- Implement a circular buffer / ring queue  ⭐ embedded favourite
- Implement memcpy / strcpy / atoi correctly, including edge cases
- Graph BFS/DFS
- Simple DP
```

### Project deep dive
```
- "Walk me through the memory layout of your image pipeline."  ⭐ lead here
- "What is coalescing and why did it matter?"
- "Why HWC rather than CHW?"
- "How did you validate correctness, and why a tolerance rather than exact equality?"
- "What was the bottleneck — compute or memory? How do you know?"
```

### Behavioural
```
- Why Qualcomm? Why systems/embedded rather than application software?
- Why no industry internship?  ⚠️ likely here
- Why Mechanical → CS?
- Tell me about a bug that took you a long time to find
```

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

## 6. Questions I collected myself ⭐⭐⭐

| Date | Source (senior / my attempt) | Round | Question | Topic |
|---|---|---|---|---|
| | | | | |
| | | | | |
| | | | | |

---

## 7. Corrections to make to `00-process-and-oa.md`

```
□
□
```


---

## 8. The 30-second pre-interview refresh ⭐

```
□ volatile = may change outside program flow (memory-mapped reg, ISR variable); NOT for threading
□ Struct padding: members align to their own size; total pads to the largest member's alignment
□ n & (n-1) clears the lowest set bit → Brian Kernighan's bit count
□ You cannot block, allocate, or call a long function inside an ISR
□ False sharing: two cores writing different vars on one cache line → pad to 64 bytes
□ Cache line = 64 bytes typically; miss to DRAM ≈ few hundred cycles
□ My lead project here is the IMAGE PIPELINE (memory layout), not the compiler one
```
