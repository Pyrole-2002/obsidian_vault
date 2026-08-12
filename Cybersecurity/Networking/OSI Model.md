![[Pasted image 20260813005255.png]]

- The Open Systems Interconnection (OSI) Model is a conceptual framework used to understand and standardize how different networking protocols and devices communicate. It was created by the International Organization for Standardization (ISO).
- A Protocol Data Unit (PDU) is the unit of data that is specified by a protocol at a particular layer of a network model.
	- It represents the data plus the protocol control info (headers or trailers) added to that layer. A PDU is a formatted block of data that a layer sends or receives. A PDU contains:
		- Service Data Unit (SDU is the payload/data from the upper layer.
		- Protocol Control Information (PCI) are the headers and trailers added by the current layer.
	- During encapsulation, each layer adds its own header/trailer to the PDU, creating a new PDU.
# Layer 1: Physical
- PDU: Bits / Symbols
- Function: Transmits raw bits over a physical medium.
- It defines:
	- Cables, connectors, pins
	- Electrical, optical, radio signals
	- Data rates, voltage levels, timing
	- Physical topology
- Devices:
	- Ethernet cables, fiber optics, coaxial cable
	- Hubs, repeaters, transceivers, antennas
	- NIC physical components
- The physical layer doesn't understand frames, packets or addresses. It only deals with 1s and 0s.
# Layer 2: Data Link
- PDU: Frame
- Function: Provides node-to-node delivery between devices on the same local network.
- It handles:
	- Physical addressing using MAC addresses
	- Framing: Packaging bits into frames
	- Error Detection: Usually via FCS/CRC
	- Media Access Control: Who can transmit when
- Sublayers:
	- Logical Link Control (LLC) interfaces with Network Layer.
	- Media Access Control (MAC), physical addressing and access.
- Examples:
	- Ethernet, Wi-Fi, PPP, Frame Relay
	- Switches, bridges, access points
- Layer 2 is about communication between directly connected devices on the same LAN.
# Layer 3: Network
- PDU: Packet
- Function: Provides end-to-end logical addressing and routing across multiple networks.
- It handles:
	- Logical addressing using IP addresses
	- Routing: Finding the best path
	- Packet forwarding
	- Fragmentation when needed
	- TTL/Hop Count
- Examples:
	- IP, [[ICMP]], IGMP
	- Routing Protocols: OSPF, EIGRP, BGP, RIP
	- Devices: Routers, Layer 3 Switches
- Layer 3 moves data from one network to another using logical addresses.
# Layer 4: Transport
- PDU: Segment ([[TCP]]) or Datagram ([[UDP]])
- Function: Provides end-to-end coms between apps.
- It handles:
	- Segmentation and reassembly
	- Port nums to identify apps
	- Flow control
	- Error recovery
	- Connection-oriented vs connectionless coms
- TCP: Connection-oriented; Reliable: Acknowledgements, retransmissions
- UDP: Connectionless; Not reliable: Best effort
- Layer 4 ensures data arrives completely and in order if using TCP, or quickly with low overhead if using UDP.
# Layer 5: Session
- PDU: Data
- Function: Establishes, manages, and terminates sessions between apps.
- It handles:
	- Dialog control: Full-duplex, half-duplex
	- Session establishment and teardown
	- Checkpointing/recovery
	- Synchronization
- Examples:
	- NetBIOS, [[RPC]], [[SMB]], SOCKS
- Layer 5 manages conversations between apps. In many real-world TCP/IP implementations, this layer's duties are handled inside the app or transport layer.
# Layer 6: Presentation
- PDU: Data
- Function: Formats, encrypts, and compresses data so apps can understand it.
- It handles:
	- Data translation: ASCII to EBCDIC
	- Encryption/decryption
	- Compression
	- Character encoding
	- Data syntax
- Examples:
	- TLS/[[SSL]] encryption, JPEG, GIF, MPEG, MIME
- Layer 6 ensures data is in a usable format, regardless of the underlying system or network.
# Layer 7: Application
- PDU: Data
- Function: Provides network services directly to end users and apps.
- It is the closest layer to the user. It does not include the actual app itself, but the protocols apps use to communicate.
- Examples:
	- [[HTTP and HTTPS]], FTP, [[SMTP]], [[POP3]], [[IMAP]]
	- [[DNS]], [[DHCP]], [[SSH]], Telnet, [[SNMP]]
- When you open a browser and visit a website, the browser uses Layer 7 protocols.
---
## Data Encapsulation
- When data is sent, it moves down the layers. Each layer adds its own header, and sometimes a trailer.
- Example sending an email:
	1. **Application** — Creates the email data
	2. **Transport** — Adds TCP header → **Segment**
	3. **Network** — Adds IP header → **Packet**
	4. **Data Link** — Adds Ethernet header and trailer → **Frame**
	5. **Physical** — Converts frame to **bits** and transmits
- At the receiving end, the process reverses:
	- Bits → Frame → Packet → Segment → Data
	- Each layer removes its own header/trailer
	- The Application layer delivers the original data
- This is called **decapsulation**.
## OSI Model vs [[TCP_IP|TCP/IP]] Model
The OSI model is conceptual. The TCP/IP model is the actual protocol suite used on the Internet.

|OSI Layer|TCP/IP Layer|
|---|---|
|Application, Presentation, Session|Application|
|Transport|Transport|
|Network|Internet|
|Data Link, Physical|Network Access / Link|

- OSI has **7 layers**; TCP/IP originally has **4** — often shown as **5**    
- OSI is **theoretical**; TCP/IP is **practical**
- OSI protocols were mostly replaced by TCP/IP
- TCP/IP combines Session and Presentation into Application
## Miscellaneous
- **PDU names by layer:** Bits → Frames → Packets → Segments → Data
- **Layer 2 vs Layer 3 addressing:**  
	- MAC addresses work at Layer 2, IP addresses at Layer 3.  
	- **[[ARP]]** maps IP addresses to MAC addresses and operates between Layers 2 and 3.
- **Switches vs Routers:**  
    Switches forward frames using MAC addresses (Layer 2).  
    Routers forward packets using IP addresses (Layer 3).
- **TCP vs UDP:**  
    TCP is reliable, connection-oriented, and slower.  
    UDP is fast, connectionless, and best-effort.
- **Port numbers:**  
    Ports are a Layer 4 concept that identify which application should receive the data.
- **Encapsulation:**  
    Each layer adds its own header/trailer. Receivers remove them in reverse order.
- **Not all protocols fit perfectly:**  
    Some protocols cross layers. For example, TLS/SSL is often placed at Presentation/Session, but in TCP/IP it is part of Application. MPLS is sometimes called “Layer 2.5.”