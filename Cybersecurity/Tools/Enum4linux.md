- `enum4linux` is a Perl wrapper that automates several built-in Samba tools to extract as much information as possible from Windows and Samba (Linux/Unix [[SMB]]) hosts. It gathers data via **SMB** and **NetBIOS** without needing valid credentials, by leveraging a **null session** (anonymous, unauthenticated connection).
- The tools it orchestrates:
	- `smbclient` – list shares, attempt null connection
	- `rpcclient` – query user/group info, password policy via RPC
	- `net` (Samba’s) – RPC commands and share enumeration
	- `nmblookup` – NetBIOS name resolution
- Internal/CTF reconnaissance against a Windows or Samba target
- When ports **139/445** (NetBIOS/SMB) are open
- Early stage of a penetration test to collect:
	- Valid usernames (for password attacks)
	- Available file shares
	- Domain/Workgroup details
	- Password policy (min length, lockout)
	- OS version information
	- Group memberships (who might be an admin)
### Null Sessions
- A **Null Session** is an anonymous SMB connection using empty credentials (username `''`, password `''`).
- Older Windows versions (NT, 2000, XP, 2003) often allowed this by default. Modern Windows (10/11, Server 2016+) and properly configured Samba **disable null sessions**, but you can still test; sometimes legacy systems or misconfigured appliances remain vulnerable.
- Even when a full null session is denied, partial information leakage is common (e.g., NetBIOS names via [[NMBLookup]]).
### Syntax
```bash
enum4linux [options] <target_ip>
```

| Flag | Long Flag (if exists)     | Action                                                                                                                                                          |
| ---- | ------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `-a` | Do all simple enumeration | Runs **-U -S -G -P -r -o -n -i**. This is your default “spray and pray” scan.                                                                                   |
| `-U` | `--users`                 | List users via [[RPC]] (SMB/RPC null session). Yields usernames for brute-force attacks ([[SSH]], SMB, etc.).                                                   |
| `-S` | `--shares`                | Enumerate shared folders (UNC paths like `\\target\IPC$`, `\\target\C$`). Reveals which directories are accessible—some might be readable/writable anonymously. |
| `-G` | `--groups`                | Dump groups and their members. Instantly see which users belong to `Domain Admins` or `Administrators`.                                                         |
| `-P` | `--password-policy`       | Retrieve the account password/complexity/lockout policy. Critical for choosing brute-force speed (avoiding lockouts).                                           |
| `-r` | `--rid-cycling`           | Enumerate users via RID cycling (brute-force RIDs). Works when `-U` fails because NetUserEnum is restricted.                                                    |
| `-o` | `--os-information`        | Pull OS details (version, build, service pack) from SMB/RPC responses.                                                                                          |
| `-n` | `--netbios`               | NetBIOS name lookup and enumeration (via `nmblookup`). Returns machine name, domain/workgroup, sometimes user info in name suffix.                              |
| `-i` | `--ipc-info`              | Enumerate IPC$ share information (what’s available via named pipes).                                                                                            |
| `-N` | `--no-pass`               | Force no password. Default, but explicit if needed.                                                                                                             |
| `-u` | `--user <user>`           | Attempt connection as a specific user (if you already have a username).                                                                                         |
| `-p` | `--password <pass>`       | Provide password for the user. Turns null session into authenticated enumeration—often yields richer results.                                                   |
| `-w` | `--workgroup <WORKGROUP>` | Specify workgroup/domain (otherwise auto-detected).                                                                                                             |

> **Note:** The `-a` flag is convenient but noisy. In a real engagement you often run targeted flags first to avoid detection and understand what’s exposed.
### Limitations
- **Null sessions often blocked** on modern systems (Windows 10/11, Server 2016+, patched Samba). You may get `NT_STATUS_ACCESS_DENIED`. Try `--user` and `--password` if you have even low-privilege creds.
- **RPC over SMB** can be firewalled. Port 445 must be open and reachable.
- **SMB signing** may prevent certain calls; `enum4linux` may fall back to less informative methods.
- **NetBIOS (139)** is sometimes blocked, so `-n` may fail. That’s okay if SMB is up.
- **Slow over high-latency links**; RID cycling (`-r`) can take a while.
- If you get incomplete data, run individual tools manually (see below) for finer control.
### Manual Alternatives when `enum4linux` Fails
Because `enum4linux` is just a wrapper, you can run its components directly for debugging or to access features the script doesn’t expose:
```bash
# NetBIOS lookup
nmblookup -A <target>

# List shares via smbclient
smbclient -L //target -N

# Connect anonymously to a share
smbclient //target/share -N

# rpcclient with null session
rpcclient -U "" -N <target>
# Inside rpcclient:
rpcclient $> enumdomusers       # -U equivalent
rpcclient $> enumalsgroups builtin   # group info
rpcclient $> querydominfo       # password policy / OS info
rpcclient $> lsaenumsid         # SID enumeration
rpcclient $> lookupnames <user>  # SID of specific user
rpcclient $> queryuser <rid>    # user details
```
### Modern `enum4linux-ng`
`enum4linux` is no longer actively maintained. `enum4linux-ng` (Next Generation) is a Python rewrite with better error handling, JSON/YAML output, and improved support for modern SMB. Same purpose, more reliable.
```bash
enum4linux-ng -A 192.168.1.10        # all simple enumeration
enum4linux-ng -U 192.168.1.10        # users
enum4linux-ng -oA output 192.168.1.10  # output to all formats
```
---