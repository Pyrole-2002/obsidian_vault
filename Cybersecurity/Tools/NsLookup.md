- Name Server Lookup (`nslookup`) is a cli network administration tool. Its primary job is to query [[DNS]] servers to find out how domain names map to IP addresses and vice versa.
- The DNS is a hierarchical, distributed database. At the top is the **root** (`.`), beneath which are **Top-Level Domains** (`.com`, `.org`, `.net`, country codes like `.uk`), then **second-level domains** (`example.com`), and possibly **subdomains** (`www.example.com`).
- **Recursive Resolver (Caching Resolver):** The server your machine normally sends queries to. It traverses the DNS tree on your behalf, caches answers, and returns the result to you. Examples: your ISP’s DNS, 8.8.8.8 (Google), 1.1.1.1 (Cloudflare).
- **Authoritative Name Server:** A server that holds the definitive records for a zone (e.g., `ns1.example.com` for `example.com`). It answers only with data it knows from its local zone files, and does not perform recursion for queries outside its zones.
- When you run `nslookup`, you are talking to a Resolver (usually provided by ISP or public like Google's `8.8.8.8`). If the resolver doesn't know the answer, it asks a chain of servers:
	- Root Servers (`.`): Points to the TLD servers.
	- Top Level Domain (TLD) Servers (`.com`, `.org`): Points to the Authoritative servers.
	- Authoritative Name Servers: The absolute source of truth for a specific domain (such as Amazon's Route53 servers hosting your company's domain).
- When you run a basic `nslookup`, the first line of the result almost always says `Non-authoritative anser`.
	- Authoritative: The answer came directly from the server that hosts the domain's DNS zone file.
	- Non-authoritative: The answer came from a cache (ISP's DNS server or Google's `8.8.8.8`). The are repeating an answer they fetched earlier to save time.
- **Recursive query:** The resolver must return the final answer or an error. The client expects the resolver to do all the work.
- **Iterative query:** The server returns the best answer it has, often a referral to another server. `nslookup` can mimic an iterative resolver by disabling recursion (`set norecurse`).
- An Fully Qualified Domain Name (FQDN) ends with a trailing dot (`.`), representing the root: `www.example.com`. The dot tells DNS “this name is absolute.” Without it, your system’s DNS search list might append local domains, altering the query. `nslookup` respects this.
- `nslookup` can pull various types of data. You need to specify these types when troubleshooting:

| **Record Type** | **Name**           | **What it does**                                                    | **Real-World Use**                                                                                                                                                    |
| --------------- | ------------------ | ------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **A**           | Address            | Maps a hostname to an **IPv4** address.                             | Finding a web server's IP.                                                                                                                                            |
| **AAAA**        | Quad-A             | Maps a hostname to an **IPv6** address.                             | Finding modern web server IPs.                                                                                                                                        |
| **CNAME**       | Canonical Name     | Maps an alias to a true domain name.                                | Pointing `[www.site.com](https://www.site.com)` to `site.com`.                                                                                                        |
| **MX**          | Mail Exchange      | Directs email to a mail server.                                     | Troubleshooting missing emails.                                                                                                                                       |
| **TXT**         | Text               | Holds text information.                                             | Verifying domain ownership, SPF/DMARC (anti-spam).                                                                                                                    |
| **NS**          | Name Server        | Lists the authoritative servers for a domain.                       | Checking who manages a domain's DNS.                                                                                                                                  |
| **SOA**         | Start of Authority | Core info (admin email, refresh rates).                             | Checking DNS propagation delays.                                                                                                                                      |
| **PTR**         | Pointer            | Maps an IP back to a hostname (Reverse DNS).                        | Verifying email server legitimacy.                                                                                                                                    |
| SRV             | Service Locator    | Defines host and port for specific services like [[SIP]], [[LDAP]]. | Discover both the specific server hostname and exact port number required to connect to specialized network services like Microsoft Active Directory or VoIP systems. |
#### Operating Modes
- Non-Interactive Mode: Best for one-off queries or writing scripts. You issue the entire command on one line.
```bash
nslookup [option] [host] [server]
nslookup -type=MX example.com 8.8.8.8
```
- Interactive Mode: Best for deep troubleshooting sessions where you need to run multiple queries against different servers. You type `nslookup` and hit Enter. Your prompt changes to a `>`.
- Important options (most can also be used as `set` commands in interactive mode):
```bash
$ nslookup
> set type=mx
> google.com
> server 8.8.8.8
> yahoo.com
> exit
```
#### Debug Mode
```bash
nslookup -debug example.com
```
- Debug mode exposes the raw anatomy of a DNS packet.
- You will see sections mapping exactly to the DNS protocol standard (RFC 1035).
- HEADER: Shows flags.
	- `opcode = QUERY`: You are asking a question.
	- `rd` (Recursion Desired): You asked your server to go fetch the answer for you.
	- `ra` (Recursion Available): The server confirms it can do that.
- QUESTIONS: The raw query. `IN` stands for Internet. It is a legacy class field from the 1980s.
- ANSWERS: The actual result (IP address).
- AUTHORITY RECORDS: The name servers that hold the ultimate truth for this domain.
- TTL (Time to Live): Crucial metric. You will see a num in seconds (`ttl = 300`). This tells the caching servers "You can remember this IP for 5 minutes (300 seconds), then you must ask me again."
#### Interactive Mode Commands
- `server <IP/host>`: Changes the default DNS server to the specified one for future queries. Uses the current default server to resolve the new server's name (if a name is given).
- `lserver <IP/host>`: Changes the default server but uses the initial server (your original system resolver) to resolve the new server's name. This is vital when DNS is broken and you can't resolve the name of the new server via the broken one.
#### More Options
Inside interactive mode (or passed as flags), you can manipulate how `nslookup` behaves under poor network conditions:
- `host [server]`: Domain name or IP address to look up. Optional `server` specifies which DNS server to query.
- `-type=TYPE` or `-query=TYPE`: Record type (A, AAAA, MX, NS, SOA, CNAME, PTR, TXT, SRV, etc.). If omitted, defaults to A/AAAA depending on system.
- `-timeout=SECONDS`: Changes how long it waits for a reply before giving up. Default is usually 5 sec.
- `-domain=DOMAIN`: Append this domain to unqualified names.
- `-retry=NUMBER`: Number of retries if timeout occurs. Default 3.
- `-port=PORT`: DNS runs on port 53 by default. If you are testing a custom DNS server running on a non-standard port, use this.
- `-d2`: Enables exhaustive debug mode; shows even more packet detail than standard `-debug`.
- `-class=CLASS`: DNS class. Almost always IN (Internet). Rarely changed.
- `-recurse` / `-norecurse`: Set the RD flag on/off. `-norecurse` makes it iterative.
- `-vc`: Use [[TCP]] virtual circuit instead of [[UDP]] (forces TCP). Useful for testing large responses or zone transfers.
- `-nosearch`: Disable appending domains from the search list.
#### Zone Transfer (`ls`)
- **`ls [option] domain`**: Lists the contents of a DNS zone, essentially performing a zone transfer (AXFR). This command is often blocked for security.
    - `ls example.com`: List all records in the zone.
    - `ls -a example.com`: Show CNAME and aliases.
    - `ls -d example.com`: List all available record types.
    - `ls -t TYPE example.com`: List records of a specific type.
- **Important:** Use `set vc` first if the zone is large; zone transfers commonly use TCP. If the server refuses the AXFR, you’ll see an error like `Transfer failed.` or `Query refused`. This is normal.
#### Modern Context
In the early 2000s, Unix engineers declared `nslookup` deprecated in favor of [[DIG]] and [[host]]. `dig` outputs raw, standard DNS responses natively, making it much easier to parse in scripts.