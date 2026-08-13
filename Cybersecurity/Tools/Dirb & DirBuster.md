# `dirb` : Web Content Scanner
- `dirb` is a cli tool used in pentesting to discover hidden directories and flies on a web server.
- Operates exclusively over [[HTTP_HTTPS]]; unlike [[Nmap]] or [[Enum4linux]], it does not probe lower-level services or protocols.
- Launches a dictionary-based attack. It takes a wordlist, appends each entry to a target URL, and analyses the HTTP response codes to determine if a resource exists.
### Syntax
```bash
dirb <url_base> [<wordlist_file(s)>] [options]
```
- If no wordlist is provided, `dirb` uses its built‑in default list (`/usr/share/dirb/wordlists/common.txt` or similar).
- **Recursive by default** – when a valid directory is found, `dirb` automatically starts a new scan inside that directory with the same wordlist.
- Resource existence is judged by the HTTP status code:
    - `200 OK` – resource exists and is fully accessible.
    - `301/302 Redirect` – resource exists but forwards elsewhere.
    - `403 Forbidden` – resource exists but access is denied (often a great finding!).
    - `404 Not Found` – resource does not exist (hidden by default to keep output clean).
### Options
| Option                       | Description                                                                                 |
| ---------------------------- | ------------------------------------------------------------------------------------------- |
| `-X <ext>`                   | Append a single extension to every word (e.g., `-X .php`).                                  |
| `-x <file>`                  | Read a list of extensions from a text file and try each word with every extension.          |
| `-f`                         | Fine‑tune 404 detection to reduce false positives.                                          |
| `-r`                         | **Disable recursion** – stops `dirb` from automatically scanning discovered subdirectories. |
| `-R`                         | Interactive recursion – asks for permission before scanning each newly found directory.     |
| `-t`                         | Do not force a trailing `/` on URLs.                                                        |
| `-b`                         | Do not squash sequences like `/../` or `/./` in the given URL.                              |
| `-a <string>`                | Custom User‑Agent string (bypass simple filters).                                           |
| `-c <string>`                | Set a custom cookie for all requests.                                                       |
| `-H <header>`                | Add a custom HTTP header (e.g., `-H "X-Forwarded-For: 127.0.0.1"`).                         |
| `-u <user:pass>`             | Credentials for Basic HTTP authentication.                                                  |
| `-E <cert>`                  | Use a client certificate file for secure connections.                                       |
| `-p <proxy[:port]>`          | Route traffic through a proxy (e.g., `-p http://127.0.0.1:8080`).                           |
| `-P <proxy_user:proxy_pass>` | Proxy authentication credentials.                                                           |
| `-z <msecs>`                 | Add a delay (in milliseconds) between requests to evade rate limits.                        |
| `-o <file>`                  | Save scan results to a file.                                                                |
| `-S`                         | Silent mode – shows only successful discoveries.                                            |
| `-i`                         | Case‑insensitive search.                                                                    |
| `-l`                         | Print the `Location` header when a redirect is found.                                       |
| `-N <code>`                  | Ignore responses with a specific status code (e.g., `-N 403`).                              |
| `-v`                         | Verbose mode – also show non‑existent pages (404).                                          |
| `-w`                         | Do not stop on WARNING messages.                                                            |
- **Wordlists**: The default wordlist is small; for thorough scans use larger ones (e.g., `/usr/share/seclists/Discovery/Web-Content/common.txt`, `directory-list-2.3-medium.txt`).
- **Authentication**: When targeting a site behind HTTP Basic Auth, the `-u` flag is essential.
- **Performance**: `dirb` is single‑threaded; large scans can be slow. Use `-z` to respect server limits.
- **False Positives**: Some servers return 200 for every request (soft 404s). Use `-f` and inspect results manually.
---
# DirBuster : Directory Brute-Forcer
- DirBuster is a **Java‑based GUI application** developed by OWASP for brute‑forcing directories and files on web servers.
- It shares the same core concept as `dirb` but offers a graphical interface, multi‑threading, and additional attack modes.
- **Multiple Attack Modes**:
    - **Pure Brute‑force:** Generates combinations of characters (letters, numbers) to discover resources (`aaa`, `aab`). This does not rely on a wordlist.
    - **Dictionary‑Based:** Uses a wordlist file (often `directory-list-2.3-medium.txt` or the bundled `directory-list-1.0.txt`).
    - **Hybrid:** Combination of dictionary and brute‑force (dictionary words with appended numbers).
- **Multi‑Threading:** Configurable number of threads (default 10) to speed up scanning.
- **Recursion Options**:
    - Can be set to recursive, non‑recursive, or to a specific depth.
    - Follow redirects or treat them as “found” items.
- **Pause / Resume:** Long scans can be paused and later resumed.
- **Report Generation:** Exports results as text, CSV, or HTML.
- **Custom Headers & Authentication**:
    - Custom User‑Agent.
    - Basic, Digest, NTLM authentication.
    - Custom cookies.
- **Proxy Support:** Configure HTTP proxy for traffic analysis (e.g., Burp Suite).
- **URL Fuzzing:** Replace part of the URL with `{dir}` placeholder to scan within a specific path structure.
---
# `dirb` vs. DirBuster vs. [[Feroxbuster]] vs. [[Gobuster]]
|Feature|`dirb`|DirBuster|**Feroxbuster**|**Gobuster**|
|---|---|---|---|---|
|**Language**|C|Java|Rust|Go|
|**Interface**|CLI only|GUI (Java)|CLI only|CLI only|
|**Speed**|Slow (single‑threaded)|Moderate (multi‑threaded)|Very fast (recursive, parallel)|Fast (multi‑threaded, but no recursion built‑in)|
|**Recursion**|Enabled by default|Optional, depth‑limited|Enabled by default, depth control|Not natively supported (must chain multiple scans)|
|**Attack Modes**|Dictionary only|Dictionary + Pure Brute‑force|Dictionary only|Dictionary only (separate modes: dir, dns, vhost, etc.)|
|**Wordlist**|Small built‑in|Small built‑in|No built‑in; must specify|No built‑in; must specify|
|**Threading**|Single‑threaded|Configurable threads|Configurable threads (default 50)|Configurable threads (via `-t`)|
|**Filtering**|Status code ignore (`-N`)|Status code & length filter|Extensive filters: status, size, line count, word count, regex|Status code blacklist/whitelist, length filter|
|**Authentication**|Basic (`-u`), cookie (`-c`), custom headers (`-H`)|Basic, Digest, NTLM, cookies, custom headers|Basic, Bearer token, cookies, custom headers|Basic, cookies, custom headers|
|**Proxy**|`-p`|Built‑in proxy config|`--proxy`|`--proxy`|
|**Extensions**|`-X` (single) or `-x` (file)|GUI‑based file extension list|`-x` (multiple)|`-x` (multiple)|
|**False‑Positive Handling**|`-f` flag|None|Auto‑detection of soft 404s via `--filter-status` etc.|Manual; use `-b` to blacklist codes|
|**Output Formats**|Plain text (`-o`)|Text, CSV, HTML|Plain, JSON, CSV|Plain, JSON, CSV|
|**Ease of Use**|Simple for quick scans|GUI‑driven; good for beginners|Modern CLI with rich options, fast and flexible|Straightforward CLI; no recursion may be limiting|
|**Best Use Case**|Quick, low‑intensity scans; legacy environments|Visual analysis, brute‑force of short directory names|Comprehensive, high‑speed recursive discovery|Fast non‑recursive scans, DNS/VHOST enumeration|

---