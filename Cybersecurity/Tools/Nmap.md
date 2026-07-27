- Network Mapper (Nmap) is an open-source tool used to discover hosts and services on a computer network.
- It works by sending specially crafted packets to the target and analyzing the responses.
```bash
nmap [scan type] [options] [target]
```
- Scan Types
	- By default, if you run nmap with sudo, it uses Stealth SYN scan (-sS). If you run it without sudo, it uses a Connect scan (-sT).
	- `-sS` : Stealth SYN Scan: Sends a SYN packet, gets a SYN/ACK, but tears down the connection before completing the [[TCP]] handshake. It is fast and slightly stealthier.
	- `-sT` : TCP Connect Scan: Completes the full TCP handshake. Slower and more likely to be logged by the target's firewall/IDS (Intrusion Detection System).
	- `-sU` : [[UDP]] Scan: Scans for UDP ports like (DNS or [[SNMP]]).
- Port Specifications
	- By default, nmap scans the top 1000 most common ports.
	- `-p` : Scan specific ports: `-p 80,443,445`.
	- `-p-` : Scan all ports: `nmap -p- 192.168.199.130`.
	- `-F` : Fast scan: Scans only the top 100 most common ports.
- Enumeration
	- `-sV` : Version detection: Probes open ports to determine service and version info.
	- `-O` : OS detection: Guesses the target's OS by analyzing how its network stack responds to odd packets.
	- `-sC` : Default scripts: Runs nmap's default set of vulnerability and discovery scripts, Nmap's Scripting Engine (NSE) against the target.
- Output
	- `-oN` : Normal output: Saves the scan exactly as it looks on your screen to a text file.
	- `-oX` : XML output: Saves as XML which can be imported into tools like [[Metasploit]].
	- `-oA` : All formats: Saves the scan in Normal, XML and Greppable formats.