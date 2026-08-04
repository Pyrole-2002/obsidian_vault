- `arp-scan` is a cli tool that discovers devices on a local network segment by sending [[ARP]] (Address Resolution Protocol) requests and listening for replies. It is fast, accurate, and virtually impossible for hosts to hide from, because every device on a LAN must answer ARP to communicate.
- **Operates at Layer 2 (Data Link)**: Unlike tools that rely on [[ICMP]] ping or [[TCP]]/[[UDP]] probes (Layer 3/4), `arp-scan` uses raw ARP frames.
- **Firewall‑proof**: Local host firewalls that drop ICMP or TCP packets **cannot** block ARP replies; the ARP protocol is handled by the kernel’s network stack below the firewall layer.
- **Extremely fast**: A `/24` network can be scanned in a few seconds.
- **Hardware fingerprinting**: MAC addresses are compared against an OUI database to identify the vendor of each network card.
>If a device has an IP address on your subnet and is powered on, `arp-scan` will find it.
- Three-step process:
	1. **Broadcast:** Sends ARP "who-has" requests to each IP in the target range.
	2. **Listen:** Captures "is-at" replies from active hosts.
	3. **Fingerprint:** Looks up the returned MAC addresses in the built in Organizationally Unique Identifier (OUI) db to show the hardware vendor.
- Because ARP is not routable, this only works on your **local subnet**; you cannot scan across a router or the internet.
```bash
sudo arp-scan [options] [targets]
sudo arp-scan --localnet
```

| Option                               | Description                                                                                                                       |
| ------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------- |
| `-l` / `--localnet`                  | Automatically scan the subnet configured on your active interface (uses IP + netmask).                                            |
| `IP/mask`                            | Scan a CIDR range.                                                                                                                |
| `IP-start-IP-end`                    | Scan a continuous range.                                                                                                          |
| `-f <file>` / `--file=<file>`        | Read targets from a file (one IP, hostname, or CIDR per line).                                                                    |
| `-I <iface>` / `--interface=<iface>` | Choose the network interface (`eth0`, `wlan0`, `tap0`).                                                                           |
| `-V <vlan_id>` / `--vlan=<vlan_id>`  | Add an 802.1Q VLAN tag to the outgoing ARP packets. Essential for scanning inside a specific VLAN when connected to a trunk port. |
| `-b <bps>` / `--bandwidth=<bps>`     | Default is 1048576 (1Mbps). Throttle the sending rate (bits per sec), lower values are stealthier.                                |
| `-i <ms>` / `--interval=<ms>`        | Default 2 ms. Interval between two packets in ms.                                                                                 |
| `-r <n>` / `--retry=<n>`             | Default 1. Number of retries per host (total attempts = retries + 1). Increase on lossy WiFi links.                               |
| `-t <ms>` / `--timeout=<ms>`         | Default 500 ms. Time to wait for a reply after the last attempt.                                                                  |
| `-q` / `--quiet`                     | Suppress all output except the final summary.                                                                                     |
| `-x` / `--plain`                     | No header/footer, just one reply per line. Ideal for piping to `awk` or `cut`.                                                    |
| `--ignoredups`                       | Show only the first reply from each host; suppress duplicate replies.                                                             |
| `-g` / `--generate`                  | Generate a list of IP addresses from the target spec without sending packets (useful for scripting).                              |
| `-P` / `--pcapsavefile=<file>`       | Save a pcap file of all sent/received ARP traffic for later analysis.                                                             |
| `-S <ip>` / `--srcaddr=<ip>`         | Spoof the sender IP in the ARP request. The target will record this IP as the requester.                                          |
| `-u <mac>` / `--hwsrc=<mac>`         | Spoof the source MAC address. Your real MAC is hidden from both the target and the switch.                                        |
| `-m <mac>` / `--destaddr=<mac>`      | Set the destination MAC.                                                                                                          |
| `-R <n>` / `--random`                | Sends packets in a random order (defeats simple IDS that look for sequential ARP sweeps).                                         |
```bash
# Stealthy Scan, very slow, only one attempt per host
sudo arp-scan -b 10000 -r 0 192.168.1.0/24

# Reliable Scan over Noisy WiFi
sudo arp-scan -r 2 -t 1000 192.168.1.0/24

# Ghost Scan
sudo arp-scan -S 10.0.0.99 -u 00:11:22:33:44:55 10.0.0.0/24
```
### MAC Address Fingerprinting (OUI DB)
`arp-scan` ships with a file `ieee-oui.txt` (or uses `mac-vendor.txt`) that maps the first three bytes of a MAC address to a manufacturer.
```bash
# Update the db
sudo get-oui -v
# - or download the latest IEEE OUI list manually and place it in /usr/share/arp-scan/ieee-oui.txt.

# Display vendor names enabled by default
Disable lookup (slightly faster scan)
sudo arp-scan --localnet --ouifile=/dev/null
```
Knowing that a host is from “Raspberry Pi Foundation” or “Cisco” instantly tells you a lot about it.
### Use Case Examples
```bash
# Quick inventory of your home network
sudo arp-scan -l

# Find a hidden / unauthorised device
sudo arp-scan -l | grep -v "Intel"   # Exclude known workstations

# Penetration test - map a target subnet
sudo arp-scan 10.10.10.0/24 | tee internal-hosts.txt

# Scan inside a specific VLAN (from a trunk)
sudo arp-scan -I eth0 -V 50 10.50.0.0/24

# Generate a target list for further scanning
sudo arp-scan -x 192.168.1.0/24 | awk '{print $1}' > live_ips.txt
nmap -iL live_ips.txt -sV -O

# Save a pcap for forensic analysis
sudo arp-scan -l -P arp_scan_dump.pcap
```
### Comparison with [[Nmap]]
|Feature|arp-scan|Nmap|
|---|---|---|
|Layer|2 (ARP)|Primarily 3/4, but can do ARP (`-PR`)|
|Speed|Extremely fast (few seconds for /24)|Fast, but ARP mode comparable|
|Firewall bypass|Yes – ARP is mandatory|Firewalls may block probes|
|Host fingerprinting|Only MAC vendor|OS and service detection|
|Routing|Local subnet only|Can scan any routable network|
|Stealth options|Source IP/MAC spoofing, random order|Decoy, idle scan, timing templates|

> Often the best approach is to run **arp-scan** first to enumerate live hosts, then feed the result to **Nmap** for deeper inspection.
### Limitations & Caveats
- **Local subnet only:** ARP is a link‑layer protocol; it cannot traverse routers.
- **Requires root privileges:** Raw sockets are needed.
- **ARP cache poisoning risk:** Rapid ARP sweeps may upset some switches or IDS/IPS. Use bandwidth limits to blend in.
- **Not completely invisible:** ARP requests are broadcast; any sniffer on the network can see the sweep, especially if you use spoofed MACs that don’t match the sender IP.
- **Duplicate replies:** Some load‑balancer or cluster setups may reply multiple times. Use `--ignoredups`.
---