- Name Server Lookup (`nslookup`) is a cli network administration tool. Its primary job is to query [[DNS]] servers to find out how domain names map to IP addresses and vice versa.
- The DNS is a hierarchical, distributed database. At the top is the **root** (`.`), beneath which are **Top-Level Domains** (`.com`, `.org`, `.net`, country codes like `.uk`), then **second-level domains** (`example.com`), and possibly **subdomains** (`www.example.com`).
- **Recursive Resolver (Caching Resolver):** The server your machine normally sends queries to. It traverses the DNS tree on your behalf, caches answers, and returns the result to you. Examples: your ISP’s DNS, 8.8.8.8 (Google), 1.1.1.1 (Cloudflare).
- When you run `nslookup`, you are talking to a Resolver (usually provided by ISP or public like Google's `8.8.8.8`). If the resolver doesn't know the answer, it asks a chain of servers:
	- Root Servers (`.`): Points to the TLD servers.
	- Top Level Domain (TLD) Servers (`.com`, `.org`): Points to the Authoritative servers.
	- Authoritative Name Servers: The absolute source of truth for a specific domain (such as Amazon's Route53 servers hosting your company's domain).
- When you run a basic `nslookup`, the first line of the result almost always says `Non-authoritative anser`.
	- Authoritative: The answer cam directly from the server that hosts the domain's DNS zone file.
	- Non-authoritative: The answer came from a cache (ISP's DNS server or Google's `8.8.8.8`). The are repeating an answer they fetched earlier to save time.
- `nslookup` can pull various types of data. You need to specify these types when troubleshooting.

| **Record Type** | **Name**           | **What it does**                              | **Real-World Use**                                             |
| --------------- | ------------------ | --------------------------------------------- | -------------------------------------------------------------- |
| **A**           | Address            | Maps a hostname to an **IPv4** address.       | Finding a web server's IP.                                     |
| **AAAA**        | Quad-A             | Maps a hostname to an **IPv6** address.       | Finding modern web server IPs.                                 |
| **CNAME**       | Canonical Name     | Maps an alias to a true domain name.          | Pointing `[www.site.com](https://www.site.com)` to `site.com`. |
| **MX**          | Mail Exchange      | Directs email to a mail server.               | Troubleshooting missing emails.                                |
| **TXT**         | Text               | Holds text information.                       | Verifying domain ownership, SPF/DMARC (anti-spam).             |
| **NS**          | Name Server        | Lists the authoritative servers for a domain. | Checking who manages a domain's DNS.                           |
| **SOA**         | Start of Authority | Core info (admin email, refresh rates).       | Checking DNS propagation delays.                               |
| **PTR**         | Pointer            | Maps an IP back to a hostname (Reverse DNS).  | Verifying email server legitimacy.                             |
#### Operating Modes
- Non-Interactive Mode: Best for one-off queries or writing scripts. You issue the entire command on one line.
```bash
nslookup google.com
```
- Interactive Mode: Best for deep troubleshooting sessions where you need to run multiple queries against different servers. You type `nslookup` and hit Enter. Your prompt changes to a `>`.
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
#### More Options
- Inside interactive mode (or passed as flags), you can manipulate how `nslookup` behaves under poor network conditions:
	- `set timeout=X`: Changes how long it waits for a reply before giving up. Default is usually 5 sec.
	- `set retry=X`: Changes how many times it tries before failing, default is 4.
	- `set port=X`: DNS runs on port 53 by default. If you are testing a custom DNS server running on a non-standard port, use this.
	- `set d2`: Enables exhaustive debug mode; shows even more packet detail than standard `-debug`.
#### Modern Context
In the early 2000s, Unix engineers declared `nslookup` deprecated in favor of [[DIG]] and [[host]]. `dig` outputs raw, standard DNS responses natively, making it much easier to parse in scripts.