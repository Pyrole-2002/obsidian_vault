- `dirb` is a web content scanner used in pentesting to find hidden directories and files on a web server.
- Unlike [[Nmap]] or [[Enum4linux]], which probe services and protocols, `dirb` operates exclusively over HTTP/HTTPS.
- It works by launching a dictionary based attack against a web server, taking a list of common directory and file names, appending them to a target URL, and analyzing the response codes.
```bash
dirb <url_base> [<wordlist_file(s)>] [options]
```
- If  you do not specify a wordlist, `dirb` automatically uses its default list.
- By default, it is recursive. If it finds a valid directory, it will automatically start a brand new scan inside that directory using the same wordlist.
- It decides if a file or directory exists based on the HTTP status code the server sends back.
	- 200 (OK): The file or directory exists and is fully accessible.
	- 301/302 (Redirect): The object exists but forwards you somewhere else.
	- 403 (Forbidden): The directory or file exists, but the web server is configured to deny you access (Great Finding).
	- 404 (Not Found): The object does not exist. By default, `dirb` hides these to keep your screen clean.
- File Extensions & Scope Options
	* **`-X <ext>`** : Append a specific file extension to every word (e.g., `-X .php`).
	* **`-x <file>`** : Amplify search with a list of extensions provided in a text file.
	* **`-f`** : Fine-tuning of NOT_FOUND (404) detection to help prevent false positives.
	* **`-r`** : Don't search recursively (stops `dirb` from automatically scanning discovered subdirectories).
	* **`-R`** : Interactive Recursion (asks you for permission before scanning inside newly discovered directories).
	* **`-t`** : Don't force an ending `/` on URLs.
	* **`-b`** : Don't squash or merge sequences of `/../` or `/./` in the given URL.
* Evasion, Headers & Authentication
	* **`-a <agent_string>`** : Specify a custom HTTP User-Agent string to bypass filters.
	* **`-c <cookie_string>`** : Set a custom session cookie for the HTTP requests.
	* **`-H <header_string>`** : Add a custom HTTP header to all requests.
	* **`-u <username:password>`** : Provide credentials to bypass basic HTTP authentication.
	* **`-E <certificate>`** : Use a specified client certificate file for secure connections.
	* **`-p <proxy[:port]>`** : Route all scanner traffic through a proxy (e.g., `-p http://127.0.0.1:8080`).
	* **`-P <proxy_user:proxy_pass>`** : Provide authentication credentials for your proxy.
	* **`-z <msecs>`** : Add a delay (in milliseconds) between requests to throttle the scan and evade rate limits.
* Output & Display Controls
	* **`-o <output_file>`** : Save the scan results directly to a file on your disk.
	* **`-S`** : Silent Mode (hides the ongoing list of tested words; displays only the successful discoveries).
	* **`-i`** : Use case-insensitive search.
	* **`-l`** : Print the HTTP `Location` header when a redirect (301/302) is found.
	* **`-N <status_code>`** : Ignore responses returning this specific HTTP code (e.g., `-N 403` to hide forbidden errors).
	* **`-v`** : Verbose mode (shows non-existent pages as well).
	* **`-w`** : Don't stop the scan when encountering WARNING messages.