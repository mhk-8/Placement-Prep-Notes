# Computer Networks — Worked Numericals

> Subnetting and efficiency calculations are the two families that appear in almost every OA that tests networking. Both are mechanical; the marks go to whoever does the arithmetic without converting to binary.

---

## Part 1 — Subnetting

### The method (never convert to binary under time pressure)

```
Host bits      = 32 − prefix
Usable hosts   = 2^(host bits) − 2          (network + broadcast reserved)
Block size     = 256 − (last non-zero mask octet)
Subnet IDs     = multiples of the block size in that octet
Broadcast      = next subnet ID − 1
Usable range   = subnet ID + 1  …  broadcast − 1
```

| Prefix | Mask | Block | Usable hosts |
|---|---|---|---|
| /24 | 255.255.255.0 | 256 | 254 |
| /25 | 255.255.255.128 | 128 | 126 |
| /26 | 255.255.255.192 | 64 | 62 |
| /27 | 255.255.255.224 | 32 | 30 |
| /28 | 255.255.255.240 | 16 | 14 |
| /29 | 255.255.255.248 | 8 | 6 |
| /30 | 255.255.255.252 | 4 | 2 |

Memorise the right-hand column pattern: 254, 126, 62, 30, 14, 6, 2.

---

### N1. Which subnet does an address belong to?

**Q.** Host `192.168.10.100/26`. Find the subnet ID, broadcast address and usable host range.

```
/26 → mask 255.255.255.192 → block size = 256 − 192 = 64
Subnets in the last octet: 0, 64, 128, 192
100 falls in [64, 127]

Subnet ID   = 192.168.10.64
Broadcast   = 192.168.10.127
Usable      = 192.168.10.65  …  192.168.10.126   (62 hosts)
```

---

### N2. How many subnets and hosts?

**Q.** `200.1.2.0/24` must be divided so that each subnet supports at least 25 hosts. What prefix, how many subnets, how many usable hosts each?

```
Need ≥ 25 usable hosts → 2^h − 2 ≥ 25 → h = 5 gives 30 ✔  (h = 4 gives only 14)
Prefix = 32 − 5 = /27
Subnets = 2^(27−24) = 2^3 = 8
Usable hosts per subnet = 30
Block size = 256 − 224 = 32 → subnet IDs .0, .32, .64, .96, .128, .160, .192, .224
```
**Answer: /27, 8 subnets, 30 usable hosts each.**

**The trap:** "at least 25 hosts" means 25 *usable*, so you must satisfy `2^h − 2 ≥ 25`, not `2^h ≥ 25`.

---

### N3. Number of hosts in a large block

**Q.** How many usable host addresses does `172.16.0.0/20` provide?

```
Host bits = 32 − 20 = 12
Usable    = 2^12 − 2 = 4096 − 2 = 4094
```

---

### N4. Supernetting / route aggregation

**Q.** Aggregate `192.168.0.0/24`, `192.168.1.0/24`, `192.168.2.0/24`, `192.168.3.0/24` into one prefix.

Four consecutive /24s = 2² blocks, so borrow 2 bits from the prefix:
```
/24 − 2 = /22   →   192.168.0.0/22
Range: 192.168.0.0 … 192.168.3.255 ✔
```
**Check the alignment:** aggregation is only valid when the first block's third octet is a multiple of the count (0 is a multiple of 4 ✔). `192.168.1.0/24 … 192.168.4.0/24` cannot be aggregated into a single /22.

---

## Part 2 — Efficiency and throughput

### The formulas

```
Transmission time  Tt = frame size / bandwidth
Propagation time   Tp = distance / propagation speed      (or RTT / 2)
a = Tp / Tt

Stop-and-wait efficiency  η = 1 / (1 + 2a)
Sliding-window efficiency η = min(1,  N / (1 + 2a))
Throughput = η × bandwidth
Optimal window for full utilisation:  N ≥ 1 + 2a
```

---

### N5. Stop-and-wait efficiency and throughput

**Q.** Bandwidth 1 Mbps, frame size 1000 bits, RTT 20 ms. Find the efficiency and effective throughput under stop-and-wait.

```
Tt  = 1000 bits / 10^6 bps = 1 ms
RTT = 2Tp = 20 ms  →  Tp = 10 ms
a   = Tp / Tt = 10

η = 1 / (1 + 2×10) = 1/21 = 0.0476  →  4.76%
Throughput = 0.0476 × 1 Mbps = 47.6 kbps
```
**The lesson to state aloud:** on a long fat link, stop-and-wait wastes over 95% of the capacity, which is the entire motivation for sliding-window protocols.

---

### N6. Optimal window size and sequence-number bits

**Q.** Same link. What window size gives 100% utilisation, and how many sequence-number bits are needed for Go-Back-N and for Selective Repeat?

```
N ≥ 1 + 2a = 1 + 20 = 21 frames

Go-Back-N:        N ≤ 2^k − 1   →  21 ≤ 2^k − 1  →  2^k ≥ 22  →  k = 5   (2^5 − 1 = 31 ✔)
Selective Repeat: N ≤ 2^(k−1)   →  21 ≤ 2^(k−1)  →  2^(k−1) ≥ 21 → k = 6  (2^5 = 32 ✔)
```
**Why the SR bound is tighter:** the sender and receiver windows must not overlap ambiguously, so each can occupy at most half the sequence space.

---

### N7. Utilisation with a given window

**Q.** Bandwidth 4 Mbps, frame 4000 bits, one-way propagation 20 ms, window N = 7. Efficiency?

```
Tt = 4000 / (4 × 10^6) = 1 ms
a  = 20 / 1 = 20
η  = min(1, N / (1 + 2a)) = min(1, 7 / 41) = 0.1707  →  17.07%
Throughput = 0.1707 × 4 Mbps ≈ 683 kbps
```

---

### N8. Bandwidth-delay product

**Q.** A 10 Mbps link with a 30 ms RTT. How much data can be "in flight"?

```
BDP = bandwidth × RTT = 10^7 bps × 0.030 s = 300,000 bits = 37.5 KB
```
This is the minimum send-window size needed to keep the pipe full, and it is why TCP window scaling exists — the base 16-bit window field caps out at 64 KB, which is too small for modern long fat links.

---

## Part 3 — Error detection

### N9. CRC

**Q.** Data `1010`, generator `1011` (degree 3). Find the transmitted codeword.

**Step 1 — append (degree of generator) zeros:** `1010` → `1010000`

**Step 2 — modulo-2 division (XOR, no borrow), aligning the generator under each leading 1:**
```
   1010000
   1011          ← align at bit 0
   -------
   0001000       ← leading 1 is now at bit 3
      1011       ← align at bit 3
   -------
   0000011       ← only 3 bits left, shorter than the generator → stop
```
**Remainder = `011`**

**Step 3 — codeword = data + remainder:** `1010` + `011` = **`1010011`**

**Receiver check:** divide the received `1010011` by `1011`; a zero remainder means no detected error.

**Properties worth quoting:** a generator of degree r detects all single-bit errors, all burst errors of length ≤ r, and all odd-numbered bit errors if the generator has `(x + 1)` as a factor.

---

### N10. Hamming distance

**Q.** A code has minimum Hamming distance 5. How many errors can it detect, and how many can it correct?

```
Detect  = d − 1            = 4
Correct = ⌊(d − 1)/2⌋      = ⌊4/2⌋ = 2
```
**Intuition:** to *detect*, a corrupted word must not land on another valid codeword. To *correct*, it must stay strictly nearer to the original than to any other codeword — hence roughly half the distance.

---

### N11. Parity bits for Hamming code

**Q.** How many parity bits are needed to protect 7 data bits with a single-error-correcting Hamming code?

```
Need 2^r ≥ m + r + 1  with m = 7
r = 3 → 8 ≥ 11 ✘
r = 4 → 16 ≥ 12 ✔
```
**Answer: 4 parity bits**, giving the familiar (11, 7) code.

---

## Practice set

1. `10.1.1.0/28` — how many usable hosts, and what is the broadcast address of the first subnet?
2. Which subnet does `172.16.35.123/21` belong to?
3. A `/24` network must be split into 12 subnets. What prefix, and how many usable hosts each?
4. Bandwidth 2 Mbps, frame 2000 bits, RTT 40 ms. Stop-and-wait efficiency?
5. Same link — minimum window for 100% utilisation?
6. A code has minimum Hamming distance 4. Detection and correction capability?
7. How many usable addresses in `192.168.0.0/22`?

<details><summary>Answers</summary>

1. Host bits = 4 → 2⁴ − 2 = **14 usable**. Block size = 16, so the first subnet is 10.1.1.0–10.1.1.15 → **broadcast 10.1.1.15**.
2. /21 → mask 255.255.248.0 → block size in the third octet = 256 − 248 = 8 → boundaries at 0, 8, 16, 24, **32**, 40… 35 falls in [32, 39] → **subnet 172.16.32.0**, broadcast 172.16.39.255.
3. 12 subnets needs 2ˢ ≥ 12 → s = 4 → prefix /28, giving 16 subnets and 2⁴ − 2 = **14 usable hosts each**.
4. Tt = 2000/(2×10⁶) = 1 ms; Tp = 20 ms; a = 20 → η = 1/41 = **2.44%**.
5. N ≥ 1 + 2a = **41 frames**.
6. Detect 3, correct ⌊3/2⌋ = **1**.
7. Host bits = 10 → 2¹⁰ − 2 = **1022**.

</details>
