- It is an open-source cli utility included in the Samba suite.
- It was initially developed by developers to test [[MS-RPC]] functionality natively within Samba.
- Cross-Platform Interaction: It acts as a client interface, allowing UNIX and Linux workstations to execute MS-RPC functions and communicate directly with Windows systems.
- System admins frequently use `rpcclient` in custom scripts to remotely manage Windows NT clients from their Linux machines.
- It requires port 139 and 445 to be open and accepting SMB connections. If both are filtered by a firewall, the tool with hang or timeout.
- Once `rpcclient $>` prompt is acquired, a pentester or attacker can use it to map out the target's internal structure.
	- System Enumeration: The tool provides commands to query the target for detailed information, such as domain users, local users, and network groups.
	- Password Policies: Attackers can retrieve the domain's password policy, including min password lengths and account lockout thresholds, which is critical for planning a brute-force attack.
	- SID Resolution: `rpcclient` can resolve usernames to their Security Identifiers (SIDs) or vice-versa, revealing the internal identification structure of the network.
	- Null Sessions: Operating RPC calls via unauthenticated Null Sessions is highly dangerous for the target. It allows attackers to scrape all of the sensitive enumeration data mentioned above without needing a valid account on the system.
```bash
rpcclient [options] <target_IP_or_hostname>
```
- If you are attacking an older system, you usually aim for a Null Session (unauthenticated access).
- `-U` : Specifies the username to connect with. `-U ""` is for Null Session.
- `-N` : Not to ask for a password, for scripting Null Session checks.
- `-W` : Specifies the target domain or workgroup name if required by the server.
- `-p` : Specifies the port to connect to. It defaults to 445 (SMB over TCP) but can be forced to 139 (NetBIOS) if 445 is closed.
- `-c` : Executes a semicolon-separated list of commands and exits immediately without entering the interactive prompt. Excellent for bash scripting.
## Interactive Commands
- Once you see the `rpcclient $>` prompt, you use built-in MS-RPC commands.
- Server and Share Enumeration:
	- `srvinfo` : Displays basic server information, including the OS version and platform ID.
	- `netshareenumall` : Lists all available network shares including hidden admin shares like `IPC$` and `ADMIN$`.
	- `netsharegetinfo <share>` : Provides detailed information about a specific share.
- User Enumeration:
	- `enumdomusers` : Dumps the complete list of users on the system along with their Relative Identifiers (RIDs).
	- `queryuser <RID>` : Pulls detailed information about a specific user account using their RID. Shows full names, descriptions, and logon data.
	- `querydispinfo` : Displays a slightly different formatted, highly readable list of users, their RIDs, and their account descriptions.
- Group Enumeration:
	- `enumdomgroups` : Lists all domain or local groups on the system and their RIDs.
	- `querygroup <RID>` : Displays details about a specific group.
	- `querygroupmem <RID>` : Lists the RIDs of all users who are members of that specific group.
- SID and RID Manipulation:
	- `lsaquery` : Retrieves the main SID for the domain or local machine.
	- `lookupnames <username>` : Converts a known username into its corresponding SID and RID.
	- `lookupsids <SID>` : Converts a known SID back into a human-readable username.