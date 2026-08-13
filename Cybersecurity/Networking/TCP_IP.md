- TCP/IP is the fundamental protocol suite of the internet and most modern networks. It is named after two of its most important protocols: [[TCP]] (Transmission Control Protocol) and [[IP]] (Internet Protocol), but the suite contains many protocols working at different layers.
## The Layers
- TCP/IP is described with a 4-layer model:

| TCP/IP Layer              | Role                                         | Example Protocols                      |
| ------------------------- | -------------------------------------------- | -------------------------------------- |
| **Application**           | User-facing services, data formatting        | HTTP, HTTPS, DNS, DHCP, FTP, SMTP, SSH |
| **Transport**             | End-to-end communication, reliability, ports | TCP, UDP                               |
| **Internet**              | Addressing, routing, packet forwarding       | IP, ICMP, ARP, IGMP                    |
| **Network Access / Link** | Physical hardware, local network delivery    | Ethernet, Wi-Fi, PPP, MAC addresses    |
- Compare to the OSI 7-layer model:

| OSI Layer      | TCP/IP Mapping |
| -------------- | -------------- |
| 7 Application  | Application    |
| 6 Presentation | Application    |
| 5 Session      | Application    |
| 4 Transport    | Transport      |
| 3 Network      | Internet       |
| 2 Data Link    | Network Access |
| 1 Physical     | Network Access |
## Encapsulation
- Data travels down the layers at the sender and up the layers at the receiver. Each layer adds its own header and sometimes trailer:
- **Segment**: Transport layer data unit (TCP/[[UDP]] header + payload).
- **Packet/Datagram**: Internet layer data unit (IP header + segment).
- **Frame**: Link layer data unit (Ethernet header + packet + trailer).
- Each layer only understands its own header and passes the payload upward after stripping its header.
## 1. Network Access Layer (Link Layer)
- This layer handles physical addressing and delivery on a local network.
### Ethernet & MAC Addresses
- MAC Address: 48-bit physical address, usually written as six hex pairs like `00:1A:2B:3C:4D:5E`.
- Ethernet frames contain:
	- Destination MAC
	- Source MAC
	- EtherType field (`0x0800` for IPv4, `0x86DD` for IPv6)
	- Payload
	- Frame Check Sequence (FCS) for error detection
- Switches learn which MAC addresses are reachable on which ports and forward frames accordingly.
### [[Cybersecurity/Networking/Protocols/ARP]]: Address Resolution Protocol
- ARP maps an IPv4 address to a MAC address on a local network.
- Host sends an ARP request (broadcast) asking  "Who has IP `192.168.1.10`? Tell `192.168.1.5`."
- The owner replies with an ARP reply (unicast) containing its MAC address.
- The sender caches the result in its ARP table.
- For IPv6, ARP is replaced by Neighbor Discovery Protocol (NDP) using ICMPv6.
## 2. Internet Layer: IP
- The internet layer provides connectionless, best-effort delivery of packets. IP does not guarantee delivery, order, or integrity, that is left to upper layers.
### IPv4 Header

|Field|Bits|Description|
|---|---|---|
|Version|4|IP version (4 for IPv4)|
|IHL|4|Header length in 32-bit words (minimum 5 = 20 bytes)|
|DSCP / ECN|8|Differentiated Services / Explicit Congestion Notification|
|Total Length|16|Total packet length in bytes|
|Identification|16|Used for fragmentation reassembly|
|Flags|3|DF (Don't Fragment), MF (More Fragments)|
|Fragment Offset|13|Offset of fragment in 8-byte units|
|TTL|8|Time To Live, decremented at each router; packet dropped when 0|
|Protocol|8|Next-layer protocol (6=TCP, 17=UDP, 1=ICMP)|
|Header Checksum|16|Error check for IP header only|
|Source IP|32|Sender address|
|Destination IP|32|Receiver address|
|Options|variable|Optional, rarely used|
- Typical IPv4 header size is 20 bytes.
### IPv6 Header
- IPv6 has a simplified header of 40 bytes:
	- Version (4 bits)
	- Traffic Class (8)
	- Flow Label (20)
	- Payload Length (16)
	- Next Header (8): Replaces protocol field, allows extension headers
	- Hop Limit (8): Equivalent to TTL
	- Source Address (128)
	- Destination Address (128)
- IPv6 routers do not fragment packets; fragmentation is done only by the source using extension headers.
### IP Addressing
#### IPv4
IPv4 addresses are 32-bit numbers, written in dotted decimal: `192.168.1.100`.
- **Network portion** and **host portion** separated by subnet mask or prefix length.
- Example: `192.168.1.0/24` means network part is 24 bits, host part is 8 bits.
- Hosts per subnet = `2^(32 - prefix) - 2` (subtract network and broadcast addresses).
- Legacy classes (A, B, C) are replaced by **CIDR** (Classless Inter-Domain Routing).
**Private IPv4 addresses (RFC 1918):**
- `10.0.0.0/8`
- `172.16.0.0/12`
- `192.168.0.0/16`
Other special ranges:
- Loopback: `127.0.0.0/8`
- Link-local (APIPA): `169.254.0.0/16`
- Multicast: `224.0.0.0/4`
#### IPv6
IPv6 addresses are 128-bit, written in hex groups separated by colons, e.g. `2001:0db8:0000:0000:0000:ff00:0042:8329`.
Compression rules:
- Leading zeros in a group can be omitted.
- One or more consecutive all-zero groups can be replaced with `::` (once only).
IPv6 address types:
- **Global unicast**: `2000::/3`
- **Link-local**: `fe80::/10`
- **Unique local**: `fc00::/7`
- **Loopback**: `::1`
- **Multicast**: `ff00::/8`
No broadcast in IPv6; multicast is used instead.
### Subnetting & CIDR
CIDR allows flexible network prefixes. Example:
Network `192.168.1.0/24` can be split into four `/26` subnets:
- `192.168.1.0/26` – hosts `192.168.1.1 – 192.168.1.62`
- `192.168.1.64/26` – hosts `192.168.1.65 – 192.168.1.126`
- `192.168.1.128/26` – hosts `192.168.1.129 – 192.168.1.190`
- `192.168.1.192/26` – hosts `192.168.1.193 – 192.168.1.254`
Each `/26` has 62 usable host addresses.
### Routing
- Routers forward packets based on the destination IP address using a routing table.
- A routing table entry typically contains:
	- Destination network (`10.0.0.0/8`)
	- Next-hop router IP
	- Outgoing interface
	- Metric/cost
- Routers use longest prefix match to choose the most specific route.
- Common routes:
	- Default route: `0.0.0.0/0`: Used when no other route matches.
	- Directly connected networks.
- Routing protocols:
	- **OSPF** – link-state, interior gateway protocol, uses Dijkstra’s algorithm.
	- **BGP** – path-vector, exterior gateway protocol, used between ISPs.
	- **RIP** – distance-vector, older, hop count limit 15.
	- **EIGRP** – Cisco proprietary, advanced distance-vector.
### NAT: Network Address Translation
- NAT allows multiple private IP addresses to share one public IP.
- Static NAT: One-to-one mapping.
- Dynamic NAT: Pool of public addresses.
- PAT (Port Address Translation) / NAT Overload: Many pvt IPs share one public IP by mapping different source port numbers.
- NAT maintains a state table and rewrites source/destination IPs and ports. It provides some basic obscurity but is not a security mechanism by itself.
### [[ICMP]]: Internet Control Message Protocol
ICMP is used for error reporting and diagnostics.
Common types:
- **Echo Request (Type 8)** and **Echo Reply (Type 0)** – used by `ping`.
- **Destination Unreachable (Type 3)** – network, host, port unreachable, etc.
- **Time Exceeded (Type 11)** – TTL expired, used by `traceroute`.
- **Redirect (Type 5)** – better route available.
### Fragmentation & MTU
- **MTU** (Maximum Transmission Unit) is the largest packet size a link can carry (Ethernet default is 1500 bytes).
- If a packet is larger than the MTU, IP can fragment it into smaller packets using the Identification, Flags, and Fragment Offset fields.
- **Path MTU Discovery (PMTUD)** uses the DF bit: if a router cannot forward a packet without fragmenting and DF is set, it sends an ICMP "Fragmentation Needed" message. The sender then reduces packet size.
## 3. Transport Layer: UDP & TCP
- The transport layer provides end-to-end communication between apps using ports.
### Ports & Sockets
- Port numbers are 16-bit: `9-65535`.
- Well-known ports: `0-1023`.
- Registered ports: `1024-49151`.
- Ephemeral ports: `49152-65535`.
- A socket is the combination of IP address + Port number. A connection is defined by the pair: `(source IP, source port, destination IP, destination port)`.
### UDP: User Datagram Protocol
- UDP is a minimal, connectionless transport protocol.
- Header (8 bytes):
	- Source Port (16 bits)
	- Destination Port (16)
	- Length (16): Header + Data Length
	- Checksum (16): Optional in IPv4, mandatory in IPv6
- UDP provides:
	- No connection establishment
	- No reliability
	- No ordering
	- No flow or congestion control
- Used in: [[DNS]], [[DHCP]], [[VoIP]], real-time streaming, online gaming, [[QUIC_HTTP3|QUIC/HTTP3]].
### TCP: Transmission Control Protocol
- TCP provides a connection-oriented, reliable, byte-stream service with flow control and congestion control.
#### TCP Headers
|Field|Bits|Description|
|---|---|---|
|Source Port|16|Sending application port|
|Destination Port|16|Receiving application port|
|Sequence Number|32|Byte offset of first data byte in this segment|
|Acknowledgment Number|32|Next expected byte from the other side|
|Data Offset|4|Header length in 32-bit words (min 5 = 20 bytes)|
|Reserved|3|Reserved bits|
|Flags|9|NS, CWR, ECE, URG, ACK, PSH, RST, SYN, FIN|
|Window Size|16|Receiver's advertised flow control window|
|Checksum|16|Checksum over pseudo-header + header + data|
|Urgent Pointer|16|Offset to urgent data (rarely used)|
|Options|variable|MSS, Window Scale, SACK, Timestamps, etc.|
- Minimum header size is **20 bytes**, maximum 60 bytes.
#### Sequence & Acknowledgment Numbers
- TCP treats data as a stream of bytes.
- The **Sequence Number** is the byte offset of the first data byte in the segment.
- The **Acknowledgment Number** is the next byte the receiver expects to receive.
- Initial Sequence Numbers (ISNs) are random to prevent spoofing attacks.
- Example:
	- Host A sends 100 bytes with seq=1.
	- Host B ACKs with ack=101 (next expected byte).
#### Connection Establishment: 3-Way Handshake
```
Client                              Server
  |                                    |
  |--- SYN, seq=x -------------------->|
  |                                    |
  |<-- SYN+ACK, seq=y, ack=x+1 -------|
  |                                    |
  |--- ACK, seq=x+1, ack=y+1 -------->|
  |                                    |
  |        ESTABLISHED                 |
```
- **SYN** and **FIN** consume one sequence number each.
- The handshake synchronizes ISNs and confirms both directions work.
- Connection states:
	- Server starts in `LISTEN`.
	- Client sends SYN → `SYN-SENT`.
	- Server receives SYN → `SYN-RECEIVED`, sends SYN+ACK.
	- Client receives SYN+ACK → `ESTABLISHED`, sends ACK.
	- Server receives ACK → `ESTABLISHED`.
#### Connection Termination: 4-Way Handshake
- TCP is full-duplex, so each direction is closed independently:
```
Client                              Server
  |                                    |
  |--- FIN, seq=m -------------------->|
  |<-- ACK, ack=m+1 ------------------|
  |                                    |
  |<-- FIN, seq=n ---------------------|
  |--- ACK, ack=n+1 ----------------->|
  |                                    |
```
- The side that sends the first FIN enters **TIME-WAIT** and waits for **2 MSL** (Maximum Segment Lifetime, usually 30–120 seconds) to ensure the final ACK is received and old duplicate segments expire.
- Other TCP states:
	- `CLOSE-WAIT`: waiting for application to close.
	- `LAST-ACK`: waiting for final ACK after sending FIN.
	- `FIN-WAIT-1`, `FIN-WAIT-2`, `CLOSING`, `TIME-WAIT`.
#### Reliability: Sliding Window & Retransmission
TCP uses:
- **Cumulative ACKs**: an ACK with number N acknowledges all bytes up to N-1.
- **Sliding Window**: sender can send multiple segments before waiting for ACKs, up to the window size.
- **Retransmission timer (RTO)**: if no ACK is received before the timeout, the segment is retransmitted.
    - RTO is based on measured RTT (Round-Trip Time).
    - **Karn's algorithm**: do not use RTT samples from retransmitted segments.
- **Fast Retransmit**: if the sender receives **three duplicate ACKs**, it immediately retransmits the missing segment without waiting for the timeout.
- **Selective Acknowledgment (SACK)**: allows receiver to acknowledge non-contiguous blocks, so sender retransmits only missing bytes.
#### Flow Control
Flow control prevents a fast sender from overwhelming a slow receiver.
- The receiver advertises a **Window Size** (`rwnd`) in each ACK.
- The sender can have at most `rwnd` bytes in flight (sent but not ACKed).
- If `rwnd = 0`, the sender stops and periodically sends **zero-window probes**.
- **Window Scaling** option extends the 16-bit window field up to 1 GB.
#### Congestion Control
Congestion control prevents the network from being overloaded. It is sender-side and uses a **congestion window (cwnd)**.
Effective sending window = `min(cwnd, rwnd)`.
Classic algorithms (TCP Reno):
1. **Slow Start**
    - Starts with `cwnd = 1 MSS`.
    - Increases `cwnd` by 1 MSS for each ACK received → exponential growth (doubles every RTT).
    - Continues until `cwnd >= ssthresh` (slow start threshold).
2. **Congestion Avoidance**
    - Increases `cwnd` by approximately 1 MSS per RTT (linear growth).
    - e.g. `cwnd += MSS * MSS / cwnd` per ACK.
3. **Timeout (packet loss detected by RTO)**
    - Set `ssthresh = cwnd / 2`.
    - Set `cwnd = 1 MSS`.
    - Enter slow start.
4. **Fast Retransmit / Fast Recovery**
    - On 3 duplicate ACKs:
        - Set `ssthresh = cwnd / 2`.
        - Set `cwnd = ssthresh + 3 MSS`.
        - Retransmit missing segment.
        - On each further duplicate ACK, increase `cwnd` by 1 MSS.
        - On new ACK, set `cwnd = ssthresh` and enter congestion avoidance.
Modern congestion control algorithms include **CUBIC** (default in Linux) and **BBR** (Google).
#### TCP Options
Important options negotiated during the handshake:
- **MSS (Maximum Segment Size)**: largest payload TCP can send. Typically `MTU - 40` = 1460 on Ethernet.
- **Window Scale**: shifts window size left up to 14 bits.
- **SACK**: selective acknowledgments.
- **Timestamps**: RTT measurement and protection against wrapped sequence numbers (PAWS).
#### TCP vs UDP Summary
|Feature|TCP|UDP|
|---|---|---|
|Connection|Connection-oriented|Connectionless|
|Reliability|Reliable, retransmits|Unreliable|
|Ordering|In-order delivery|No ordering|
|Flow control|Yes|No|
|Congestion control|Yes|No|
|Header size|20–60 bytes|8 bytes|
|Overhead|Higher|Lower|
|Use cases|Web, email, file transfer|DNS, VoIP, streaming, gaming|
## 4. Application Layer
- The application layer contains protocols that provide services directly to users.
### DNS: Domain Name System
DNS translates domain names to IP addresses.
- Hierarchical namespace: root → TLD (`.com`, `.org`) → domain (`example.com`) → subdomain (`www.example.com`).
- Resolution is usually recursive: client asks a recursive resolver, which queries root, TLD, and authoritative servers.
- Uses UDP port 53 (TCP for large responses/zone transfers).
- Record types:
    - `A` – IPv4 address
    - `AAAA` – IPv6 address
    - `CNAME` – canonical name alias
    - `MX` – mail exchanger
    - `NS` – name server
    - `PTR` – reverse lookup
    - `TXT` – text data (e.g. SPF, DKIM)
- Caching with TTL improves performance.
### DHCP: Dynamic Host Configuration Protocol
DHCP assigns IP addresses and network configuration to hosts.
DORA process:
1. **Discover** – client broadcasts a request.
2. **Offer** – server offers an IP address.
3. **Request** – client requests the offered IP.
4. **Acknowledge** – server confirms the lease.
Uses UDP ports 67 (server) and 68 (client).
### [[HTTP_HTTPS|HTTP/HTTPS]]
- **HTTP** (port 80) and **HTTPS** (port 443) are request/response protocols for the web.
- Methods: GET, POST, PUT, DELETE, HEAD, etc.
- Status codes: 200 OK, 301 Moved Permanently, 404 Not Found, 500 Internal Server Error.
- HTTP/1.1 added persistent connections and pipelining.
- HTTP/2 uses binary framing and multiplexing over one TCP connection.
- HTTP/3 uses QUIC over UDP, reducing head-of-line blocking.
HTTPS adds **TLS** (Transport Layer Security):
- TLS handshake negotiates cipher suites, exchanges certificates, and derives session keys.
- Provides confidentiality, integrity, and authentication.
### Other Common Protocols
| Protocol   | Port    | Purpose                                |
| ---------- | ------- | -------------------------------------- |
| [[FTP]]    | 21/20   | File transfer                          |
| [[SSH]]    | 22      | Secure remote shell                    |
| [[Telnet]] | 23      | Insecure remote shell                  |
| [[SMTP]]   | 25      | Sending email                          |
| [[POP3]]   | 110     | Retrieving email                       |
| [[IMAP]]   | 143     | Retrieving email (server-side folders) |
| [[NTP]]    | 123     | Time synchronization                   |
| [[SNMP]]   | 161/162 | Network management                     |
| [[RDP]]    | 3389    | Remote Desktop                         |
## End-to-End Example: Loading a Web Page
Trace what happens when you type `https://www.example.com` into a browser:
1. **DNS Resolution**
    - Browser checks cache; if not found, sends DNS query to resolver.
    - Resolver walks the hierarchy and returns the IP address of `www.example.com`.
2. **TCP Connection**
    - Browser creates a socket to `IP:443`.
    - TCP performs the three-way handshake.
3. **TLS Handshake** (for HTTPS)
    - Client and server negotiate TLS parameters, authenticate the server certificate, and establish encryption keys.
4. **HTTP Request**
    - Browser sends an HTTP GET request for `/` over the encrypted TLS connection.
5. **Data Transfer**
    - Server sends the HTML page in TCP segments.
    - TCP handles segmentation, ordering, retransmissions, flow control, and congestion control.
    - IP routes packets through the internet using routing tables.
    - At each hop, routers decrement TTL and forward based on destination IP.
    - On the local network, ARP resolves next-hop MAC addresses.
6. **Rendering**
    - Browser parses HTML, requests additional resources (CSS, JS, images), and renders the page.
7. **Connection Close or Keep-Alive**
    - HTTP/1.1 may keep the TCP connection open for additional requests.
    - Eventually, TCP closes with a four-way handshake.
## Security Considerations
TCP/IP was designed for openness, not security. Common vulnerabilities:
- **IP Spoofing**: forging source IP addresses.
- **TCP SYN Flood**: attacker sends many SYN packets without completing handshakes, exhausting server resources. Mitigation: SYN cookies, rate limiting.
- **UDP Amplification DDoS**: small query triggers large response (DNS, NTP, SSDP).
- **ARP Spoofing**: attacker sends fake ARP replies to intercept traffic.
- **DNS Poisoning**: corrupting DNS cache to redirect users.
- **Man-in-the-Middle**: intercepting and modifying traffic.
Security protocols add protection:
- **TLS/SSL**: encrypts application data.
- **IPsec**: secures IP layer (AH/ESP, tunnel/transport modes).
- **DNSSEC**: authenticates DNS responses.
- **HTTPS**: HTTP over TLS.
## Diagnostic Tools
- ping – sends ICMP echo requests, tests reachability.
- [[Traceroute]] / tracert – maps the path to a destination using TTL and ICMP.
- ipconfig / ifconfig / ip – view and configure IP settings.
- [[Cybersecurity/Tools/arp|arp]] -a – display ARP cache.
- [[netstat]] / ss – show connections, routing tables, listening ports.
- [[NSLookup]] / dig – query DNS records.
- [[tcpdump]] / Wireshark – capture and analyze packets.
---