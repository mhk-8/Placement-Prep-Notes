# Computer Architecture — Diagrams

---

## 1. The classic 5-stage pipeline

```
  cycle →   1    2    3    4    5    6    7    8
  I1       IF   ID   EX   MEM  WB
  I2            IF   ID   EX   MEM  WB
  I3                 IF   ID   EX   MEM  WB
  I4                      IF   ID   EX   MEM  WB

  IF  instruction fetch      MEM memory access
  ID  decode + register read  WB  write back
  EX  execute / ALU

  n instructions take (k + n − 1) cycles, not n × k
```

## 2. Forwarding and the load-use stall

```
  ADD R1, R2, R3      IF  ID  EX  MEM WB
  SUB R4, R1, R5          IF  ID  EX  MEM WB
                                  ▲
                          EX→EX forwarding: no stall ✔

  LW  R1, 0(R2)       IF  ID  EX  MEM WB
  SUB R4, R1, R5          IF  ID ─── ▼ ── EX  MEM WB
                                 stall
                          data ready only after MEM → 1 bubble unavoidable ✘
```

## 3. Cache address decomposition

```
   32-bit address
   ┌──────────────────┬───────────┬──────────────┐
   │       TAG        │   INDEX   │ BLOCK OFFSET │
   └──────────────────┴───────────┴──────────────┘
      compared           selects       byte within
      on lookup          the set       the block

   offset = log2(block size)
   sets   = cache size / (block size × associativity)
   index  = log2(sets)
   tag    = address bits − index − offset
```

```
   DIRECT MAPPED           2-WAY SET ASSOC          FULLY ASSOCIATIVE
   ┌────┐                  ┌────┬────┐              ┌────┬────┬────┬────┐
   │ 0  │ one home         │ 0  │ 0  │ two homes    │ any location      │
   │ 1  │ per block        │ 1  │ 1  │ per block    │                   │
   │ 2  │                  │ 2  │ 2  │              │ index = 0 bits    │
   └────┘                  └────┴────┘              └───────────────────┘
   most conflict misses    compromise               no conflict misses
   cheapest lookup                                  costliest lookup
```

## 4. Memory hierarchy with real numbers

```
   ┌──────────────┐  ~0.3 ns   registers        few hundred bytes
   ├──────────────┤  ~1 ns     L1               32–64 KB
   ├──────────────┤  ~4 ns     L2               256 KB–1 MB
   ├──────────────┤  ~12 ns    L3 (shared)      8–32 MB
   ├──────────────┤  ~80 ns    DRAM             GBs
   ├──────────────┤  ~100 µs   SSD              TBs
   ├──────────────┤  ~10 ms    HDD              TBs
   └──────────────┘
        each level ≈ 10–100× slower, ≈ 10× cheaper per byte
```

Memorising the shape of this table also gives you the latency numbers used in `04_System_Design`.

## 5. IEEE 754 single precision

```
   ┌─┬──────────┬───────────────────────────┐
   │S│ exponent │         mantissa          │
   └─┴──────────┴───────────────────────────┘
    1     8  bits          23 bits          = 32 bits

   value = (−1)^S × 1.mantissa × 2^(exponent − 127)
                    └── leading 1 is IMPLICIT, not stored

   exponent all 0s  → zero / denormal
   exponent all 1s  → infinity (mantissa 0) or NaN (mantissa ≠ 0)

   double: 1 | 11 (bias 1023) | 52
```

## 6. Amdahl's law

```
  speedup
    │
  5 ┤                                    ← P = 0.90 → limit 10
    │        ╭──────────────────────
  4 ┤      ╭─╯
    │    ╭─╯                             ← P = 0.75 → limit 4
  3 ┤  ╭─╯      ╭────────────────────
    │ ╱       ╭─╯
  2 ┤╱     ╭──╯                          ← P = 0.40 → limit 1.67
    │   ╭──╯   ─────────────────────
  1 ┼───────────────────────────────────►
    1   2   4   8  16  32  64   processors

    Speedup = 1 / ((1 − P) + P/N),   limit = 1/(1 − P)
```

The curves flatten quickly. Optimise the serial fraction before adding hardware — the same lesson applies to distributed systems.
