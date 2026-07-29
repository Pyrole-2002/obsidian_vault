- This is a highly efficient cli tool used for network discovery.
- Unlike tools like [[Nmap]] that rely on Layer 3 protocols like [[ICMP]] ping or [[TCP]]/[[UDP]] packets, arp-scan operates exclusively at Layer 2 using the Address Resolution Protocol ([[ARP]]).
- Because ARP is fundamental to how local networks function, devices cannot hide from arp-scan by configuring local firewalls to drop pings. If a device is on your local subnet and is communicating, it must answer an ARP request.
- When you run arp-scan, it performs 3 actions:
	1. Broadcasts: It sends out raw ARP packets on the local network.
	2. Listens: It waits for devices to send ARP reply packets.
	3. Fingerprints: It takes the MAC addresses from the replies and looks them up in an internal OUI (Organizational Unique Identifier) db to identify the hardware vendor.
- Because ARP is not a routable protocol, arp-scan only works on your local subnet. You cannot scan a target across the internet or through a router.
```bash
sudo arp-scan [Options] [Target]
```
- Target Specification
	- `-l` / `--localnet` : Local Network: Automatically scans the subnet attached to your active network interface by calculating IP range using your IP and subnet mask.
	- `[IP/CIDR]` : Custom Range: Scans a specific network block.
	- `-f` / `--file` : File Input: Reads a list of target IPs or hostnames from a text file.
- Network Configuration
	- `-I` / `--interface` : Specify Interface: Specifies which network interface to send ARP requests out of (eth0, wlan0, tap0).
	- `-V` / `--vlan` : VLAN Tagging: Adds an 802.1Q VLAN tag to the ARP packets. Useful on a trunk port when you want to sweep a specific VLAN.
- Evasion and Spoofing
	- `-S` / `--srcaddr` : Spoof Source IP: Spoofs the source IP address in the ARP request, making the target think the request came from this IP instead of yours.
	- `-u` / `--hwsrc` : Spoof Source MAC: Spoofs the source MAC address, hiding your physical hardware address from the target and the network switch.
- Output
	- `-q` / `--quiet` : Quiet Mode: Suppresses the normal output and only displays the final summary statistics.
	- `-x` / `--plain` : Plain Format: Removes header and footer, recommended if you are piping into a grep or awk script.