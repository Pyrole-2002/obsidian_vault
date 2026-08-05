- Gobuster is a fast, multi-threaded brute-force tool written in Go, used to enumerate directories, [[DNS]] subdomains, virtual hosts, Amazon S3 buckets, and fuzz parameters. It’s a staple for penetration testers and bug bounty hunters.
- **Concurrency**: Multi-threaded, adjustable threads
- **Modes**: Five distinct modes – `dir`, `dns`, `vhost`, `fuzz`, `s3`
- **Primary use**: Brute-forcing URIs, subdomains, virtual hosts, and parameterized paths
- **Output**: Plain text, CSV, JSON
- Gobuster does **not** do recursive scanning natively; it processes a wordlist against a single target base path. For recursive directory busting, you’d chain runs or use a wrapper script.
### Modes of Operation
Gobuster uses subcommands to switch between modes. The most common is `dir`.

|Mode|Command|Purpose|
|---|---|---|
|dir|`gobuster dir`|Enumerate directories and files on a web server|
|dns|`gobuster dns`|Brute-force DNS subdomains|
|vhost|`gobuster vhost`|Discover virtual hosts (name-based virtual hosting)|
|fuzz|`gobuster fuzz`|Fuzz a URL by replacing the word `FUZZ` in the path|
|s3|`gobuster s3`|Enumerate open S3 buckets (AWS)|
For these features, the behavior is essentially identical to [[Feroxbuster]]:
- **Custom headers** (`-H`, `--header`) – add multiple headers.
- **Cookies** (`-c`, `--cookie`).
- **User-Agent** (`-a`, `--useragent`).
- **Proxy** (`-p`, `--proxy`) – [[HTTP and HTTPS|HTTP]]/SOCKS5 proxy support.
- **Timeout** (`--timeout`) – per-request timeout.
- **TLS / insecure** (`-k`, `--no-tls-validation`) – ignore certificate errors.
- **Rate limiting / delay** – Gobuster uses `--delay` (milliseconds between requests) instead of a direct “requests per second” option; you can approximate a rate limit with `--delay` or combine with `-t`.
- **Filtering by status codes** (`-s`, `--status-codes` “include” and `-b`, `--status-codes-blacklist` “exclude”) – refer [[Feroxbuster]] inclusive/exclusive status code filtering.
- **Wordlist usage** (`-w`) – same idea, single wordlist per scan.
- **Extensions** (`-x`, `--extensions`) – append file extensions to words.
- **Output files** (`-o`) with automatic format detection (`.json`, `.csv`, `.txt` via extension).
- **Quiet mode** (`-q`) – output only results, no banner/progress.
- **Wildcard detection** – Gobuster does _not_ have built-in wildcard/soft-404 detection like Feroxbuster. You need to test manually and then filter by response size or word count.
- Filtering by size/length, status code inclusion/exclusion, and output formatting all follow the same logic as Feroxbuster.
### Differences from Feroxbuster
- **Recursion**: Gobuster does **not** recursively follow discovered directories. It enumerates only the base URL you give it. If you need recursion, you must script around it or use a wrapper.
- **Wildcard detection**: None built-in. You must identify “soft 404” behavior yourself and filter via `--exclude-length` or `--exclude-body`.
- **No heuristic auto-filtering**: There’s no automatic adjustment based on response patterns.
- **No state file/resume**: Gobuster cannot pause and resume a scan. You must plan runs that fit one session.
- **No configuration file**: Settings can’t be stored in a `config.toml`; you rely on shell aliases or scripts.
- **Threading model**: Gobuster also uses concurrent threads, but it’s synchronous goroutines rather than async Rust; performance is similar for typical tasks.
### Gobuster `dir` Mode
This is the direct equivalent of Feroxbuster’s default behavior.
```bash
gobuster dir -u https://example.com -w /usr/share/wordlists/dirb/common.txt
```

|Flag|Description|
|---|---|
|`-u, --url`|Target URL|
|`-w, --wordlist`|Path to wordlist|
|`-t, --threads`|Number of concurrent threads (default 10)|
|`-x, --extensions`|Comma-separated list of extensions (`php,txt,html`)|
|`-s, --status-codes`|Show only these status codes (inclusive filter, `200,301,302`)|
|`-b, --status-codes-blacklist`|Hide these status codes (exclusive filter, `404,500`)|
|`--exclude-length`|Exclude responses with this body length (useful for soft 404)|
|`--include-length`|Include only responses of this length|
|`--exclude-body`|Exclude responses containing a regex pattern in the body|
|`--include-body`|Include only responses matching a regex pattern in the body|
|`--no-status`|Don’t print status codes in output (cleaner)|
|`--no-progress`|Suppress the progress bar|
|`--method`|HTTP method to use (default `GET`)|
|`-d, --domain`|Used with `vhost` mode, not `dir`|
|`--expanded`|Show expanded (full) URLs|
|`-k, --no-tls-validation`|Skip TLS certificate verification|
|`--timeout`|HTTP timeout (default 10s)|
|`-r, --follow-redirect`|Follow redirects (default off)|
|`--add-slash`|Append `/` to each request (useful for directory-only busting)|
### Gobuster `dns` Mode
```bash
gobuster dns -d example.com -w subdomains.txt
```
- `-d` – domain to enumerate
- `-r` – custom resolver (e.g., `-r 8.8.8.8:53`)
- `--wildcard` – test wildcard DNS (e.g., if every subdomain resolves). Default behavior is to detect wildcard automatically. You can force with `--wildcard` or ignore results with `--wildcard-threads`. 
- `-i` – show IP addresses in output.
The output shows the discovered subdomains and their resolved IPs.
### Gobuster `vhost` Mode
Brute-force virtual hosts by sending HTTP requests with different `Host` headers to a single IP. You specify the base URL and a wordlist of vhost names.
```bash
gobuster vhost -u https://10.10.10.10 -w vhosts.txt
```
- Use the target IP/URL as `-u`.
- Optionally append a `--domain` if the server expects a base domain for Host header.
- Filter by status codes or response size just like `dir` mode (e.g., `--exclude-length` to filter out default page responses).
- Because this hits the same IP, you need to distinguish valid vhosts from the default response; filter by response size/content.
### Gobuster `fuzz` Mode
It works like a focused fuzzer: you supply a URL with the placeholder `{GOBUSTER}` or the word `FUZZ`, and Gobuster replaces that token with each wordlist entry.
```bash
gobuster fuzz -u https://example.com/api/FUZZ -w params.txt
```
- `-u` must contain `FUZZ` (or you can change the token with `--fuzz-pattern`).
- Supports filtering by response length, status codes, etc.
- You can fuzz multiple positions by using `FUZZ` multiple times and providing multiple wordlists with `-w w1 -w w2` (in order of appearance).
This is more flexible than `dir` mode for arbitrary parameter/endpoint fuzzing.
### Gobuster `s3` Mode
Enumerate open (or authenticated) Amazon S3 buckets using wordlist.
```bash
gobuster s3 -w bucket-names.txt
```
- `--s3-endpoint` – custom S3-compatible endpoint (for non-AWS, e.g., MinIO)
- `--requester-pays` – if bucket is requester-pays.
- `--region` – AWS region.
No equivalent in Feroxbuster, because Feroxbuster is purely HTTP directory fuzzing.
---