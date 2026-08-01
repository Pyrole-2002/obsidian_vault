- MS Defender XDR correlates millions of individual threat signals across endpoints, identities, cloud apps, and email to build a unified incident timeline.
- Managing this environment requires deploying robust automation rules, managing device groups, and tuning notif mechanisms.
### Alert Tuning
- To ensure the Security Operations Center (SOC) is not overwhelmed by false positives, admins must configure alert tuning and threshold-based notifs.
- Alert tuning rules automatically resolve or hide alerts based on predefined conditions, ensuring that analysts only review high-fidelity incidents.
- Configuring alert tuning in Defender XDR:
	1. Navigate to the ***MS Defender Portal***.
	2. Select ***Settings*** in the bottom left navigation pane.
	3. Select ***MS Defender XDR***.
	4. Under the ***Rules*** section, select ***Alert Tuning***.
	5. Select ***Add tuning rule***.
	6. Define the logic by specifying the `Alert title`, `Severity`, and the specific `Entity` (such as a known vulnerability scanner IP address).
### Rear Wheel Drive Engine Idle Clutch Accelerate
![[Pasted image 20260727023449.png|576]]
![[Pasted image 20260727171504.png|856]]
# Threat Protection with Microsoft Defender XDR (Extended Detection & Response)
![[Pasted image 20260727192525.png|1221]]
## Threat Investigation with MS Graph Security API
- The MS Graph Security API is an intermediary service (broker) that provides programmatic connections to multiple MS Graph Security Providers.
- It is a RESTful web API. After you register your app and get authentication tokens for a user or service, you can make requests to the MS Graph API.
- You can use the Graph Explorer to call the Security API but you must have the required permissions and be authenticated.
## Incidents
- Defender XDR groups related alerts, compromised assets, and automated investigations into a single pane of glass called an Incident.
- The Incident Graph visually maps the blast radius, illustrating the relationships between a malicious email delivery, the identity that clicked the payload, and the endpoint where execution occurred.
## Advanced Hunting
- It allows security analysts to perform cross-domain KQL queries directly against the unified XDR telemetry. From these queries, custom detection rules can be authored. To properly generate an incident, the custom detection query must project specific entity identifiers such as `Timestamp`, `DeviceId`, or `AccountObjectId`.
- This is a query based threat-hunting tool that lets you explore up to 30 days of raw data.
- You can proactively inspect events in your network to locate threat indicators and entities.
- The flexible access to data enables unconstrained hunting for both known and potential threats.
```sql
--// MITRE ATT&CK: T1048 - Exfiltration Over Alternative Protocol
--// Purpose: Detect bulk sensitive file activity in Microsoft Teams indicating potential data exfiltration
let timeWindow = 1h;
let messageThreshold = 20;
--// Define trusted external domains to filter out legitimate business collaboration
let trustedDomains = dynamic(["trustedpartner.com", "anothertrusted.com"]);

CloudAppEvents
| where Timestamp > ago(timeWindow)
| where ActionType in ("FileDownloaded", "FileShared")
| where Application == "Microsoft Teams"
| where AccountName !endswith any (trustedDomains)
--// Summarize the volume of files touched by the identity within 5 minute bins
| summarize FileCount = count(), FileNames = make_set(ObjectName) by AccountName, IPAddress, bin(Timestamp, 5m)
--// Filter for activity exceeding the defined anomaly threshold
| where FileCount > messageThreshold
--// Projecting specific columns ensures the Custom Detection engine maps the entities correctly
| project Timestamp, AccountName, IPAddress, FileCount, FileNames
```
## Entra ID Sign-in Logs
- When hunting Entra ID sign-in logs using Kusto Query Language (KQL), the table names are different based on where you access the logs.
- In MS Defender Threat Hunting the table name is AADSignInEventsBeta.
- In MS Sentinel Logs the table name is SigninLogs.
## MS Defender for Office 365
- This is a cloud based email filtering stack that can be broken out into 4 phases of protection.
- Incoming mail passes through all these phases before delivery, but the actual path email takes is subject to an org's Defender for Office 365 config.
![[Pasted image 20260727195648.png|940]]
## MS Defender for Identity
- In Defender XDR portal, the Defender for Identity workspace displays the data received from Defender for Identity sensors.
- Defender for Identity sensors:
	- Domain controller sensor monitors domain controller traffic.
	- AD FS/AD CS sensor monitors network traffic and authentication.
- Defender for Identity cloud service runs on Azure infra and is connected to Microsoft's intelligent security graph.
### Lateral Movement Paths (LMPs)
- Defender for Identity LMPs are visual guides that help you to quickly understand and identify exactly how attackers can move laterally inside your network.
- Identities discovered by Defender for Identity to be in an LMP have LMP information under the Observed in organization tab of User page.
## Entra ID Protection
![[Pasted image 20260727201611.png|873]]![[Pasted image 20260727201827.png|888]]![[Pasted image 20260727202204.png|807]]
- Microsoft's recommended risk policy config:
- **User risk policy**
	- Require a secure password change when user risk level is High. MS Entra multifactor authentication is required before the user can create a new password with password writeback to remediate their risk.
	- A secure password change using self-service password reset is the only way to self-remediate user risk regardless of the risk level.
- **Sign-in risk policy**
	- Require MS Entra multifactor authentication when sign-in risk level is Medium or High, allowing users to prove it's them by using one of their registered authentication methods, remediating the sign-in risk.
## MS Defender for Cloud Apps
- Defender for cloud apps deals is built to:
	- Discovering and control the user of Shadow IT.
	- Protect your sensitive info anywhere in the cloud.
	- Protect against cyberthreats and anomalies.
	- Assess the compliance of your cloud apps.
- Shadow IT is the use of software, hardware, or cloud services inside a company without the knowledge or approval of the official IT department.
## MS Defender for Cloud
- It is:
	- A Development Security Operations (DevSecOps) solution that unifies security management at the code level across multi-cloud and multi-pipeline environments.
	- A Cloud Security Posture Management (CSPM) solution that surfaces actions that you can take to prevent breaches.
	- A Cloud Workload Protection Platform (CWPP) with specific protections for servers, containers, storage, databases, and other workloads.
- To enable all Defender for Cloud features including threat protection capabilities, you must enable enhanced security features on the subscription containing the applicable workloads.
### Asset Summary
Inventory Summary:
- Total Resources
- Unhealthy Resources
- Unmonitored Resources
- Unregistered Subscriptions
### Configure MS Defender for Cloud
- It automatically onboards the Azure resources and non-Azure resources by installing extensions.
- When you enable an extension, it will be installed on any new or existing resource, by assigning a security policy.
### Connect non-Azure Assets to MS Defender for Cloud
- Azure Arc simplifies governance and management by delivering a consistent multi-cloud and on-premises management platform.
- Azure Arc Enabled:
	- Install the Azure Connected Machine agent on non-Azure hosts directly with Azure Arc.
	- Add Arc connected Windows machines to a Data Collection Rule (DCR) resource to install the Azure Monitor Agent (AMA).
	- Add Azure Arc connected Linux machines as a DCR resource to install the AMA.
- Azure Portal: Workspace level direct onboarding.
- Defender for Endpoint:
	- Tenant level direct onboarding.
	- Enabled in MS Defender for Cloud environment settings.
	- Uses MS Defender for Endpoint onboarding methods.
- Use MS Defender for Cloud Attack Path Analysis and Security Explorer to scan and query the cloud security graph.
- Attack path analysis exposes attack paths and suggests recommendations as how to best remediate issues that will break the attack path and prevent successful breach.
- Use Cloud Security Explorer query builder to run graph based queries.
### Cloud Security Posture Management
<table style="border-collapse: collapse; width: 100%; text-align: center;">
  <thead>
    <tr>
      <th style="background-color: #FFC09F; color: #000000; padding: 12px; border: 1px solid var(--background-modifier-border); vertical-align: middle;">Service Models</th>
      <th style="background-color: #F498F5; color: #000000; padding: 12px; border: 1px solid var(--background-modifier-border); vertical-align: middle;">Cloud Computing Service Provider</th>
      <th style="background-color: #8CD3FF; color: #000000; padding: 12px; border: 1px solid var(--background-modifier-border); vertical-align: middle;">Category</th>
      <th style="background-color: #FFD4D4; color: #000000; padding: 12px; border: 1px solid var(--background-modifier-border); vertical-align: middle;">Name of Secure Score Functionality</th>
      <th style="background-color: #FFE5D4; color: #000000; padding: 12px; border: 1px solid var(--background-modifier-border); vertical-align: middle;">Administration Portal</th>
    </tr>
  </thead>
  <tbody>
    <!-- SaaS Section -->
    <tr>
      <td style="font-weight: bold; padding: 12px; border: 1px solid var(--background-modifier-border); vertical-align: middle;">SaaS</td>
      <td style="padding: 12px; border: 1px solid var(--background-modifier-border); vertical-align: middle;">Microsoft 365</td>
      <td style="padding: 12px; border: 1px solid var(--background-modifier-border); vertical-align: middle;">Identity, Devices and Apps</td>
      <td style="padding: 12px; border: 1px solid var(--background-modifier-border); vertical-align: middle;">Microsoft Secure Score</td>
      <td style="padding: 12px; border: 1px solid var(--background-modifier-border); vertical-align: middle;">Microsoft 365 Security center</td>
    </tr>

    <!-- PaaS Section -->
    <tr>
      <td rowspan="3" style="font-weight: bold; padding: 12px; border: 1px solid var(--background-modifier-border); vertical-align: middle;">PaaS</td>
      <td style="padding: 12px; border: 1px solid var(--background-modifier-border); vertical-align: middle;">Azure</td>
      <td style="padding: 12px; border: 1px solid var(--background-modifier-border); vertical-align: middle;">Feature Coverage for Azure PaaS Services</td>
      <td rowspan="3" style="padding: 12px; border: 1px solid var(--background-modifier-border); vertical-align: middle;">Secure Score</td>
      <td rowspan="3" style="padding: 12px; border: 1px solid var(--background-modifier-border); vertical-align: middle;">Microsoft Defender for Cloud Dashboard</td>
    </tr>
    <tr>
      <td style="padding: 12px; border: 1px solid var(--background-modifier-border); vertical-align: middle;">AWS</td>
      <td style="padding: 12px; border: 1px solid var(--background-modifier-border); vertical-align: middle;">Provided by AWS Security Hub</td>
    </tr>
    <tr>
      <td style="padding: 12px; border: 1px solid var(--background-modifier-border); vertical-align: middle;">GCP</td>
      <td style="padding: 12px; border: 1px solid var(--background-modifier-border); vertical-align: middle;">Provided by GCP Security Command Center</td>
    </tr>

    <!-- IaaS Section -->
    <tr>
      <td rowspan="3" style="font-weight: bold; padding: 12px; border: 1px solid var(--background-modifier-border); vertical-align: middle;">IaaS</td>
      <td style="padding: 12px; border: 1px solid var(--background-modifier-border); vertical-align: middle;">Azure</td>
      <td style="padding: 12px; border: 1px solid var(--background-modifier-border); vertical-align: middle;">Supported Platforms</td>
      <td rowspan="3" style="padding: 12px; border: 1px solid var(--background-modifier-border); vertical-align: middle;">Secure Score</td>
      <td rowspan="3" style="padding: 12px; border: 1px solid var(--background-modifier-border); vertical-align: middle;">Microsoft Defender for Cloud Dashboard</td>
    </tr>
    <tr>
      <td style="padding: 12px; border: 1px solid var(--background-modifier-border); vertical-align: middle;">GCP, AWS</td>
      <td style="padding: 12px; border: 1px solid var(--background-modifier-border); vertical-align: middle;">Supported Platforms</td>
    </tr>
    <tr>
      <td style="padding: 12px; border: 1px solid var(--background-modifier-border); vertical-align: middle;">On-premises</td>
      <td style="padding: 12px; border: 1px solid var(--background-modifier-border); vertical-align: middle;">Supported Platforms</td>
    </tr>
  </tbody>
</table>
### MS Cloud Security Benchmark (MCSB)
- It is automatically assigned to your subscriptions and accounts when you onboard Defender for Cloud.
- This builds on the cloud security principles defined by the Azure Security Benchmark and applies these principles with detailed technical implementation guidance for Azure, for other cloud providers, and for other MS Clouds.
- The MCSB is the default policy initiative for Defender for Cloud and is the foundation of our security recommendations.
### MS Defender for Servers
- Plan 1: Deploys MS Defender for Endpoint to your servers and provides these capabilities:
	- MS Defender for Endpoint licenses are charged per hour instead of per seat, lowering costs for protecting VMs only when they are in use.
	- Defender for Endpoint deploys automatically to all cloud workloads so that you know they're protected when they spin up.
	- Alerts and vulnerability data from Defender for Endpoints is shown in Defender for Cloud.
- Plan 2: (Formerly Defender for Servers) Includes the benefits of Plan 1 and support for all of the other Defender for Servers features.
### MS Defender for App Service
- MS Defender for App Service uses the scale of the cloud to identify attacks targeting apps running over App Service.
- Attackers probe web apps to find and exploit weaknesses. Before being routed to specific environments, requests to apps running in Azure go through several gateways, where they're inspected and logged.
- This data is then used to identify exploits and attackers and learn new patterns that will be used later.
### MS Defender for Databases
- Threat protection for Azure Cosmos DB.
- Threat protection for open-source relational dbs are available:
	- Azure DB For PostgreSQL
	- Azure DB for MySQL
	- Azure DB for MariaDB
### MS Defender for Storage
![[Pasted image 20260731150002.png|802]]
### MS Defender for Containers
- Defender for Containers protects your clusters whether they're running in:
	- Azure Kubernetes Service (AKS).
	- Amazon Elastic Kubernetes Service (EKS) in a connected AWS account.
	- An unmanaged Kubernetes distribution, using Azure Arc enabled Kubernetes.
### MS Defender for Key Vault
- Defender detects unusual and potentially harmful attempts to access of exploit Key Vault accounts.
- This layer of protection allows you to:
	- Address threats without being a security expert.
	- Address threats without the need to manage third-party security monitoring systems.
- When anomalous activities occur, Defender shows alerts and optionally sends them via email to relevant members of your org. These alerts include the details of the suspicious activity and recommendations on how to investigate and remediate threats.
### MS Defender for Resource Manager
- It protects against following issues:
	- Suspicious resource management operations, such as operations from malicious IP addresses, disabling antimalware, and suspicious scripts running in VM extensions.
- Use of exploitation toolkits like Microburst or PowerZure.
- Lateral movement from the Azure management layer to the Azure resources data plane.
### MS Defender for APIs
- Defender for APIs helps you gain visibility into business critical APIs. You can investigate and improve security posture, prioritize vulnerability fixes, and detect against the top OWASP API threats:
	- Unified inventory of all APIs published within Azure API Management.
	- Monitor API traffic against top OWASP API threats through ML based and threat intelligence based detections.
	- Security insights including identifying unauthenticated, inactive/dormant, and externally exposed APIs.
	- Classifies APIs that receive or respond with sensitive data.

##  MS Defender for Endpoint (MDE)
- MDE settings govern the sensor behavior on individual machines across the enterprise.
- Advanced features must be configured to enable modern response capabilities, such as remote forensic collections and automated threat remediation.
- It is a platform designed to help enterprise networks prevent, detect, investigate, and respond to advanced threats on their endpoints.
![[Pasted image 20260729215408.png|866]]
- Defender for Endpoint detection and response capabilities provide advanced attack detections that are near real-time and actionable.
- When a threat is detected, alerts are created in the system for an analyst to investigate. Alerts with the same attack techniques or attributed to the same attacker are aggregated into an entity called an incident.
- Inspired by the "assume breach" mindset, Defender for Endpoint continuously collects behavioral cyber telemetry. This includes process information, network activities, deep optics into the kernel and memory manager, user sign activities, registry and file system changes, etc.
- Enabling advanced MDE features:
	1. Navigate to ***MS Defender Portal***.
	2. Select ***Settings>Endpoints***.
	3. Under ***General***, select ***Advanced features***.
	4. Toggle the necessary capabilities to ***On***:
		- **Live Response:** Enables remote shell connectivity to managed devices for forensic investigation.
		- **Live Response Unsigned Script Execution:** Allows custom PowerShell scripts from the tenant library to execute on endpoints. This requires strict RBAC governance, as allowing scripts increases the potential attack surface if the tenant is compromised.
		- **Enable EDR in Block Mode:** Instructs the Endpoint Detection and Response (EDR) sensor to proactively block malicious artifacts post-breach, even if a third-party antivirus is operating as the primary engine.
### Deploy the MS Defender for Endpoint Environment
- Data storage location: Determined by the geo-location of the tenant during provisioning. You can't change the location after this setup.
- Data retention: Data from Defender for Endpoint is retained for 180 days. However, in an advanced hunting investigation it's accessible via a query for a period of 30 days.
- For enabling preview features, the default is on and can be changed later.
### Onboard Devices
- You'll need to go to the onboarding section of Defender for Endpoint portal to onboard any of the supported devices.
- Depending on the device, you'll be guided with appropriate steps and provided management and deployment tool options suitable for the device.
### RBAC
- Custom roles are setup in the Defender XDR settings. Permission options:
	- Manage security operations:
		- View data
		- Active remediation actions
		- Live response capabilities
		- Alert's investigation
	- Manage portal system
	- Manage posture settings
### Device Groups
- Create device groups and use them to:
	- Limit access to related alerts and data to specific Entra ID user groups with assigned RBAC roles.
	- Configure different auto-remediation settings for different sets of devices.
	- Assign specific remediation levels to apply during automated investigations.
	- In an investigation, filter the device list to just specific device groups by using the group filter.
- In the process of creating a device group:
	- Set the automated remediation level for that group.
	- Specify the matching rule that determines which device group belongs to the group based on the device name, domain, tags, and OS platform.
	- Select the Entra ID user group that should have access to the device group.
	- Rank the device group relative to other groups after it is created.
### Attack Surface Reduction (ASR)
- ASR Rules are hardware and software level policy restrictions that block behaviors commonly abused by malware. These rules operate by intercepting OS calls such as Office macros attempting to create child processes, or executable content launching directly from external USB drives.
- Attack surface reduction rules.
- Hardware-based isolation.
- Application control.
- Exploit protection.
- Network protection.
- Windows defender firewall.
- Web protection.
- Controlled folder access.
- Removable storage protection.

| ASR Rule                                                | Operational Description                                                                                                            |
| ------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| Block credential stealing from LSASS.                   | Prevents tools like [[Mimikatz]] from interacting with the Local Security Authority Subsystem Service (LSASS) to dump credentials. |
| Block Office apps from creating child processes.        | Halts malicious macro execution chains that attempt to spawn PowerShell or Command prompt.                                         |
| Block executable content from email client and webmail. | Prevents the direct execution of malicious payloads originating from MS Outlook or web clients.                                    |
| Block untrusted and unsigned processes from USB.        | Mitigates lateral movement via physical media by restricting unsigned code execution.                                              |

#### Sample ASR Rules
- Block executable content from email client and webmail.
- Block all Office applications from creating child processes.
- Block Office applications from creating executable content.
- Block Office applications from injecting code into other processes.
- Block execution of potentially obfuscated scripts.
- Use advanced protection against ransomware.
#### Rule Modes
- Off
- Not configured or Disable: 0
- Block (enable ASR rule): 1
- Audit: 2
- Warn: 6
Admins can deploy ASR rules via MS Intune or configure them directly via Powershell. When implementing new ASR rules, it is critical to deploy them initially in Audit mode to monitor for app compatibility issues and prevent business disruptions.
#### Deployment Options
- MS Configuration Manager
- Group Policy
- PowerShell
- MS Intune
- Mobile Device Management (MDM)
```powershell
# Enable the ASR Rule to block Office child processes in Audit Mode to prevent business disruption
Set-MpPreference -AttackSurfaceReductionRules_Ids D4F940AB-401B-4EFC-AADC-AD5F3C50688A -AttackSurfaceReductionRules_Actions AuditMode # D4F940AB-401B-4EFC-AADC-AD5F3C50688A is the guid of the ASR rule

# To apply exclusions for a legacy line-of-business application that legitimately triggers the ASR rule
Add-MpPreference -AttackSurfaceReductionOnlyExclusions "C:\LegacyApp\finance_macros.xlsm"

# Review the configured ASR rules and their current states on a local machine
Get-MpPreference | Select-Object AttackSurfaceReductionRules_Ids, AttackSurfaceReductionRules_Actions
```
### Investigation
#### Automated Investigation & Response (AIR)
- MS Defender utilizes device groups to apply specific automation levels to clusters of endpoints.
- By segregating machines logically, orgs can dictate whether threats are remediated automatically or require manual SOC approval.
- Full: remediate threats automatically.
- Semi: require approval for any remediation.
- Semi: require approval for core folders remediation.
- Semi: require approval for non-temp folders remediation.
- No automated response.
- The recommended setting for standard workstations is "Full-remediate threats automatically", whereas highly sensitive db servers might be configured for "Semi-require approval for any remediation".
#### Automatic Attack Disruption
- It is a native XDR capability that limits lateral movement by containing compromised assets at machine speed, drastically reducing the blast radius of sophisticated threats.
- When the Defender correlation engine reaches a 99% confidence threshold indicating an active threat, such as an ongoing ransomware deployment or an Adversary-in-the-Middle (AitM) phishing campaign, it executes automated actions across connected MS services.

| Automated Response Action | Product Execution Action         | Tactical Description                                                                                                                                          |
| ------------------------- | -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Disable User              | Defender for Identity / Entra ID | Disables the AD or Entra ID account instantly to prevent further sign-in, resource access, and lateral movement.                                              |
| Contain Device            | Defender for Endpoint            | Isolates the machine at the network level. Blocks all ingress and egress traffic except for persistent telemetry communication to the Defender cloud service. |
| Revoke User Session       | MS Entra ID                      | Terminates all active OAuth sessions and tokens, effectively severing the attacker's persistent cloud access.                                                 |
| Safeboot Hardening        | Defender for Endpoint            | Applies predictive shielding to block potential tampering attempts that leverage Safe Mode reboots.                                                           |

- While automated containment is highly effective, orgs must occasionally exempt critical infra from these actions to avoid catastrophic operational downtime. Exclusions should be used sparingly, such as for core domain DCs or emergency break-glass admin accounts.
- Configuring Attack Disruption Exclusions:
	1. Go to the ***MS Defender Portal***.
	2. Navigate to ***Settings>MS Defender XDR***.
	3. Under the ***Automated Response*** heading, select ***Identities*** or ***Devices***.
	4. To exclude and entity, select ***Add User Exclusion***. Provide the User Principal Name (UPN).
	5. To exclude a device, specify a predefined Device Tag representing the critical asset group, and save the config.
#### Investigate a File
- Investigate the details of a file associated with a specific alert, behavior, or event to help determine if the file exhibits malicious activities, identify the attack motivation, and understand the potential scope of the breach.
#### Investigate a User Account
- Identify user accounts with the most active alerts (Users at Risk) and investigate cases of potentially compromised credentials, or pivot on the associated user account when investigating an alert or device to identify possible lateral movement between devices with that user account.
#### Investigate an IP Address
- Where the IP is worldwide.
- Lookup reverse DNS names.
- Review alerts related to this IP
- Check if IP appears in the organization.
- Understand prevalence.
#### Investigate Domains & URLs
- You can see info from the following sections in the URL and Domain view:
	- Domain details, registrant contact info.
	- MS verdict.
	- Incidents related to this URL or domain.
	- Prevalence of the URL or domain in the org.
	- Most recent observed devices with URL or domain.
### Automation
- The advanced features area provides many on/off switches for features within the product.
- **File Content Analysis:** Enable file content analysis capability so that certain files and email attachments can automatically be uploaded to the cloud for additional inspection in automated investigation.
- **Memory Content Analysis:** Enable memory content analysis capability if you would like MS Defender for Endpoint to automatically investigate memory content of process. When enabled, memory content might be uploaded to Defender for Endpoint during an automated investigation.
- **Automation Folder Exclusions:** Automation folder exclusions allow you to specify folders that the automated investigation will skip. You can control the following attributes about the folder that you'd like to be skipped: folders, extensions of files, file names.
### MS Endpoints Manager
- Turn on the MS Intune connection from MS Defender XDR Portal.
- Turn on the Defender for Endpoint integration in Intune Admin Center.
- Create the compliance policy in Intune Admin Center.
- Assign the policy.
- Create an Entra ID Conditional Access Policy.
