- Feroxbuster is a blazingly fast, recursive, multi-threaded content delivery tool written in Rust.
- It is used by pentesters, bug bounty hunters, and security researchers to brute-force directories, files, and API endpoints on web servers.
- **Extremely fast**: Built in Rust, leverages async I/O and multi-threading.
- **Recursive scanning**: Automatically dives into discovered directories.
- **Wildcard detection**: Identifies "soft 404" pages to avoid false positives.
- **Smart filtering**: Filter by HTTP status codes, response size, line count, word count, and regex.
- **Multiple wordlists**: Specify several wordlists, each with optional extensions.
- **Advanced recursion control**: Set depth limits, force recursion on specific status codes, or use dynamic depth.
- **Output versatility**: JSON, CSV, HTML report, and plain text.
- **Resume capability**: Pause and resume scans via state files.
- **Proxy support**: Route traffic through [[HTTP_HTTPS|HTTP]]/SOCKS proxies.
- **Rate limiting & timeouts**: Fine-tune to avoid overwhelming targets.
- **Configuration files**: Set defaults in `ferox-config.toml`.
- **Heuristic filtering**: Automatically adjust filters based on observed responses.
```bash
feroxbuster -u https://example.com -w /path/to/wordlist.txt
```
- This will:
	- Enumerate directories and files using `GET` requests.
	- Use 50 concurrent threads (default).
	- Follow redirects.
	- Print results in a clean, colored terminal output.
- Inclusive filters show **only** matching results; exclusive filters **hide** matching results.
### Options
| Flag                   | Description                                                                                |
| ---------------------- | ------------------------------------------------------------------------------------------ |
| `-u, --url`            | Target URL (required)                                                                      |
| `-w, --wordlist`       | Path to wordlist file(s) (can be used multiple times)                                      |
| `-t, --threads`        | Number of concurrent threads (default: 50)                                                 |
| `-T, --timeout`        | Request timeout in seconds (default: 7)                                                    |
| `--rate-limit`         | Limit requests per second (e.g., `10`)                                                     |
| `-p, --proxy`          | Proxy URL (`http://127.0.0.1:8080` or `socks5://...`)                                      |
| `-S, --status-codes`   | Include. Status codes to show (e.g., `200,301`)                                            |
| `-f, --filter-status`  | Exclude. Status codes to hide                                                              |
| `--filter-size`        | Exclude. Hide responses of given size                                                      |
| `--filter-line-count`  | Exclude. Hide responses with given line count                                              |
| `--filter-word-count`  | Exclude. Hide responses with given word count                                              |
| `--filter-regex`       | Exclude. Hide responses matching regex (body)                                              |
| `--match-size`         | Include. Show only responses of given size                                                 |
| `--match-line-count`   | Include. Show only responses with given line count                                         |
| `--match-word-count`   | Include. Show only responses with given word count                                         |
| `--match-regex`        | Include. Show only responses matching regex (body)                                         |
| `-r, --recursive`      | Enable recursive scanning (default: off)                                                   |
| `-d, --depth`          | Maximum recursion depth (default: 4)                                                       |
| `--force-recursion`    | Force recursion on all found directories, ignoring filter limits                           |
| `--recursion-status`   | Status codes that trigger recursion (default: `200,204,301,302,307,308,401,403,405`)       |
| `-x, --extensions`     | Append extensions to each word (e.g., `php,html,js`)                                       |
| `--add-slash`          | Append `/` to each request (useful for directory-only enumeration)                         |
| `--dont-extract-links` | Disable extraction of links from response bodies for additional discovery                  |
| `--collect-backups`    | Attempt to discover backup files                                                           |
| `--collect-words`      | Collect words from responses and use them for further enumeration                          |
| `-o, --output`         | Output file for results (determines format by extension: `.json`, `.csv`, `.html`, `.txt`) |
| `--json`               | Force JSON output                                                                          |
| `--csv`                | Force CSV output                                                                           |
| `--html-report`        | Force HTML report                                                                          |
| `--no-state`           | Disable state file (prevents resuming)                                                     |
| `--resume`             | Resume from a previous scan’s state file                                                   |
| `--quiet`              | Only print URLs found (no progress)                                                        |
| `-v, --verbosity`      | Increase log verbosity (use multiple times)                                                |
| `-H, --header`         | Add custom header(s) (can be used multiple times)                                          |
| `-b, --cookie`         | Specify cookie(s)                                                                          |
| `-U, --user-agent`     | Set a custom User-Agent                                                                    |
| `--insecure`           | Disable TLS certificate validation                                                         |
| `--methods`            | HTTP methods to use (default: `GET`)                                                       |
| `-q, --query`          | Query parameters for each request                                                          |
| `--redirects`          | Follow redirects (default: true)                                                           |
| `--dont-filter`        | Disable automatic filtering entirely                                                       |
| `--force-recursion`    | Recursively scan even if response matches a filter                                         |
> Use `-f 404` to hide the common 404 noise, or `-S 200,204,301,302,307,401,403,405` to show only interesting codes.\
### Configuration File
- You can save commonly used flags in a `ferox-config.toml` file. By default, `feroxbuster` looks for this file in the current directory or `$HOME/.config/feroxbuster/`.
- Command-line flags override the config file.
```toml
wordlist = "/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt"
threads = 100
timeout = 5
status_codes = [200, 204, 301, 302, 307, 401, 403, 405]
filter_status = [404]
recursive = true
depth = 3
output = "/home/user/scans/ferox-report.json"
proxy = "http://127.0.0.1:8080"
insecure = true
extensions = ["php", "html", "js"]
```
### Best Practices
1. **Start small, then scale up**: Use a small wordlist first to map the site structure, then deeper wordlists on interesting paths.
2. **Always check for wildcards**: Feroxbuster does it automatically, but look for the `WLD` tag in results – those responses may be false positives.
3. **Respect the target**: Use `--rate-limit` and sensible thread counts to avoid DoSing the server.
4. **Combine with other enumeration**: Feed discovered directories into tools like `nuclei` or manual review.
5. **Leverage `--collect-backups`**: It automatically appends common backup extensions (`.bak`, `.old`, `.swp`, etc.) to found files.
6. **Use `--add-slash` when hunting for directories only** – this avoids file hits and keeps output clean.
7. **State files are your friend**: If a scan is large, always allow state files to resume later.
```bash
# Phase 1: Quick directory map
feroxbuster -u https://target.com -w /usr/share/seclists/Discovery/Web-Content/common.txt \
  -r -d 2 --add-slash -o dirs.json

# Phase 2: File fuzzing with extensions on discovered directories
for dir in $(jq -r '.[] | select(.status_code==200) | .url' dirs.json); do
  feroxbuster -u "$dir" -w /usr/share/seclists/Discovery/Web-Content/raft-medium-files.txt \
    -x php,asp,aspx,jsp,html,txt,bak -o "files_$(echo $dir | tr '/' '_').json"
done

# Phase 3: API discovery on subdomain
feroxbuster -u https://api.target.com -w /usr/share/seclists/Discovery/Web-Content/api/objects.txt \
  -H "Accept: application/json" -S 200,201,401,403 -o api.json
```