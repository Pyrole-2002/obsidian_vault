- `netdiscover` is a network recon tool designed primarily for IPv4 address discovery on LANs.
- It works on Layer 2, unlike tools like [[Nmap]] which often use ICMP (ping) or [[TCP]]/[[UDP]] packets (Layer 3/4).
- It operates primarily using [[Cybersecurity/Networking/Protocols/ARP]].
- **Broadcasting:** When run in active mode, it sends out ARP request packets to the local network segment.
- **Responses:** Any active device on that local network that owns or listens to that IP will reply with an ARP reply containing its MAC address.
- It can also run passively with -p flag, simply listening to the network passing by without sending any packets itself. This is stealthier but relies on other devices talking.
```bash
netdiscover
# -i <interface>: Specifies which network interface to use (eth0, wlan0)
# -r <range>: Scans a specific custom IP range or subnet
# -p: Passive mode
# -s <time>: Sleep time between each ARP request
```

```
 Currently scanning: 172.16.0.0/16   |   Screen View: Unique Hosts
 22 Captured ARP Req/Rep packets, from 4 hosts.   Total size: 1320
 _____________________________________________________________________________
   IP            At MAC Address     Count     Len  MAC Vendor / Hostname      
 -----------------------------------------------------------------------------
 192.168.199.1   00:50:56:c0:00:08     17    1020  VMware, Inc.
 192.168.199.2   00:50:56:f2:82:a8      3     180  VMware, Inc.
 192.168.199.130 00:0c:29:c6:3a:46      1      60  VMware, Inc.
 192.168.199.254 00:50:56:e6:af:3c      1      60  VMware, Inc.
```
- For the above output:
	- `192.168.199.1` is your host machine. This is the vnet adapter sitting on your physical computer.
	- `192.168.199.2` is the NAT gateway. This acts as the router for your vnet, allowing your VMs to reach the internet.
	- `192.168.199.254` is the [[DHCP]] server. This is the VMware service that automatically assigns IP addresses to new VMs.
	- `192.168.199.130` is the target (Kioptrix). VMware typically starts handing out dynamic IPs to guest VMs starting at `.128` or `.130`.
