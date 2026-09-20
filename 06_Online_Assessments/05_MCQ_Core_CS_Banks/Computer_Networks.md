
# Computer Networks — Theory and MCQ Bank

> **Why it matters.** Networks is a guaranteed section for Cisco, VMware, NetApp, Juniper and most
> service companies, and appears in every general technical MCQ round. The questions cluster around
> the OSI/TCP-IP layers, TCP vs UDP, IP addressing and subnetting, and the common protocols and
> their port numbers.

---

# Part 1 — Theory

## 1.1 The OSI model ⭐⭐⭐

| # | Layer | Job | PDU | Devices | Protocols |
|---|---|---|---|---|---|
| 7 | **Application** | Interface to the user's application | Data | — | HTTP, FTP, SMTP, DNS, DHCP, SNMP, Telnet, SSH |
| 6 | **Presentation** | Translation, encryption, compression | Data | — | SSL/TLS, JPEG, ASCII, MPEG |
| 5 | **Session** | Establish, manage, terminate sessions | Data | — | NetBIOS, RPC, PPTP |
| 4 | **Transport** | End-to-end delivery, segmentation, flow and error control | **Segment** (TCP) / **Datagram** (UDP) | — | **TCP, UDP** ⭐ |
| 3 | **Network** | Logical addressing and routing across networks | **Packet** | **Router**, L3 switch | **IP**, ICMP, IGMP, OSPF, BGP, RIP ⭐ |
| 2 | **Data Link** | Node-to-node delivery, MAC addressing, framing, error detection | **Frame** | **Switch**, bridge, NIC | Ethernet, PPP, ARP*, HDLC |
| 1 | **Physical** | Bits on the wire | **Bit** | **Hub**, repeater, cable | Ethernet physical, DSL, USB |

**Mnemonic (top-down):** *All People Seem To Need Data Processing*
**Mnemonic (bottom-up):** *Please Do Not Throw Sausage Pizza Away*

`*` ARP sits between layers 2 and 3; most exam keys place it at **layer 2** (it produces link-layer
addresses) though some say layer 3. If offered both, choose layer 2 unless the paper says otherwise.

**TCP/IP model mapping ⭐:**
```
OSI 7,6,5  →  TCP/IP Application
OSI 4      →  TCP/IP Transport
OSI 3      →  TCP/IP Internet
OSI 2,1    →  TCP/IP Network Access (Link)
```

## 1.2 TCP vs UDP ⭐⭐⭐

| Feature | TCP | UDP |
|---|---|---|
| Connection | **Connection-oriented** (3-way handshake) | **Connectionless** |
| Reliability | Guaranteed delivery, ACKs, retransmission | **None** — fire and forget |
| Ordering | In-order delivery guaranteed | No ordering guarantee ⚠️ |
| Flow control | Yes (sliding window) | No |
| Congestion control | Yes (slow start, AIMD) | No |
| Header size | **20 bytes** minimum (up to 60) | **8 bytes** fixed ⭐ |
| Speed | Slower | Faster |
| Error checking | Checksum + recovery | Checksum only (detect, discard) |
| Use cases | HTTP/HTTPS, FTP, SMTP, SSH, file transfer | DNS, DHCP, VoIP, video streaming, online gaming, SNMP, TFTP ⭐ |

**The TCP 3-way handshake ⭐⭐⭐:**
```
Client                                Server
  │ ──── SYN (seq = x) ───────────────► │      state: SYN-SENT
  │ ◄─── SYN-ACK (seq = y, ack = x+1) ─ │      state: SYN-RECEIVED
  │ ──── ACK (ack = y+1) ─────────────► │      state: ESTABLISHED
```
**Connection termination is a 4-way handshake** (FIN, ACK, FIN, ACK) because each direction closes
independently — a half-close is legal. ⭐

⚠️ **TIME_WAIT** lasts `2 × MSL` (maximum segment lifetime) on the side that closes first, to
ensure the final ACK arrives and to prevent stale segments from a previous connection being
confused with a new one.

**TCP congestion control ⭐:**
```
Slow start       : cwnd doubles every RTT (exponential) until ssthresh
Congestion avoid : cwnd += 1 MSS per RTT (linear) — "additive increase"
On packet loss   : Tahoe → cwnd = 1;  Reno → cwnd halved — "multiplicative decrease"
⇒ the famous AIMD sawtooth
```

## 1.3 IP addressing and subnetting ⭐⭐⭐

**IPv4 classes:**

| Class | First octet | Default mask | Networks | Hosts per network |
|---|---|---|---|---|
| A | 1-126 | /8 (255.0.0.0) | 126 | 16,777,214 |
| B | 128-191 | /16 (255.255.0.0) | 16,384 | 65,534 |
| C | 192-223 | /24 (255.255.255.0) | 2,097,152 | 254 |
| D | 224-239 | — | Multicast | — |
| E | 240-255 | — | Experimental | — |

⚠️ `127.x.x.x` is reserved for **loopback**, which is why class A stops at 126.

**Private (RFC 1918) ranges — memorise ⭐⭐:**
```
10.0.0.0    – 10.255.255.255     (10.0.0.0/8)
172.16.0.0  – 172.31.255.255     (172.16.0.0/12)     ⚠️ 172.16 to 172.31 only
192.168.0.0 – 192.168.255.255    (192.168.0.0/16)
```

**Subnetting arithmetic ⭐⭐⭐:**
```
Given /n :
   host bits         = 32 − n
   total addresses   = 2^(32−n)
   USABLE hosts      = 2^(32−n) − 2      ⚠️ subtract network address and broadcast address
   number of subnets from a classful network borrowed b bits = 2^b
```

**The /n quick table (memorise the right-hand column):**

| CIDR | Mask | Usable hosts | Block size |
|---|---|---|---|
| /24 | 255.255.255.0 | 254 | 256 |
| /25 | 255.255.255.128 | 126 | 128 |
| /26 | 255.255.255.192 | 62 | 64 |
| /27 | 255.255.255.224 | 30 | 32 |
| /28 | 255.255.255.240 | 14 | 16 |
| /29 | 255.255.255.248 | 6 | 8 |
| /30 | 255.255.255.252 | **2** | 4 |
| /31 | 255.255.255.254 | 0 (2 with RFC 3021 for point-to-point) | 2 |
| /32 | 255.255.255.255 | 1 (a single host) | 1 |

⭐ `/30` is the standard mask for a router-to-router point-to-point link: exactly two usable
addresses.

## 1.4 Port numbers ⭐⭐⭐

Memorise these; they are free marks:

| Port | Protocol | | Port | Protocol |
|---|---|---|---|---|
| 20/21 | FTP (data/control) | | 110 | POP3 |
| 22 | SSH / SFTP | | 143 | IMAP |
| 23 | Telnet | | 161/162 | SNMP |
| 25 | SMTP | | 389 | LDAP |
| 53 | **DNS** (UDP, TCP for zone transfer) ⭐ | | 443 | **HTTPS** |
| 67/68 | DHCP (server/client) | | 445 | SMB |
| 69 | TFTP | | 3306 | MySQL |
| 80 | **HTTP** | | 5432 | PostgreSQL |
| 88 | Kerberos | | 6379 | Redis |

**Port ranges:** 0-1023 well-known, 1024-49151 registered, 49152-65535 dynamic/ephemeral.

## 1.5 Key protocols in one line each ⭐

```
ARP    : IP address → MAC address, within a LAN. Broadcast request, unicast reply. ⭐
RARP   : MAC → IP (obsolete; replaced by DHCP/BOOTP)
ICMP   : error and diagnostic messages. ping = Echo Request/Reply; traceroute uses TTL expiry ⭐
DHCP   : automatic IP configuration. DORA: Discover → Offer → Request → Acknowledge ⭐⭐
DNS    : name → IP. Hierarchical: root → TLD → authoritative. Recursive vs iterative queries.
         Record types: A (IPv4), AAAA (IPv6), CNAME (alias), MX (mail), NS, PTR (reverse), TXT ⭐
NAT    : private ↔ public address translation. PAT/NAPT multiplexes by port.
HTTP   : stateless request-response. Methods GET, POST, PUT, PATCH, DELETE, HEAD, OPTIONS.
         Status: 1xx info, 2xx success, 3xx redirect, 4xx client error, 5xx server error ⭐
HTTPS  : HTTP over TLS. TLS handshake → symmetric session key; certificates provide authentication
SSH    : encrypted remote shell, port 22
SMTP/POP3/IMAP : send / download-and-delete / sync-and-keep-on-server ⭐
```

## 1.6 Devices and their layers ⭐⭐

| Device | Layer | Behaviour |
|---|---|---|
| **Hub** | 1 | Repeats every bit to every port. One collision domain, one broadcast domain ⚠️ |
| **Switch** | 2 | Forwards by MAC using a CAM table. **One collision domain per port**, one broadcast domain (per VLAN) ⭐ |
| **Router** | 3 | Forwards by IP between networks. **Separates broadcast domains** ⭐⭐ |
| **Bridge** | 2 | Connects two LAN segments |
| **Gateway** | up to 7 | Protocol translation |
| **Repeater** | 1 | Regenerates the signal |

⭐ **The exam one-liner:** a switch with `n` ports creates `n` collision domains and **1** broadcast
domain; a router creates a separate broadcast domain per interface.

## 1.7 Error detection and framing (brief)
```
Parity          : detects odd numbers of bit errors only
Checksum        : sum of words, one's complement (used by IP, TCP, UDP)
CRC             : polynomial division; detects burst errors; used in Ethernet ⭐
Hamming code    : can CORRECT single-bit errors; needs r parity bits with 2^r ≥ m + r + 1
Sliding window  : Go-Back-N (retransmit from the lost frame) vs Selective Repeat (retransmit only
                  the lost one) ⭐  — window sizes: GBN ≤ 2^n − 1, SR ≤ 2^(n−1)
CSMA/CD         : Ethernet's classic access method (detect collisions, back off exponentially)
CSMA/CA         : Wi-Fi's method — avoidance, because collision detection is impractical on radio ⭐
```

---

# Part 2 — MCQ Bank (20 questions with full explanations)

---

**Q1.** Which OSI layer is responsible for **end-to-end** delivery between processes?
```
(a) Network    (b) Transport    (c) Data Link    (d) Session
```
<details><summary>Answer: (b) Transport</summary>

The transport layer provides **process-to-process** (end-to-end) delivery, identified by **port
numbers**. It handles segmentation, reassembly, and (for TCP) reliability and flow control.

**Why not (a):** the network layer provides **host-to-host** delivery using IP addresses — it gets
the packet to the right machine, not the right process.
**Why not (c):** the data link layer provides **node-to-node** delivery across a single link, using
MAC addresses.
</details>

---

**Q2.** Which is **not** a feature of UDP?
```
(a) Connectionless    (b) Low overhead    (c) Guaranteed delivery    (d) No congestion control
```
<details><summary>Answer: (c)</summary>

UDP provides **no delivery guarantee** — datagrams may be lost, duplicated or reordered, and UDP
will not retransmit. That is exactly the trade it makes for speed and low overhead (an 8-byte
header versus TCP's 20).

Applications that use UDP either tolerate loss (voice, video) or implement their own reliability
(QUIC, DNS retries).
</details>

---

**Q3.** How many usable host addresses are available in a `/27` subnet?
```
(a) 32    (b) 30    (c) 31    (d) 28
```
<details><summary>Answer: (b) 30</summary>

```
Host bits = 32 − 27 = 5
Total addresses = 2⁵ = 32
Usable = 32 − 2 = 30
```
⚠️ Two addresses are always reserved: the **network address** (all host bits 0) and the
**broadcast address** (all host bits 1). Forgetting the `−2` is the most common subnetting error.
</details>

---

**Q4.** The TCP three-way handshake consists of:
```
(a) SYN, ACK, FIN    (b) SYN, SYN-ACK, ACK    (c) SYN, ACK, SYN    (d) ACK, SYN, ACK
```
<details><summary>Answer: (b)</summary>

```
1. Client → Server : SYN      (seq = x)
2. Server → Client : SYN-ACK  (seq = y, ack = x + 1)
3. Client → Server : ACK      (ack = y + 1)
```
Both sides must synchronise sequence numbers, and each direction's SYN must be acknowledged — the
server combines its SYN with the ACK of the client's SYN, which is why it is three messages rather
than four.

⭐ Note that **termination** takes **four** messages (FIN, ACK, FIN, ACK), because each side closes
its direction independently and a half-open (half-closed) connection is legal.
</details>

---

**Q5.** Which protocol resolves an IP address to a MAC address?
```
(a) DNS    (b) DHCP    (c) ARP    (d) RARP
```
<details><summary>Answer: (c) ARP</summary>

ARP broadcasts *"who has 192.168.1.10?"* on the local segment and receives a unicast reply
containing the MAC address, which is then cached in the ARP table.

**Why not the others:**
- **DNS** resolves a **domain name** to an IP address.
- **DHCP** *assigns* an IP address to a host.
- **RARP** does the reverse (MAC → IP) and is obsolete, superseded by BOOTP/DHCP.
</details>

---

**Q6.** Which port does **HTTPS** use by default?
```
(a) 80    (b) 443    (c) 8080    (d) 22
```
<details><summary>Answer: (b) 443</summary>

80 is HTTP, 443 is HTTPS (HTTP over TLS), 22 is SSH, and 8080 is a common *alternative* HTTP port
used by application servers and proxies — it is a convention, not a registered standard for HTTPS.
</details>

---

**Q7.** A **router** operates at which OSI layer?
```
(a) 1    (b) 2    (c) 3    (d) 4
```
<details><summary>Answer: (c) Layer 3 (Network)</summary>

A router forwards packets between networks using **IP addresses** and a routing table, which is the
network layer's job.

**Contrast:** a **switch** is layer 2 (MAC addresses), a **hub** is layer 1 (raw bits). A "layer 3
switch" is a switch with routing capability — an exception that proves the rule.

⭐ The exam consequence: a router **separates broadcast domains**; a switch does not.
</details>

---

**Q8.** DHCP uses which sequence of messages?
```
(a) Request, Offer, Discover, Ack    (b) Discover, Offer, Request, Ack
(c) Offer, Discover, Ack, Request    (d) Discover, Request, Offer, Ack
```
<details><summary>Answer: (b) — DORA</summary>

```
DISCOVER : client broadcasts "is there a DHCP server?"       (from 0.0.0.0 to 255.255.255.255)
OFFER    : server offers an available address
REQUEST  : client broadcasts its acceptance of one offer     (broadcast, so other servers withdraw)
ACK      : server confirms the lease
```
Mnemonic: **DORA**. Ports 67 (server) and 68 (client), over UDP.
</details>

---

**Q9.** Which of the following IP addresses is **private**?
```
(a) 172.32.5.1    (b) 192.169.1.1    (c) 10.200.3.4    (d) 172.15.0.1
```
<details><summary>Answer: (c) 10.200.3.4</summary>

The private ranges are `10.0.0.0/8`, `172.16.0.0/12` and `192.168.0.0/16`.

**Why the others are public — these are deliberately near-misses ⚠️:**
- `172.32.5.1` — the 172 private range ends at **172.31**, not 172.32.
- `192.169.1.1` — the private range is `192.168`, not 192.169.
- `172.15.0.1` — the range *starts* at 172.16.

⭐ Recognising these off-by-one distractors is exactly what the question tests.
</details>

---

**Q10.** What does a **switch** use to forward frames?
```
(a) IP address    (b) MAC address    (c) Port number    (d) Domain name
```
<details><summary>Answer: (b) MAC address</summary>

A switch learns the source MAC of each incoming frame and records it against the ingress port in
its **CAM/MAC address table**. It then forwards frames to the single port associated with the
destination MAC.

If the destination MAC is unknown, the switch **floods** the frame to every port except the
incoming one — which is how it learns. Broadcast frames are always flooded.
</details>

---

**Q11.** Which protocol is used by the `ping` utility?
```
(a) TCP    (b) UDP    (c) ICMP    (d) ARP
```
<details><summary>Answer: (c) ICMP</summary>

`ping` sends an **ICMP Echo Request (type 8)** and expects an **Echo Reply (type 0)**. ICMP is a
network-layer protocol carried inside IP, and it has **no port numbers** — which is why firewalls
must block it by protocol rather than by port.

⭐ `traceroute` also uses ICMP (on Windows) or UDP with increasing TTL (classic Unix), relying on
the **ICMP Time Exceeded** message returned by each router when the TTL reaches zero.
</details>

---

**Q12.** The **minimum** size of a TCP header is:
```
(a) 8 bytes    (b) 20 bytes    (c) 40 bytes    (d) 60 bytes
```
<details><summary>Answer: (b) 20 bytes</summary>

The TCP header is 20 bytes without options and can extend to **60 bytes** with options (the
4-bit data-offset field counts 32-bit words, so 5-15 words = 20-60 bytes).

For comparison: **UDP is a fixed 8 bytes**; the **IPv4** header is 20-60 bytes; the **IPv6** header
is a fixed **40 bytes**. ⭐ These four numbers are worth memorising together.
</details>

---

**Q13.** Which HTTP status code class indicates a **client** error?
```
(a) 2xx    (b) 3xx    (c) 4xx    (d) 5xx
```
<details><summary>Answer: (c) 4xx</summary>

```
1xx Informational  (100 Continue, 101 Switching Protocols)
2xx Success        (200 OK, 201 Created, 204 No Content)
3xx Redirection    (301 Moved Permanently, 302 Found, 304 Not Modified)
4xx Client error   (400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found,
                    405 Method Not Allowed, 429 Too Many Requests)
5xx Server error   (500 Internal Server Error, 502 Bad Gateway, 503 Service Unavailable,
                    504 Gateway Timeout)
```
⚠️ Know the difference between **401 Unauthorized** (you are not authenticated) and **403
Forbidden** (you are authenticated but not permitted) — a favourite follow-up. ⭐
</details>

---

**Q14.** In Go-Back-N ARQ with an `n`-bit sequence number, the maximum sender window size is:
```
(a) 2ⁿ    (b) 2ⁿ − 1    (c) 2^(n−1)    (d) n
```
<details><summary>Answer: (b) 2ⁿ − 1</summary>

With `2ⁿ` distinct sequence numbers, the window must be at most `2ⁿ − 1` so that the receiver can
always distinguish a retransmission of an old frame from a new frame.

**Contrast with Selective Repeat**, where the window must be at most **`2^(n−1)`** — half the
sequence space — because the receiver buffers out-of-order frames and needs the sender and receiver
windows never to overlap ambiguously. ⭐ Being able to state both bounds *and* the reason is what
distinguishes a good answer.
</details>

---

**Q15.** Which layer adds the **MAC address** to a frame?
```
(a) Physical    (b) Data Link    (c) Network    (d) Transport
```
<details><summary>Answer: (b) Data Link</summary>

The data link layer performs framing and adds the source and destination **MAC addresses** plus a
trailer with an error-detecting CRC. The network layer adds **IP addresses**; the transport layer
adds **port numbers**.

⭐ The encapsulation picture worth memorising:
```
[ Frame header | [ IP header | [ TCP header | DATA ] ] | Frame trailer (CRC) ]
   MAC addrs       IP addrs      port numbers
```
</details>

---

**Q16.** How many collision domains does an 8-port **switch** create?
```
(a) 1    (b) 2    (c) 8    (d) 16
```
<details><summary>Answer: (c) 8</summary>

Each switch port is its own **collision domain** — that is the entire point of a switch, and why
switched Ethernet can run full-duplex with no collisions at all.

All 8 ports remain in **one broadcast domain** (unless VLANs divide them).

**Contrast with a hub:** an 8-port hub has **1** collision domain and **1** broadcast domain,
because it simply repeats bits to every port. ⭐
</details>

---

**Q17.** Which DNS record type maps a domain name to an **IPv6** address?
```
(a) A    (b) AAAA    (c) CNAME    (d) MX
```
<details><summary>Answer: (b) AAAA</summary>

```
A     : name → IPv4 address (32 bits)
AAAA  : name → IPv6 address (128 bits)    — "quad-A", four times the bits of an A record ⭐
CNAME : name → another name (alias)
MX    : mail exchange server for the domain (with a priority value)
NS    : authoritative name servers
PTR   : IP → name (reverse lookup)
TXT   : free-form text (SPF, DKIM, domain verification)
SOA   : start of authority — zone metadata
```
</details>

---

**Q18.** TCP's congestion-control algorithm follows which pattern?
```
(a) Additive increase, additive decrease
(b) Multiplicative increase, multiplicative decrease
(c) Additive increase, multiplicative decrease
(d) Multiplicative increase, additive decrease
```
<details><summary>Answer: (c) AIMD</summary>

In congestion avoidance, `cwnd` grows by roughly **one MSS per RTT** (additive increase); on
detecting loss, it is **halved** (multiplicative decrease). The result is the characteristic
sawtooth.

⚠️ Note that **slow start** is the exception: `cwnd` *doubles* every RTT (exponential growth) until
it reaches `ssthresh`, at which point congestion avoidance's additive increase takes over. The name
"slow start" refers to starting from a small window, not to a slow growth rate. ⭐
</details>

---

**Q19.** Which of the following is a **connectionless** protocol at the network layer?
```
(a) TCP    (b) IP    (c) SSH    (d) FTP
```
<details><summary>Answer: (b) IP</summary>

**IP is connectionless and unreliable** — it makes a best-effort attempt to deliver each datagram
independently, with no handshake, no ordering and no retransmission. Reliability, when needed, is
provided *above* it by TCP.

**Why not the others:** TCP is connection-oriented (layer 4); SSH and FTP are application-layer
protocols that run over TCP.

⭐ The phrase to remember: **"IP is best-effort; TCP makes it reliable."**
</details>

---

**Q20.** A host has IP `192.168.10.75` with mask `255.255.255.192`. What is its network address and
broadcast address?
```
(a) Network 192.168.10.64, broadcast 192.168.10.127
(b) Network 192.168.10.0,  broadcast 192.168.10.255
(c) Network 192.168.10.72, broadcast 192.168.10.79
(d) Network 192.168.10.64, broadcast 192.168.10.255
```
<details><summary>Answer: (a)</summary>

```
Mask 255.255.255.192 = /26   ⇒  host bits = 6,  block size = 2⁶ = 64
Subnets in the last octet start at:  0, 64, 128, 192

75 falls in the block starting at 64:
   Network address   = 192.168.10.64
   Usable hosts      = 192.168.10.65  to  192.168.10.126     (62 hosts)
   Broadcast address = 192.168.10.127
```

⭐ **The method to use every time:**
```
1. Convert the mask to CIDR and compute the BLOCK SIZE = 256 − (last non-255 mask octet)
   Here: 256 − 192 = 64
2. List the block boundaries: 0, 64, 128, 192
3. Find which block contains the host's octet.
4. Network = block start;  Broadcast = next block start − 1;  Hosts = everything between.
```
This takes about ten seconds once practised, and it answers every subnetting question in the paper.
</details>

---

## Scoring

| Correct | Read as |
|---|---|
| 18-20 | Networks is interview-ready |
| 14-17 | Drill subnetting and the port table |
| 10-13 | Re-read Part 1, especially the layer table and TCP/UDP |
| < 10 | Half a day on the layer model and addressing |

---

## Recall questions

1. List the seven OSI layers with their PDU and one device each.
2. Give eight differences between TCP and UDP.
3. Draw the three-way handshake with sequence numbers, and explain why teardown takes four steps.
4. State the three private IPv4 ranges exactly.
5. Give the block-size method for subnetting and apply it to `/28`.
6. Recite the port numbers for DNS, SSH, SMTP, HTTP, HTTPS, DHCP and MySQL.
7. Explain DORA.
8. How many collision and broadcast domains does an 8-port switch create? A hub? A router?
9. State the window-size bounds for Go-Back-N and Selective Repeat, with the reason.
10. Explain AIMD and how slow start differs from it.
