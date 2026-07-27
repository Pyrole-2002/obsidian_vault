- It is an information-gathering tool used to extract data from Windows machines and Linux machines running Samba (software that allows Linux to share files over a network like Windows).
- It automates running a bunch of older, built-in Samba commands (`smbclient`, `rpcclient`, `net`, `nmblookup`).
- It works by attempting to establish a Null Session — an anonymous, unauthenticated connection to the target machine.
```bash
enum4linux [flags] [target_ip]
# -a: run every standard enumeration check
# -U: extracts valid usernames, later brute force via SSH or SMB
# -S: lists available network shares (folders made public on network)
# -G: shows user groups, tells if user has admin privileges
# -p: reveals rules like minimum password length or account lockout thresholds
# -o: attempts tp pull exact OS details based on network responses
```

