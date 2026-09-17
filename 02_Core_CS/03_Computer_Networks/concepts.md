# Computer Networks — Concepts

## 1. Core idea in 3 lines
Networking is layering: each layer solves one problem and hands a clean abstraction upward, so that "send these bytes to that machine" decomposes into framing, addressing, routing, reliability and application semantics. Almost every CN interview question is answered by naming the right layer and its protocol. The one question you will certainly be asked is "what happens when you type a URL", which is just a walk down and back up the stack.

---

## 2. The layers

| OSI layer | TCP/IP | Job | Unit | Protocols |
|---|---|---|---|---|
| 7 Application | Application | Services to the user | message | HTTP, DNS, SMTP, FTP, SSH |
| 6 Presentation | ″ | Encoding, encryption, compression | — | TLS, JPEG |
| 5 Session | ″ | Dialogue control | — | — |
| 4 **Transport** | Transport | **Process-to-process**, reliability | segment | **TCP, UDP** |
| 3 **Network** | Internet | **Host-to-host**, routing | packet | **IP**, ICMP, routing protocols |
| 2 Data link | Link | **Hop-to-hop**, framing, MAC | frame | Ethernet, ARP, PPP |
| 1 Physical | ″ | Bits on the wire | bit | — |

**The distinction that gets tested:** the network layer delivers to a **host** (IP address); the transport layer delivers to a **process** (port). A socket is the pair (IP, port).

**Devices by layer:** hub = physical; switch and bridge = data link (MAC); router = network (IP); gateway = above.

**Encapsulation:** each layer prepends its header going down, and strips it going up. A single HTTP GET travels as Ethernet[IP[TCP[HTTP]]].

---

## 3. TCP and UDP

| | TCP | UDP |
|---|---|---|
| Connection | connection-oriented (handshake) | connectionless |
| Reliability | ACKs, retransmission, sequencing | none |
| Ordering | guaranteed | not guaranteed |
| Flow control | sliding window | none |
| Congestion control | yes | none |
| Header | 20 bytes minimum | **8 bytes** |
| Speed | slower | faster |
| Use | HTTP, SSH, SMTP, file transfer | DNS, DHCP, VoIP, video, gaming, QUIC |

**What TCP actually guarantees:** that data arrives in order and uncorrupted, **or that the application is told the connection failed**. It does not guarantee delivery in the face of a broken network — no protocol can. This precise phrasing is a favourite MCQ trap.

### Three-way handshake
```
Client                              Server
  │── SYN, seq=x ─────────────────────►│
  │◄── SYN-ACK, seq=y, ack=x+1 ────────│
  │── ACK, ack=y+1 ───────────────────►│
```
Three messages rather than two because **both** sides must synchronise a sequence number and have it acknowledged.

### Four-way teardown
Each direction closes independently (FIN / ACK, FIN / ACK), because TCP is full-duplex and one side may still have data to send. The closer then sits in **TIME_WAIT** for 2×MSL, so that delayed duplicates from the old connection cannot be mistaken for data on a new one with the same port pair.

### Flow control vs congestion control
**Flow control** protects the *receiver*: the receiver advertises a window (`rwnd`) that bounds unacknowledged data in flight. **Congestion control** protects the *network*: the sender maintains `cwnd`, and the effective window is `min(cwnd, rwnd)`.

**Congestion control phases:**
1. **Slow start** — `cwnd` doubles every RTT (exponential) until it reaches `ssthresh`
2. **Congestion avoidance** — `cwnd` grows by one MSS per RTT (linear, "additive increase")
3. **On three duplicate ACKs** — fast retransmit and fast recovery: halve `cwnd` ("multiplicative decrease")
4. **On timeout** — treat as severe congestion: `ssthresh = cwnd/2`, `cwnd = 1`, restart slow start

The AIMD pattern is what makes TCP fair across competing flows.

---

## 4. The network layer

**IPv4 addressing.** 32 bits, written as four dotted octets. Classes A (/8), B (/16), C (/24) are historical; **CIDR** replaced them with an explicit prefix length.

**Subnetting arithmetic — the only method you need under time pressure:**
```
Host bits   = 32 − prefix
Hosts/subnet = 2^(host bits) − 2      (network + broadcast addresses are reserved)
Block size  = 256 − (the last non-zero octet of the mask)
Subnets start at multiples of the block size in that octet
```
For `/26` the mask is 255.255.255.192 → block size 64 → subnets at .0, .64, .128, .192, each with 62 usable hosts.

**Private ranges** (RFC 1918): 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16. **NAT** maps many private addresses onto one public address by rewriting the source IP and port, which is why a home network of twenty devices needs one public address.

**IPv6** is 128 bits, has no broadcast (it uses multicast and anycast), a fixed 40-byte header, and no header checksum — errors are left to the layers above and below.

**Fragmentation** happens when a packet exceeds the link MTU (1500 bytes on Ethernet). IPv4 routers may fragment; IPv6 routers may not — the source must do path-MTU discovery.

**Routing:**
- **Distance vector** (RIP): each router shares its whole table with neighbours; simple, slow to converge, suffers count-to-infinity (mitigated by split horizon and poison reverse)
- **Link state** (OSPF): each router floods its local links to everyone and runs Dijkstra on the full map; faster convergence, more memory and CPU
- **Path vector** (BGP): carries the full AS path, used between autonomous systems, policy-driven rather than shortest-path

---

## 5. The data link layer

**Framing, MAC addressing (48 bits, burned in), error detection, and media access.**

**Error detection:** parity (detects odd numbers of bit flips), checksum (used by IP and TCP), and **CRC** (polynomial division — detects all burst errors shorter than the generator, and is what Ethernet uses).

**Error correction:** with minimum Hamming distance `d`, a code can **detect** `d − 1` errors and **correct** `⌊(d−1)/2⌋`.

**ARQ protocols:**

| Protocol | Window | Retransmits | Efficiency |
|---|---|---|---|
| Stop-and-wait | 1 | the single frame | `1/(1 + 2a)` |
| Go-Back-N | N | the frame **and everything after it** | `N/(1 + 2a)` capped at 1 |
| Selective Repeat | N | only the lost frame | `N/(1 + 2a)` capped at 1 |

where `a = propagation time / transmission time`.

**Sequence-number constraint:** Go-Back-N needs `N ≤ 2ᵏ − 1`; Selective Repeat needs `N ≤ 2ᵏ⁻¹`. Selective Repeat's tighter bound exists because sender and receiver windows must not overlap ambiguously.

**Media access:** CSMA/CD (Ethernet — listen, transmit, detect collisions, back off exponentially) and CSMA/CA (Wi-Fi — collisions cannot be detected on radio, so avoid them with RTS/CTS and random backoff).

**ARP** maps an IP address to a MAC address on the local link, by broadcast. It is the glue between layers 3 and 2.

---

## 6. The application layer

**DNS** resolves names to addresses through a hierarchy: stub resolver → recursive resolver → root → TLD → authoritative. Records: A (IPv4), AAAA (IPv6), CNAME (alias), MX (mail), NS (nameserver), TXT. Runs over **UDP port 53** for ordinary queries, falling back to TCP for large responses and zone transfers. Caching at every level, governed by TTL, is what makes it viable.

**HTTP** is stateless and request–response.
- **1.1:** persistent connections, pipelining (rarely used), head-of-line blocking
- **2:** binary framing, multiplexed streams over one TCP connection, header compression, server push
- **3:** runs over **QUIC on UDP**, which removes TCP's head-of-line blocking and merges the transport and TLS handshakes

**Methods:** GET (safe, idempotent), POST (neither), PUT (idempotent), DELETE (idempotent), PATCH (not idempotent). **Status classes:** 1xx informational, 2xx success, 3xx redirection, 4xx client error, 5xx server error. Know 200, 201, 204, 301 vs 302, 304, 400, 401 vs 403, 404, 429, 500, 502, 503, 504.

**HTTPS / TLS handshake:** negotiate the cipher suite, the server presents a certificate signed by a CA, the client verifies the chain, both derive a shared symmetric key (via RSA key transport historically, ECDHE today for forward secrecy), and all subsequent traffic uses symmetric encryption. **Asymmetric crypto is used only to establish the symmetric key**, because it is orders of magnitude slower.

**Cookies vs sessions:** a cookie is client-side state sent with every request to its domain; a session is server-side state keyed by a cookie's session ID. JWTs move the state back to the client, signed rather than stored.

**Other ports worth memorising:** 20/21 FTP, 22 SSH, 23 Telnet, 25 SMTP, 53 DNS, 67/68 DHCP, 80 HTTP, 110 POP3, 143 IMAP, 443 HTTPS, 3306 MySQL, 5432 PostgreSQL, 6379 Redis, 27017 MongoDB.

---

## 7. What happens when you type google.com

Rehearse this as a three-minute answer; it is asked constantly.

1. **Browser cache → OS cache → hosts file** checked for the name.
2. **DNS resolution:** the stub resolver asks the configured recursive resolver, which walks root → TLD (.com) → authoritative nameserver, and caches the answer per its TTL.
3. **ARP** resolves the default gateway's MAC address if it is not already cached.
4. **TCP three-way handshake** to the resolved IP on port 443.
5. **TLS handshake:** certificate validation, key exchange, symmetric keys derived.
6. **HTTP GET** sent; the server responds, possibly 301-redirecting first.
7. **Routing** of every packet hop by hop: each router decrements TTL and forwards by longest-prefix match; NAT rewrites addresses at the home router.
8. **Browser renders:** parses HTML, builds the DOM, fetches CSS/JS/images (over the same connection under HTTP/2), builds the CSSOM and render tree, lays out and paints.
9. **Connection closed** or kept alive for reuse.

---

## 8. Recall questions

1. What is the difference between what the network layer and the transport layer address?
2. Precisely what does TCP guarantee?
3. Why is the handshake three-way and the teardown four-way?
4. What is TIME_WAIT for?
5. Flow control vs congestion control — who is being protected in each?
6. Name TCP's four congestion phases and what triggers the transition to each.
7. Give the block-size method for subnetting, and the usable-host formula.
8. Why does IPv6 have no header checksum?
9. Distance vector vs link state — one advantage each.
10. Give the efficiency formulas for stop-and-wait and Go-Back-N.
11. Why does Selective Repeat need `N ≤ 2ᵏ⁻¹` where Go-Back-N needs `N ≤ 2ᵏ − 1`?
12. Walk through the TLS handshake and say where asymmetric crypto is used.
13. What does HTTP/3 change, and what problem does it solve?
14. Walk through what happens when you type a URL.
