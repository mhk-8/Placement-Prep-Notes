# Computer Networks — Flashcards

## Questions

1. What does each of the data link, network and transport layers address?
2. At which layers do a hub, a switch and a router operate?
3. Precisely what does TCP guarantee?
4. Why three segments to open a connection and four to close it?
5. What is TIME_WAIT for, and how long does it last?
6. Flow control vs congestion control — who is protected in each, and what is the effective window?
7. Name TCP's congestion phases and the trigger for each transition.
8. What is the difference in response between three duplicate ACKs and a timeout?
9. Minimum TCP and UDP header sizes?
10. Give the block-size subnetting method and the usable-host formula.
11. How many usable hosts in /26, /27, /28, /30?
12. List the three RFC 1918 private ranges.
13. Give the stop-and-wait and sliding-window efficiency formulas, and define `a`.
14. Minimum window size for full utilisation?
15. Sequence-number bounds for Go-Back-N and Selective Repeat, and why they differ.
16. How does CRC work, and what does a degree-r generator guarantee?
17. With minimum Hamming distance d, how many errors can be detected and corrected?
18. Why does DNS use UDP, and when does it use TCP?
19. Distance vector vs link state — one advantage each.
20. What does a router do to each packet it forwards?
21. HTTP/1.1 vs 2 vs 3 in one line each.
22. Which HTTP methods are idempotent, and which is also safe?
23. What does 301 vs 302 vs 304 mean?
24. Where is asymmetric cryptography used in TLS, and why only there?
25. What is forward secrecy and which key exchange provides it?

---

## Answers

1. Data link: MAC address, next hop. Network: IP address, destination host. Transport: port number, destination process.
2. Hub: physical (layer 1). Switch: data link (layer 2, MAC). Router: network (layer 3, IP).
3. In-order, uncorrupted delivery of the byte stream, or an explicit failure notification to the application. Not delivery itself, which no protocol can promise.
4. Both directions need an initial sequence number acknowledged, which takes three messages. Closing is per-direction because TCP is full-duplex, so each side sends its own FIN and gets its own ACK.
5. So that delayed duplicate segments from the closed connection cannot be delivered into a new connection reusing the same four-tuple. It lasts 2 × MSL.
6. Flow control protects the receiver's buffer (`rwnd`); congestion control protects the network (`cwnd`). The sender uses `min(cwnd, rwnd)`.
7. Slow start (exponential) until `ssthresh`; congestion avoidance (linear, +1 MSS/RTT); fast retransmit and fast recovery on three duplicate ACKs; slow start again after a timeout.
8. Three duplicate ACKs mean mild congestion — halve `cwnd` and retransmit. A timeout means severe congestion — `ssthresh = cwnd/2`, `cwnd = 1`, restart slow start.
9. TCP 20 bytes (up to 60 with options); UDP 8 bytes.
10. Block size = 256 − last non-zero mask octet; subnets start at multiples of that block; usable hosts = 2^(32 − prefix) − 2.
11. /26 → 62, /27 → 30, /28 → 14, /30 → 2.
12. 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16.
13. `a = Tp/Tt`. Stop-and-wait: `η = 1/(1 + 2a)`. Sliding window: `η = min(1, N/(1 + 2a))`.
14. `N ≥ 1 + 2a`.
15. Go-Back-N: `N ≤ 2ᵏ − 1`. Selective Repeat: `N ≤ 2ᵏ⁻¹`. SR is tighter because the receiver also keeps a window of N, and the two windows must not overlap ambiguously.
16. Treat the data as a polynomial, append r zeros, divide modulo 2 by the generator, and send the remainder as the check bits. A degree-r generator detects all single-bit errors and all burst errors of length ≤ r.
17. Detect d − 1; correct ⌊(d − 1)/2⌋.
18. Queries and replies are single small datagrams, so a handshake would dominate the cost. It falls back to TCP for responses over 512 bytes and for zone transfers.
19. Distance vector is simple and low-memory; link state converges faster and is immune to count-to-infinity.
20. Decrements TTL (discarding at zero and returning ICMP), looks up the destination by longest-prefix match, rewrites the layer-2 header for the next hop, and forwards.
21. 1.1: persistent connections, textual, head-of-line blocking. 2: binary framing, multiplexed streams, header compression over one TCP connection. 3: QUIC over UDP, so loss stalls only the affected stream, with a faster combined handshake.
22. Idempotent: GET, PUT, DELETE (and HEAD, OPTIONS). GET is additionally safe — no side effects at all.
23. 301 permanent redirect, 302 temporary redirect, 304 not modified (cache validation, no body).
24. Only in the handshake — server authentication via the certificate, and the key exchange. Asymmetric operations are orders of magnitude slower, so bulk data uses the derived symmetric key.
25. Each session uses an ephemeral key pair, so compromising the server's long-term private key later cannot decrypt recorded sessions. ECDHE provides it.
