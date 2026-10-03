
# Computer Networks — One Pager

> **Use:** every core-CS round. The highest-frequency questions are a narrow set: TCP vs UDP,
> the three-way handshake, "what happens when you type a URL", and TCP congestion control.
> Know those four cold before anything else. ⭐

---

## 1. The layer models ⭐⭐

| OSI | TCP/IP | Unit | Protocols | Device |
|---|---|---|---|---|
| 7 Application | Application | message | HTTP, DNS, SMTP, FTP, SSH, DHCP | — |
| 6 Presentation | — | — | TLS/SSL, encoding | — |
| 5 Session | — | — | — | — |
| 4 Transport | Transport | **segment** | **TCP, UDP**, QUIC | — |
| 3 Network | Internet | **packet** | **IP**, ICMP, ARP*, OSPF, BGP | **Router** |
| 2 Data link | Link | **frame** | Ethernet, Wi-Fi, PPP | **Switch**, bridge |
| 1 Physical | Link | bit | cables, radio | Hub, repeater |

\*ARP straddles layers 2-3. **Hub** = layer 1 broadcast; **switch** = layer 2, MAC table;
**router** = layer 3, routing table; **gateway** = protocol translation.

**Encapsulation:** each layer prepends its header going down, strips it going up.

---

## 2. TCP vs UDP ⭐⭐⭐

| | TCP | UDP |
|---|---|---|
| Connection | Connection-oriented (handshake) | Connectionless |
| Reliability | Acks, retransmission, sequencing | None — fire and forget |
| Ordering | Guaranteed | Not guaranteed |
| Flow control | Sliding window | None |
| Congestion control | Yes | None ⚠️ can swamp a network |
| Header | 20-60 B | **8 B** |
| Speed / latency | Slower, head-of-line blocking | Faster, no setup |
| Duplicate detection | Yes | No |
| Uses | HTTP(S), SSH, SMTP, file transfer | DNS, DHCP, VoIP, video, games, QUIC ⭐ |

**UDP header (8 B):** source port · dest port · length · checksum. That is the whole list.

### Three-way handshake ⭐⭐⭐
```
Client → SYN (seq = x)
Server → SYN-ACK (seq = y, ack = x+1)
Client → ACK (ack = y+1)
```
**Why three and not two:** both sides must agree on *both* initial sequence numbers, and the
third message confirms the server's ISN reached the client. Two would leave the server's
sequence number unconfirmed and permit duplicate/stale connection requests. ⭐

### Four-way teardown
```
FIN → ACK → FIN → ACK, then TIME_WAIT (2×MSL) on the closer.
TIME_WAIT exists so a delayed duplicate segment cannot be delivered to a new connection
reusing the same port pair, and so the final ACK can be retransmitted. ⭐
```
**States:** CLOSED · LISTEN · SYN_SENT · SYN_RECEIVED · ESTABLISHED · FIN_WAIT_1/2 ·
CLOSE_WAIT · LAST_ACK · TIME_WAIT.
**SYN flood:** half-open connections exhaust the backlog; mitigated with SYN cookies. ⚠️

---

## 3. TCP reliability and control ⭐⭐

```
FLOW CONTROL     : protects the RECEIVER. Advertised window (rwnd) in every ACK.
CONGESTION CTRL  : protects the NETWORK. cwnd, maintained by the sender.
Effective window = min(cwnd, rwnd)                      ⭐ know which protects what
```

### Congestion control phases ⭐⭐⭐
```
1. SLOW START      : cwnd starts at 1 MSS, DOUBLES each RTT (exponential) until ssthresh
2. CONGESTION AVOID: additive increase, +1 MSS per RTT (linear)
3. FAST RETRANSMIT : 3 duplicate ACKs → retransmit immediately, don't wait for timeout
4. FAST RECOVERY   : ssthresh = cwnd/2, cwnd = ssthresh (TCP Reno halves — AIMD)
   TIMEOUT         : more severe — ssthresh = cwnd/2, cwnd = 1, back to slow start ⚠️
```
> **AIMD** = additive increase, multiplicative decrease. Modern variants: **CUBIC** (Linux
> default) and **BBR** (models bandwidth and RTT rather than treating loss as congestion). ⭐

```
Nagle's algorithm   : coalesce small writes — adds latency. TCP_NODELAY disables it ⭐
Delayed ACK         : wait ~200 ms to piggyback. Nagle + delayed ACK interact badly ⚠️
Silly window syndrome: tiny windows; fixed by Clark's solution
Selective ACK (SACK): acknowledge non-contiguous blocks
Karn's algorithm    : don't use retransmitted segments for RTT estimation
RTO                 : EstimatedRTT = (1−α)·Est + α·Sample; RTO = Est + 4·DevRTT
```

---

## 4. IP addressing and subnetting ⭐⭐

```
IPv4: 32 bits, dotted decimal. IPv6: 128 bits, hex, colon-separated.
CIDR /n → n network bits, (32−n) host bits → 2^(32−n) addresses, minus 2 usable
         (network address and broadcast address). ⭐
```

| CIDR | Mask | Addresses | Usable hosts |
|---|---|---|---|
| /24 | 255.255.255.0 | 256 | 254 |
| /25 | 255.255.255.128 | 128 | 126 |
| /26 | 255.255.255.192 | 64 | 62 |
| /27 | 255.255.255.224 | 32 | 30 |
| /28 | 255.255.255.240 | 16 | 14 |
| /30 | 255.255.255.252 | 4 | 2 (point-to-point links) |

📐 **Worked subnetting:** `192.168.10.0/26`. Host bits = 6 → block size 64.
Subnets: `.0–.63`, `.64–.127`, `.128–.191`, `.192–.255`.
For `.0/26`: network `192.168.10.0`, broadcast `192.168.10.63`, usable `.1–.62`, 62 hosts.
A host `192.168.10.100` falls in the `.64/26` subnet (since 64 ≤ 100 < 128).

**Private ranges:** `10/8` · `172.16/12` · `192.168/16`. **Loopback:** `127/8`.
**Link-local:** `169.254/16` (DHCP failure). **Classes:** A /8, B /16, C /24 (historical).
**NAT:** maps many private addresses to one public one using port translation (PAT).
**VLSM / supernetting:** variable-length masks / route aggregation to shrink routing tables.

---

## 5. What happens when you type a URL ⭐⭐⭐ (rehearse this out loud)

```
 1. Browser cache / service worker checked
 2. DNS resolution: browser cache → OS cache → hosts file → resolver → root → TLD → authoritative
    (recursive vs iterative; records A, AAAA, CNAME, MX, NS, TXT; TTL caching) ⭐
 3. ARP to find the gateway's MAC (if not cached)
 4. TCP three-way handshake to the server IP on port 443
 5. TLS handshake: ClientHello → ServerHello + certificate → key exchange (ECDHE) →
    Finished. Certificate validated against the trust store. Session keys derived ⭐
 6. HTTP GET sent (method, path, headers, cookies)
 7. Server/load balancer → application → database → response
 8. HTTP response: status line, headers, body. Possibly 3xx redirect → repeat
 9. Browser parses HTML → DOM, CSS → CSSOM, JS executes → render tree → layout → paint
10. Subresources fetched (multiplexed on HTTP/2 or QUIC streams)
11. Connection kept alive or closed
```

---

## 6. HTTP ⭐⭐

| Method | Safe | Idempotent | Note |
|---|---|---|---|
| GET | ✅ | ✅ | Cacheable |
| HEAD | ✅ | ✅ | Headers only |
| POST | ❌ | ❌ | Create |
| PUT | ❌ | ✅ | Replace whole resource ⭐ |
| PATCH | ❌ | ❌ | Partial update |
| DELETE | ❌ | ✅ | |
| OPTIONS | ✅ | ✅ | CORS preflight |

```
1xx info · 2xx success (200 OK, 201 Created, 204 No Content)
3xx redirect (301 permanent, 302 found, 304 Not Modified ⭐)
4xx client (400, 401 unauthenticated, 403 unauthorised, 404, 409 conflict, 429 rate limited)
5xx server (500, 502 bad gateway, 503 unavailable, 504 timeout)
```

| Version | Change |
|---|---|
| HTTP/1.0 | New TCP connection per request |
| HTTP/1.1 | Keep-alive, pipelining, Host header, chunked encoding ⚠️ head-of-line blocking |
| **HTTP/2** | Binary framing, **multiplexed streams**, HPACK header compression, server push ⭐ |
| **HTTP/3** | Over **QUIC/UDP** — removes TCP head-of-line blocking, 0-RTT resumption ⭐ |

```
Caching: Cache-Control (max-age, no-store, private) · ETag + If-None-Match → 304 · Last-Modified
Cookies vs sessions vs JWT: server-set state · server-side store · signed stateless token
CORS: browser-enforced same-origin policy; preflight OPTIONS for non-simple requests
REST vs GraphQL vs gRPC: resource URLs · one flexible query · binary HTTP/2 RPC with protobuf ⭐
WebSocket: HTTP Upgrade → full-duplex persistent connection
```

---

## 7. DNS, security and the rest

```
DNS: UDP/53 (TCP for large responses and zone transfers). Recursive resolver walks
     root → TLD → authoritative. Records: A, AAAA, CNAME, MX, NS, TXT, SOA, PTR.
     Caching by TTL at every level. DNS round-robin as crude load balancing ⭐
DHCP: DORA — Discover, Offer, Request, Acknowledge (UDP broadcast)
ARP : IP → MAC within a broadcast domain. ARP spoofing is a layer-2 attack ⚠️
ICMP: ping (echo request/reply), traceroute (TTL expiry), destination unreachable
NAT · VPN · firewall (stateless packet filter vs stateful) · proxy (forward vs reverse) ⭐

Symmetric vs asymmetric: one shared key (fast, AES) vs key pair (slow, RSA/ECC).
TLS uses asymmetric to exchange a symmetric session key. ⭐
Hash (SHA-256) ≠ encryption — one way. HMAC for integrity + authenticity.
Digital signature: hash, then encrypt with the private key.
Certificate / CA / chain of trust. HTTPS = HTTP over TLS, port 443.
Attacks: MITM · DDoS · SQL injection · XSS · CSRF · replay · DNS poisoning
```

### Routing
```
Link state (OSPF, Dijkstra) vs distance vector (RIP, Bellman-Ford, count-to-infinity ⚠️)
Interior (OSPF, IS-IS) vs exterior (BGP — path vector, the internet's routing protocol) ⭐
```

### Error and reliability at layer 2
```
Checksum (weak) · CRC (standard in Ethernet) · Hamming code (corrects single-bit errors)
Stop-and-wait · Go-Back-N (cumulative ACK, retransmit window) · Selective Repeat
Window-size limits: GBN ≤ 2ⁿ−1, SR ≤ 2^(n−1) for an n-bit sequence number ⭐
CSMA/CD (Ethernet, collision detection) vs CSMA/CA (Wi-Fi, avoidance + RTS/CTS)
```

📐 **Throughput of a sliding window:** `Efficiency = W / (1 + 2a)` where `a = Tp/Tt`
(propagation / transmission time), W = window size. For stop-and-wait, W = 1.

---

## Recall questions
1. Why is the handshake three-way and not two-way?
2. What does TIME_WAIT protect against, and how long does it last?
3. Flow control vs congestion control — which protects whom?
4. Name the four congestion-control phases and what cwnd does in each.
5. `192.168.10.0/26` — how many usable hosts, and what is the broadcast address?
6. Walk through what happens when you type a URL, in 10 steps.
7. HTTP/2 vs HTTP/3 — what problem does each solve?
8. Which HTTP methods are idempotent, and why does it matter for retries?
