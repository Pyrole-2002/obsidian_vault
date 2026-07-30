- Searchsploit is a passive search tool. It is a cli for the Exploit-DB which is an archive of public exploits.
- Searchsploit does not run exploits. It simply searches a local, offline db or scripts and source code, allowing you to read the code, copy it to your machine, compile it and run it manually.
```bash
searchsploit [options] <search_term_1> <search_term_2>
```
- It uses an **AND** operator by default. If you search `searchsploit Samba 2.2`, it will only return exploits that have both terms in path or title.
- Searching flags
	- `-t` : Forces the tool to only search the exploit's title ignoring the author or file path.
	- `-c` : Performs a case-sensitive search.
	- `-e` : Searches for an exact string match rather than individual keywords `searchsploit -e "Samba 2.2.1a"`.
	- `--exclude` : Removes results containing specific words. Great for filtering out DoS attacks `searchsploit samba 2.2 --exclude="DOS`.
- Interaction flags
	- `-x` : Opens the exploit code directly in your terminal pager so you can read the author's instructions.
	- `-p` : Shows the absolute file path to the exploit on your machine and copies that path to clipboard. Stored in `/usr/share/exploitdb/exploits`.
	- `-m` : Recommended. Copies the exploit file directly into your current working dir so you can edit and compile it safely.
	- `-w` : Displays the Exploit-DB.com web URL instead of the local file path.
- DB maintenance flags
	- `-u` : Updates the local Exploit-DB using git. Run this periodically to pull down new exploits.
