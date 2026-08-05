- The `host` command is a simple yet powerful [[DNS]] lookup utility, commonly found on Linux, macOS, and other Unix-like systems.
- It’s part of the BIND (Berkeley Internet Name Domain) suite, created by the Internet Systems Consortium (ISC), and is designed for querying DNS servers directly from the terminal.
- **Primary function**: Resolve domain names to IP addresses and vice versa.
- **Can also look up**: Any DNS record type (MX, NS, TXT, CNAME, AAAA, SOA, etc.).
- **Query specific servers**: Point queries to a custom DNS resolver instead of the system default.
- **Perform reverse lookups**: Convert an IP address back into a hostname.
- **Check zone data** (with caution): List all records in a DNS zone if the server allows a zone transfer.
- Its simpler than [[DIG]] but more versatile than [[NSLookup]].
### Syntax
```bash
host [options] <name | IP address> [DNS server]
```
- `<name>` : A domain name (e.g., `example.com`) or IP address (for reverse lookup).
- `[DNS server]` : Optional; an IP address or hostname of a specific DNS server to query. If omitted, the system’s configured resolver (from `/etc/resolv.conf`) is used.
### Use Cases
- Simple Forward Lookup (A/AAAA records)
```bash
host example.com

# Output
example.com has address 93.184.216.34
example.com has IPv6 address 2606:2800:220:1:248:1893:25c8:1946
```
- Reverse DNS Lookup (PTR Record)
```bash
host 8.8.8.8

# Output
8.8.8.8.in-addr.arpa domain name pointer dns.google.
```
- Lookup a specific record type.
```bash
host -t MX gmail.com
host -t NS example.com
host -t TXT _dmarc.google.com
host -t SOA example.com
host -t CNAME www.github.com

# Output for MX
gmail.com mail is handled by 5 gmail-smtp-in.l.google.com.
gmail.com mail is handled by 10 alt1.gmail-smtp-in.l.google.com.
...
```
- Query a specific DNS server.
```bash
host example.com 8.8.8.8
host -t AAAA example.com 1.1.1.1
```
- List all records in a zone (Zone Transfer).
```bash
host -l example.com ns1.example.com
```
- `-l` requests a zone transfer (AXFR) from the specified authoritative name server.
- **Most public servers reject zone transfers** unless you are a trusted secondary. If the server doesn’t allow it, you’ll see `Transfer failed.`
- **Important**: Only use this on domains you own or have explicit permission to test. Repeated attempts against third‑party servers may be considered abusive.
### Options

| Option         | Meaning                                                                                                             |
| -------------- | ------------------------------------------------------------------------------------------------------------------- |
| `-a`           | Equivalent to `-v -t ANY`; show all available info.                                                                 |
| `-c <class>`   | Set query class (IN for Internet, CH for Chaos, etc.). Default `IN`.                                                |
| `-d` / `-v`    | Enable verbose/debug output (`-v` is the verbose equivalent).                                                       |
| `-l`           | List zone (AXFR). Requires a name server argument.                                                                  |
| `-N <ndots>`   | Number of dots needed before a name is considered absolute (rarely used).                                           |
| `-r`           | Disable recursive processing (set RD bit to 0). Useful to see what an authoritative server knows without recursion. |
| `-R <number>`  | Number of retries for UDP queries (default 1).                                                                      |
| `-t <type>`    | Query a specific resource record type (A, AAAA, MX, NS, CNAME, PTR, SOA, TXT, SRV, etc.).                           |
| `-T`           | Use [[TCP]] instead of [[UDP]] for queries. Helpful for large responses or troubleshooting.                         |
| `-W <seconds>` | Timeout in seconds to wait for a reply (default is usually 5).                                                      |
| `-4`           | Force use of IPv4 transport only.                                                                                   |
| `-6`           | Force use of IPv6 transport only.                                                                                   |
### Reverse Lookup
Reverse lookups are done automatically for anything that looks like an IP address. `host` converts IPv4 addresses to the format `d.c.b.a.in-addr.arpa` and IPv6 to `.....ip6.arpa`. You can also force a reverse lookup of a name by using `host` on a PTR record explicitly:
```bash
host -t PTR 4.4.8.8.in-addr.arpa
```
But normally just `host 8.8.4.4` is enough.
### Limitations
- **No built‑in DNSSEC validation**: `host` doesn’t show DNSSEC status or validate signatures. Use `dig +dnssec` or `delv` for that.
- **ANY queries are filtered**: Many public resolvers return only a subset of records for `-a` or `-t ANY`. Better to query the specific record type.
- **Zone transfers**: Almost always blocked; don’t rely on `-l` unless you are the domain admin.
- **Output parsing**: `host` is designed for humans. If you need machine‑parsable output (e.g., in scripts), `dig` with `+short` is a safer choice.
- **Caching**: Queries to a recursive resolver will return cached results. To see fresh authoritative data, query one of the domain’s name servers directly.