- Domain Information Groper (`dig`) is a flexible, powerful cli tool for querying [[DNS]] name servers.
- It is the de facto standard for DNS troubleshooting and exploration. Unlike older tools like [[NSLookup]], `dig` provides consistent output, fine-grained control, and is designed for scripting.
- `dig` is used to:
	- Diagnose DNS resolution issues.
	- Verify that your DNS records are propagating correctly.
	- Retrieve all types of DNS records (A, MX, TXT).
	- Perform DNSSEC validation.
	- Trace the entire delegation path from the root servers.
	- Obtain output in machine readable formats (JSON, YAML).
### Syntax
```bash
dig [@server] [name] [type] [options]
```

| Component | Description                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| `@server` | The DNS server to query. If omitted, uses  system resolver (`/etc/resolv.conf`) |
| `name`    | The domain name to look up.                                                     |
| `type`    | The record type. Default `A`.                                                   |
| `options` | Additional query flags (prefixed with `+`) or standard arguments.               |
### Default Output
```
; <<>> DiG 9.18.12 <<>> example.com A
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 12345
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; QUESTION SECTION:
;example.com.           IN      A

;; ANSWER SECTION:
example.com.    3600    IN      A       93.184.216.34

;; Query time: 20 msec
;; SERVER: 8.8.8.8#53(8.8.8.8) (UDP)
;; WHEN: Mon Jan 01 12:00:00 UTC 2026
;; MSG SIZE  rcvd: 56
```
- **HEADER**: Status (`NOERROR`, `NXDOMAIN`, `SERVFAIL`), flags (`qr`=response, `rd`=recursion desired, `ra`=recursion available, `aa`=authoritative).
- **QUESTION**: What you asked.
- **ANSWER**: The resource records requested.
- **AUTHORITY**: Nameservers authoritative for the domain.
- **ADDITIONAL**: Glue records (IP addresses of those nameservers).
- **Footer**: Time taken, server used, message size.
### Output Formatting Flags
These “query options” start with `+` and are used to control the amount of output. They are vital for both readability and scripting.

| Flag          | Effect                                                        |
| ------------- | ------------------------------------------------------------- |
| `+short`      | **Terse answer only**. Ideal for scripts.                     |
| `+noall`      | Clears all standard output. Combine with others.              |
| `+answer`     | Show only the ANSWER section (combine with `+noall`).         |
| `+question`   | Show only QUESTION section.                                   |
| `+authority`  | Show AUTHORITY section.                                       |
| `+additional` | Show ADDITIONAL section.                                      |
| `+comments`   | Show comment lines (header, footer).                          |
| `+stats`      | Show query statistics.                                        |
| `+noquestion` | Suppress QUESTION section.                                    |
| `+nocomments` | Suppress comments.                                            |
| `+nostats`    | Suppress statistics footer.                                   |
| `+multiline`  | Show records in a verbose, multi-line format for readability. |
| `+yaml`       | Output in YAML format.                                        |
| `+json`       | Output in JSON format (useful for programmatic parsing).      |
### Changing Query Behavior
| Flag         | Description                                                                         |
| ------------ | ----------------------------------------------------------------------------------- |
| `+tcp`       | Force [[TCP]] instead of UDP (needed for large responses or AXFR).                  |
| `+notcp`     | Use only [[UDP]].                                                                   |
| `+time=T`    | Timeout in seconds (default: 5). `+time=10`                                         |
| `+tries=N`   | Number of UDP retries (default: 3).                                                 |
| `+retry=N`   | Number of times to retry a full query.                                              |
| `+bufsize=B` | Set EDNS buffer size (e.g., `+bufsize=512` to disable EDNS, `+bufsize=4096`).       |
| `+edns=0`    | Enable EDNS0 (sends additional OPT pseudo-record). Set version/options.             |
| `+noedns`    | Disable EDNS (useful for testing old servers).                                      |
| `+dnssec`    | Set the DNSSEC OK bit (DO), requesting DNSSEC records (RRSIG, etc.).                |
| `+ad`        | Set the Authentic Data bit (ask server to authenticate data).                       |
| `+cd`        | Set Checking Disabled bit (bypass DNSSEC validation on resolver).                   |
| `+recurse`   | Set RD (Recursion Desired) bit – ask server to perform recursion.                   |
| `+norecurse` | Unset RD – may return a referral if the server is not authoritative.                |
| `+nsid`      | Request NSID (Nameserver Identifier) from server (if supported).                    |
| `+expire`    | Send an EDNS Expire option.                                                         |
| `+subnet=IP` | Send EDNS Client Subnet (ECS) for geo-aware responses (e.g., `+subnet=1.2.3.4/24`). |
### Specifying Query Details
- **Query Type (record type):** Use the type directly: `dig example.com MX`, `dig example.com TXT`, `dig example.com NS`.
- **Query class (usually IN for Internet):** `dig example.com A +class=IN` (default is `IN`).
- **Reverse DNS Lookups (`-x`):** `dig -x 93.184.216.34` This automatically constructs the PTR query `34.216.184.93.in-addr.arpa`.
- **Batch Processing:** `dig -f file.txt` will process each line of `file.txt` as an independent `dig` command (same options are applied).
### Record types of DIG and [[NSLookup]] are same.
### Advanced Techniques
##### Trace DNS Delegation (`trace`)
- Simulated a resolver by following the chain from root servers down to the authoritative nameservers. Excellent for debugging delegation problems.
- No recursion flags needed; `+trace` disables them automatically.
```bash
dig +trace example.com A
```
##### Query a Specific DNS Server
- Override your system resolver:
```bash
dig @1.1.1.1 example.com
dig @8.8.8.8 example.com MX +short
```
- Also works with non-standard ports:
```bash
dig @ns1.example.com -p 5353 example.com
```
##### Authoritative vs. Recursive Answers
- Recursive (default from your resolver): `+recurse` (default)
- Authoritative answer from the domain's own nameserver:
```bash
dig @ns1.example.com example.com +norecurse +noall +answer
```
- Look for the `aa` flag in the header.
##### Testing EDNS0 and DNS Flag Day
- Many modern services require EDNS0. Check if a server supports it:
```bash
dig +short +bufsize=4096 example.com   # Normal EDNS
dig +short +bufsize=512 example.com   # Disable EDNS (may fail)
```
##### Forcing TCP
- Required for zone transfers (AXFR) or very large responses:
```bash
dig +tcp example.com ANY

# Zone Transfer (AXFR) - If allowed
dig @ns1.example.com example.com AXFR
```
> Most authoritative servers are properly secured and will refuse AXFR from unknown sources.
##### Getting NSID (Server Identification)
```bash
dig +nsid @8.8.8.8 example.com SOA
```
##### Check Propagation / Compare Multiple Resolvers
- Create a simple loop (bash):
```bash
for server in 8.8.8.8 1.1.1.1 9.9.9.9; do
    echo "--- Querying $server ---"
    dig +short @$server example.com
done
```
##### DNSSEC Validation Status
- Use `+dnssec` and inspect the `ad` flag or RRSIG records.
```bash
dig example.com +dnssec +multi
# If resolver validates, you'll see `ad` flag; also RRSIG records.

# To test a domain that should fail validation:
dig www.dnssec-failed.org +dnssec
```
##### Debugging with `+yaml` & `+json`
```bash
dig +json example.com | jq '.Answer[] | select(.type == "A") | .data'
```
##### Using `.digrc` for Default Options
- You can store default options in `$HOME/.digrc`:
```
+noall +answer +comments
+nocmd
```
- Now every `dig` command will automatically use these options. You can override them on the command line (`dig +all` to reset).
### Limitations
- **`+short` is great but hides errors.** For debugging, first run without it to see the response status.
- **`ANY` queries are unreliable.** Many public resolvers ignore them. Query specific types.
- **Some servers ignore `+subnet`** unless you're using a resolver that honors ECS (like Google Public DNS).
- **Case sensitivity:** Domain names are case-insensitive in DNS, but `dig` preserves case in output.
- **Firewall and UDP:** If your query times out, try `+tcp` (UDP might be blocked).
- **Negative caching:** After an `NXDOMAIN`, the negative TTL (from SOA `minimum` field) applies. You won't see a change until that expires.
---