# Computer Networks — Solved OA Questions

> 22 questions in OA style with every option explained. CN MCQs cluster around four areas: layer/protocol matching, TCP mechanics, subnetting arithmetic, and port numbers. All four are pure recall, which makes this the fastest-scoring subject per hour of study.
> **45 seconds** for conceptual, **90 seconds** for subnetting.

---

## Set A — Layers and protocols

**Q1.** Which layer is responsible for **process-to-process** delivery?
(a) Network  (b) Transport  (c) Data link  (d) Session

<details><summary>Answer</summary>

**(b) Transport.**

The network layer delivers to a **host** using an IP address; the transport layer delivers to a **process** using a port number. A socket is the pair (IP, port), which is exactly the composition of those two layers.

(c) data link delivers hop-to-hop using MAC addresses. (d) the session layer manages dialogue, and in the TCP/IP model it does not separately exist.
</details>

---

**Q2.** A switch operates at which layer, and a router at which?
(a) 1 and 2  (b) 2 and 3  (c) 3 and 4  (d) 2 and 2

<details><summary>Answer</summary>

**(b) 2 and 3.**

A switch forwards frames by **MAC address** (data link); a router forwards packets by **IP address** (network). A hub is layer 1 — it simply repeats bits to every port, which is why hubs create one big collision domain and switches do not.

**Useful follow-up to volunteer:** a switch separates collision domains but not broadcast domains; a router separates both.
</details>

---

**Q3.** Which protocol maps an IP address to a MAC address?
(a) DNS  (b) DHCP  (c) ARP  (d) ICMP

<details><summary>Answer</summary>

**(c) ARP.**

ARP broadcasts "who has this IP?" on the local link and caches the reply. (a) DNS maps names to IPs. (b) DHCP assigns an IP to a host. (d) ICMP carries control and error messages — it is what `ping` and `traceroute` use.

**RARP** (the reverse: MAC → IP) exists historically but has been replaced by DHCP.
</details>

---

**Q4.** Which of these runs over **UDP** by default?
(a) HTTP  (b) SSH  (c) DNS  (d) SMTP

<details><summary>Answer</summary>

**(c) DNS.**

DNS queries are small and idempotent, so the connection overhead of TCP is not worth it — one datagram out, one back. DNS **falls back to TCP** for responses larger than 512 bytes and for zone transfers, which is the detail worth adding.

Other UDP services: DHCP, TFTP, SNMP, NTP, VoIP/RTP, and QUIC (which carries HTTP/3).
</details>

---

## Set B — TCP

**Q5.** Why is TCP's connection setup three-way rather than two-way?
(a) To exchange encryption keys
(b) Because both sides must synchronise a sequence number and have it acknowledged
(c) To negotiate the MTU
(d) For backwards compatibility

<details><summary>Answer</summary>

**(b).**

The connection is full-duplex, so each direction needs its own initial sequence number, and each ISN must be acknowledged. Client SYN carries x; server SYN-ACK carries y and acknowledges x; the client's ACK acknowledges y. Two messages would leave the server's ISN unacknowledged.
</details>

---

**Q6.** Connection teardown requires **four** segments because
(a) TCP is unreliable
(b) each direction closes independently
(c) the checksum must be recomputed
(d) of the TIME_WAIT state

<details><summary>Answer</summary>

**(b).**

TCP is full-duplex. One side sending FIN means "I have no more data", but the other may still be transmitting — this is the half-close. So each direction needs its own FIN and ACK.

(d) TIME_WAIT is a consequence of closing, not the reason for four segments — it exists so that delayed duplicates from the old connection cannot be misread as data on a new connection reusing the same port pair.
</details>

---

**Q7.** TCP's congestion window grows **exponentially** during
(a) congestion avoidance  (b) slow start  (c) fast recovery  (d) timeout

<details><summary>Answer</summary>

**(b) slow start.**

`cwnd` doubles each RTT until it reaches `ssthresh`, then congestion avoidance takes over with **linear** growth (one MSS per RTT).

The name is misleading and setters exploit it: slow start is the *fastest-growing* phase — it is "slow" only relative to blasting at full rate from the first packet.
</details>

---

**Q8.** On receiving **three duplicate ACKs**, TCP Reno
(a) sets cwnd to 1 and restarts slow start
(b) halves cwnd and performs fast retransmit/fast recovery
(c) closes the connection
(d) ignores them

<details><summary>Answer</summary>

**(b).**

Three duplicate ACKs indicate an isolated loss with packets still flowing — mild congestion — so the response is multiplicative decrease without collapsing the window.

(a) is the response to a **timeout**, which signals severe congestion: `ssthresh = cwnd/2`, `cwnd = 1`, restart slow start. Distinguishing these two responses is the most-asked congestion-control question.
</details>

---

**Q9.** Which statement about TCP is **most accurate**?
(a) TCP guarantees that data will be delivered
(b) TCP guarantees in-order, uncorrupted delivery or notifies the application of failure
(c) TCP guarantees a fixed bandwidth
(d) TCP guarantees low latency

<details><summary>Answer</summary>

**(b).**

If the network is severed, no protocol can deliver anything — TCP's guarantee is that you will either get correct, ordered data or an error, never silently corrupted or reordered data.

(a) is the common loose phrasing and is the intended trap. (c) and (d) are false: TCP actively *degrades* latency through retransmission and head-of-line blocking, which is precisely why HTTP/3 moved to QUIC over UDP.
</details>

---

**Q10.** The minimum TCP header size and minimum UDP header size are
(a) 20 and 8 bytes  (b) 20 and 20  (c) 8 and 20  (d) 16 and 8

<details><summary>Answer</summary>

**(a) 20 and 8 bytes.**

TCP's 20 bytes carry source and destination ports, sequence and acknowledgement numbers, flags, window, checksum and urgent pointer, and it can grow to 60 with options. UDP's 8 bytes are just source port, destination port, length and checksum — that minimalism *is* UDP's advantage.
</details>

---

## Set C — Addressing and subnetting

**Q11.** Host `192.168.10.100/26`. What is its subnet's broadcast address?
(a) 192.168.10.63  (b) 192.168.10.127  (c) 192.168.10.191  (d) 192.168.10.255

<details><summary>Answer</summary>

**(b) 192.168.10.127.**

```
/26 → mask 255.255.255.192 → block size 256 − 192 = 64
Subnets: .0-.63, .64-.127, .128-.191, .192-.255
100 lies in .64-.127 → subnet ID .64, broadcast .127
```
Each distractor is the broadcast of a neighbouring block, which is why finding the block size first — rather than eyeballing — is the reliable method.
</details>

---

**Q12.** How many **usable** host addresses does a `/27` subnet provide?
(a) 32  (b) 30  (c) 62  (d) 16

<details><summary>Answer</summary>

**(b) 30.**

Host bits = 32 − 27 = 5 → 2⁵ = 32 addresses, minus the network address and the broadcast address → **30 usable**.

(a) forgets the two reserved addresses — the single most common subnetting error. (c) is /26.
</details>

---

**Q13.** A `/24` network must be divided into at least 6 subnets. The smallest sufficient prefix is
(a) /25  (b) /26  (c) /27  (d) /28

<details><summary>Answer</summary>

**(c) /27.**

Borrowing s bits gives 2ˢ subnets: s = 2 gives 4 (too few), **s = 3 gives 8 ✔**. So the prefix is 24 + 3 = /27, with 30 usable hosts each.

(b) /26 yields only 4 subnets.
</details>

---

**Q14.** Which address range is **private** (RFC 1918)?
(a) 172.32.0.0/12  (b) 172.16.0.0/12  (c) 192.169.0.0/16  (d) 11.0.0.0/8

<details><summary>Answer</summary>

**(b) 172.16.0.0/12.**

The three private ranges are `10.0.0.0/8`, `172.16.0.0/12` (i.e. 172.16.x.x through 172.31.x.x) and `192.168.0.0/16`.

Every distractor is deliberately one step outside a real range — 172.**32** instead of 172.16, 192.**169** instead of 192.168, **11** instead of 10. Read the numbers, do not pattern-match.
</details>

---

## Set D — Efficiency

**Q15.** Bandwidth 1 Mbps, frame 1000 bits, RTT 20 ms. Stop-and-wait efficiency?
(a) 50%  (b) 9.1%  (c) 4.76%  (d) 100%

<details><summary>Answer</summary>

**(c) 4.76%.**

```
Tt = 1000 / 10^6 = 1 ms
RTT = 2Tp = 20 ms → Tp = 10 ms → a = Tp/Tt = 10
η = 1/(1 + 2a) = 1/21 = 4.76%
```
(b) comes from using `1/(1 + a)` — forgetting that the acknowledgement must also propagate back.
</details>

---

**Q16.** For the same link, the minimum sliding-window size for 100% utilisation is
(a) 10  (b) 11  (c) 20  (d) 21

<details><summary>Answer</summary>

**(d) 21.**

`N ≥ 1 + 2a = 1 + 20 = 21`. The sender must be able to keep transmitting for the entire round trip plus its own transmission time.

(b) 11 comes from `1 + a`.
</details>

---

**Q17.** Go-Back-N with a 4-bit sequence number — maximum window size?
(a) 16  (b) 15  (c) 8  (d) 7

<details><summary>Answer</summary>

**(b) 15.**

Go-Back-N: `N ≤ 2ᵏ − 1 = 2⁴ − 1 = 15`.
Selective Repeat: `N ≤ 2ᵏ⁻¹ = 2³ = 8`.

The receiver window in Go-Back-N is 1, so only the sender window is constrained; Selective Repeat has a receiver window of N as well, and the two must not overlap ambiguously — hence the tighter half-space bound.
</details>

---

## Set E — Application layer

**Q18.** Which HTTP status code means "the resource has moved permanently"?
(a) 301  (b) 302  (c) 304  (d) 307

<details><summary>Answer</summary>

**(a) 301.**

301 is permanent — clients and search engines should update the stored link. 302 is temporary. **304 Not Modified** is a cache-validation response with no body. 307 is a temporary redirect that additionally preserves the HTTP method.
</details>

---

**Q19.** Which HTTP methods are **idempotent**?
(a) GET, PUT, DELETE  (b) GET, POST  (c) POST, PATCH  (d) All of them

<details><summary>Answer</summary>

**(a) GET, PUT, DELETE.**

Idempotent means repeating the request leaves the server in the same state. `PUT /users/5` with the same body twice gives the same result; `DELETE` twice leaves the resource deleted. **POST is not** — it creates a new resource each time, which is why duplicate form submissions are a problem. PATCH is generally not idempotent, since it may apply a relative change.

GET is additionally **safe** — it should have no side effects at all.
</details>

---

**Q20.** In the TLS handshake, asymmetric cryptography is used to
(a) encrypt all application data
(b) establish a shared symmetric key and authenticate the server
(c) compute the checksum
(d) compress the payload

<details><summary>Answer</summary>

**(b).**

Asymmetric operations are orders of magnitude slower than symmetric ones, so they are used only for the handshake — authenticating the server's certificate and agreeing a session key. All bulk traffic afterwards is symmetric (AES).

Modern suites use **ECDHE** for the key exchange rather than RSA key transport, so that a later compromise of the server's private key cannot decrypt recorded past sessions — **forward secrecy**, a strong detail to volunteer.
</details>

---

**Q21.** HTTP/3's main change is
(a) it uses UDP via QUIC, eliminating TCP head-of-line blocking
(b) it adds encryption for the first time
(c) it removes header compression
(d) it returns to one request per connection

<details><summary>Answer</summary>

**(a).**

HTTP/2 multiplexes streams over a single TCP connection, so one lost segment stalls **every** stream — TCP's in-order delivery applies to the whole connection. QUIC implements streams at the transport layer over UDP, so a loss stalls only its own stream. QUIC also merges the transport and TLS handshakes, cutting connection setup to one round trip (zero for resumption).
</details>

---

**Q22.** Default port numbers for HTTPS, SSH and MySQL?
(a) 443, 22, 3306  (b) 80, 21, 3306  (c) 443, 23, 5432  (d) 8080, 22, 27017

<details><summary>Answer</summary>

**(a) 443, 22, 3306.**

Worth having automatic: 20/21 FTP, **22 SSH**, 23 Telnet, 25 SMTP, **53 DNS**, 67/68 DHCP, **80 HTTP**, 110 POP3, 143 IMAP, **443 HTTPS**, **3306 MySQL**, 5432 PostgreSQL, 6379 Redis, 27017 MongoDB.
</details>

---

## Scoring

| Score /22 | Reading |
|---|---|
| 19+ | CN is OA-ready |
| 15–18 | Solid; drill subnetting and the efficiency formulas |
| 10–14 | Re-read `concepts.md`, redo `numericals.md` |
| < 10 | Study the layers and TCP properly first |

**If your misses are concentrated in subnetting**, that is the best news in this file — it is the most mechanical topic in core CS, and an hour of the block-size method fixes it permanently.
