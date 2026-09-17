# Computer Networks — Rapid Fire Q&A

> Three sentences or fewer, in your own words.

**1. Why layering?** Each layer solves one problem and exposes a clean interface, so protocols can be replaced independently — swapping Ethernet for Wi-Fi changes nothing above layer 2.

**2. OSI vs TCP/IP?** OSI is the seven-layer reference model; TCP/IP is the four-layer model that was actually built and deployed. TCP/IP collapses OSI's session, presentation and application layers into one.

**3. What does each layer address?** Data link uses MAC addresses for the next hop, network uses IP addresses for the destination host, transport uses port numbers for the destination process.

**4. TCP vs UDP — when do you pick UDP?** When losing a packet matters less than waiting for it: live video, voice, gaming, and DNS lookups where a retry is cheaper than a handshake. TCP everywhere reliability and ordering matter.

**5. What exactly does TCP guarantee?** In-order, uncorrupted delivery of the byte stream, or an explicit failure notification. It cannot guarantee delivery over a severed network — nothing can.

**6. Why is the handshake three-way?** Both directions need an initial sequence number, and each must be acknowledged. Two messages would leave the server's number unacknowledged.

**7. Why four segments to close?** TCP is full-duplex and each direction closes independently, so each side sends its own FIN and receives its own ACK.

**8. What is TIME_WAIT for?** The closer waits two maximum segment lifetimes so that delayed duplicates from the old connection cannot be delivered into a new connection that reuses the same port pair.

**9. Flow control vs congestion control?** Flow control protects the receiver's buffer via the advertised window; congestion control protects the network via the congestion window. The sender uses the minimum of the two.

**10. Walk me through TCP congestion control.** Slow start doubles the window each RTT until ssthresh, then congestion avoidance adds one MSS per RTT. Three duplicate ACKs halve the window; a timeout collapses it to one and restarts slow start.

**11. Why is it called "slow" start?** Only relative to blasting at full rate immediately — it is actually the fastest-growing phase, doubling every round trip.

**12. What is the bandwidth-delay product?** Bandwidth times round-trip time: the amount of data that can be in flight. It is the minimum window needed to keep a link fully utilised, and it is why TCP window scaling exists.

**13. How does subnetting work?** The mask splits the address into a network part and a host part. Block size is 256 minus the last non-zero mask octet, subnets begin at multiples of that block, and usable hosts are 2^(host bits) − 2.

**14. Why are two addresses reserved per subnet?** The all-zeros host part is the network identifier and the all-ones host part is the directed broadcast address.

**15. What is NAT and why does it exist?** It rewrites private source addresses and ports to one public address, so many devices share a single routable IP. It was a stopgap for IPv4 exhaustion and it breaks end-to-end addressing, which is one reason IPv6 exists.

**16. Explain DNS resolution.** The stub resolver asks a recursive resolver, which walks root → TLD → authoritative unless a cached answer exists. Every level caches by TTL, which is what makes the system scale.

**17. Why does DNS use UDP?** A query and a reply are each one small datagram, so a handshake would triple the cost. It falls back to TCP above 512 bytes and for zone transfers.

**18. Distance vector vs link state?** Distance vector shares your whole table with neighbours — simple but slow to converge and prone to count-to-infinity. Link state floods your local links to everyone and each router runs Dijkstra — faster convergence for more memory and CPU.

**19. What does a router do with a packet?** Decrements TTL, discards it if TTL hits zero, does a longest-prefix-match lookup in the forwarding table, rewrites the layer-2 header for the next hop, and forwards it.

**20. What is MTU and what happens if a packet exceeds it?** The largest frame a link can carry, typically 1500 bytes on Ethernet. IPv4 routers may fragment; IPv6 routers may not, so the sender must discover the path MTU.

**21. Stop-and-wait vs sliding window?** Stop-and-wait sends one frame and waits, wasting the link whenever propagation delay exceeds transmission time. Sliding window keeps N frames in flight, and N ≥ 1 + 2a fully utilises the link.

**22. Go-Back-N vs Selective Repeat?** Go-Back-N retransmits the lost frame and everything after it, with a receiver window of one. Selective Repeat buffers out-of-order frames and retransmits only the lost one, at the cost of a more complex receiver and a tighter sequence-number bound.

**23. How does CRC detect errors?** The data is treated as a polynomial and divided modulo 2 by a generator; the remainder is appended. The receiver redoes the division and expects zero — a generator of degree r catches every burst error shorter than r.

**24. HTTP 1.1 vs 2 vs 3?** 1.1 added persistent connections but suffers head-of-line blocking. 2 adds binary framing, multiplexing and header compression over one TCP connection. 3 moves to QUIC over UDP, so a lost packet stalls only its own stream.

**25. Is HTTP stateless, and how do sessions work then?** Each request is independent. State is reconstructed from a cookie carrying a session ID that indexes server-side state, or from a signed token such as a JWT that carries the state itself.

**26. Explain the TLS handshake.** Client and server negotiate a cipher suite, the server presents a certificate that the client validates against a trusted CA chain, they perform an ECDHE key exchange, and all subsequent traffic is symmetrically encrypted.

**27. Why not use asymmetric encryption for everything?** It is orders of magnitude slower. Asymmetric crypto establishes trust and a shared key; symmetric crypto carries the data.

**28. What is forward secrecy?** Using an ephemeral key exchange so that each session's key is independent of the server's long-term private key. Compromising the server later cannot decrypt previously recorded sessions.

**29. GET vs POST vs PUT?** GET is safe and idempotent and should have no side effects. POST creates and is neither safe nor idempotent. PUT replaces and is idempotent — repeating it leaves the same state.

**30. What is a socket?** The endpoint identified by an IP address and a port; a TCP connection is uniquely identified by the four-tuple of source IP, source port, destination IP and destination port.

**31. Difference between a collision domain and a broadcast domain?** A hub makes one collision domain; a switch gives each port its own but keeps one broadcast domain; a router separates broadcast domains too.

**32. What does `traceroute` actually do?** Sends packets with TTL 1, 2, 3 and so on, and each router that decrements TTL to zero replies with an ICMP time-exceeded message, revealing itself.

**33. Why might a site be reachable by IP but not by name?** DNS resolution is failing — a bad resolver, an expired record, or a cache poisoned with a stale entry. `dig` or `nslookup` against a known-good resolver distinguishes the cases.

**34. What network work have you done in your own projects?**
*(Answer from your projects: an API you designed, a timeout or retry policy you tuned, a latency problem you traced. Concrete beats comprehensive.)*
