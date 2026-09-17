# Computer Networks — Diagrams

---

## 1. The layers and what each adds

```
  ┌────────────────────────────────────────────────────────┐
  │ APPLICATION   HTTP / DNS / SMTP / SSH        message    │
  ├────────────────────────────────────────────────────────┤
  │ TRANSPORT     TCP / UDP      process↔process  segment   │  + src/dst PORT
  ├────────────────────────────────────────────────────────┤
  │ NETWORK       IP / ICMP      host↔host        packet    │  + src/dst IP
  ├────────────────────────────────────────────────────────┤
  │ DATA LINK     Ethernet / ARP hop↔hop          frame     │  + src/dst MAC + CRC
  ├────────────────────────────────────────────────────────┤
  │ PHYSICAL      cables, radio                   bit       │
  └────────────────────────────────────────────────────────┘

  Encapsulation:   [ Ethernet [ IP [ TCP [ HTTP payload ] ] ] ]
                   └── stripped one layer at a time on the way up
```

---

## 2. TCP three-way handshake and four-way teardown

```
  SETUP                                TEARDOWN
  Client            Server             Client              Server
    │── SYN seq=x ────►│                 │── FIN seq=u ──────►│
    │◄─ SYN-ACK ───────│                 │◄─ ACK u+1 ─────────│
    │   seq=y, ack=x+1 │                 │                    │  (server may
    │── ACK ack=y+1 ──►│                 │                    │   still send)
    │                  │                 │◄─ FIN seq=v ───────│
   ESTABLISHED                           │── ACK v+1 ────────►│
                                      TIME_WAIT (2×MSL)
                                         │
                                       CLOSED
```

**Three for setup** because both directions need a sequence number acknowledged.
**Four for teardown** because each direction closes independently (half-close).
**TIME_WAIT** so that delayed duplicates cannot be mistaken for data on a new connection reusing the same port pair.

---

## 3. TCP congestion control

```
  cwnd
    │                                    ╱╲  timeout
    │                     ╱─────────────╱  ╲  → cwnd=1
    │                   ╱   congestion       ╲
    │        ssthresh ─╱─── avoidance ───────┐╲
    │                ╱     (linear, +1 MSS)  │ ╲
    │               ╱                        │  ╲
    │     slow     ╱  ← 3 dup ACKs:          │   ╲
    │     start   ╱     halve cwnd           │    ╲___ slow start again
    │  (exponential)                         │
    └───────────────────────────────────────────────────► time (RTTs)
```

| Event | Response |
|---|---|
| ACKs arriving, cwnd < ssthresh | double cwnd each RTT (slow start) |
| ACKs arriving, cwnd ≥ ssthresh | +1 MSS per RTT (congestion avoidance) |
| 3 duplicate ACKs | fast retransmit; halve cwnd (mild congestion) |
| Timeout | ssthresh = cwnd/2, cwnd = 1, slow start (severe congestion) |

---

## 4. Subnetting a /24 into /26

```
  192.168.10.0/24   →   block size 256 − 192 = 64

  ┌───────────────┬───────────────┬───────────────┬───────────────┐
  │  .0   -  .63  │  .64  - .127  │ .128  - .191  │ .192  - .255  │
  ├───────────────┼───────────────┼───────────────┼───────────────┤
  │ net     .0    │ net     .64   │ net    .128   │ net    .192   │
  │ hosts .1-.62  │ hosts .65-.126│ hosts.129-.190│ hosts.193-.254│
  │ bcast   .63   │ bcast   .127  │ bcast  .191   │ bcast  .255   │
  └───────────────┴───────────────┴───────────────┴───────────────┘
        62 usable hosts per subnet,  4 subnets
```

---

## 5. What happens when you type google.com

```
  1. CACHE     browser → OS → hosts file
       │ miss
  2. DNS       stub → recursive resolver → root → .com TLD → authoritative
       │ returns 142.250.x.x
  3. ARP       resolve the default gateway's MAC (if not cached)
       │
  4. TCP       SYN → SYN-ACK → ACK       (port 443)
       │
  5. TLS       ClientHello → cert + key exchange → symmetric keys
       │
  6. HTTP      GET / HTTP/2  →  200 OK (or 301 first)
       │
  7. ROUTING   each hop: decrement TTL, longest-prefix match, forward
       │       NAT rewrites src IP/port at the home router
  8. RENDER    parse HTML → DOM, fetch CSS/JS/img, CSSOM, render tree,
               layout, paint
```

---

## 6. Sliding window protocols

```
  GO-BACK-N (receiver window = 1)          SELECTIVE REPEAT (receiver window = N)

  sent: 1 2 3 4 5                           sent: 1 2 3 4 5
             ✗                                         ✗
  retransmit: 3 4 5   ← everything          retransmit: 3   ← only the lost one
              after the loss

  N ≤ 2^k − 1                               N ≤ 2^(k−1)
  simple receiver, wasteful on loss         complex receiver, efficient on loss
```

`η = min(1, N / (1 + 2a))` where `a = Tp / Tt`.
