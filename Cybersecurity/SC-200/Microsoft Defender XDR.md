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
- Incident response leverages specialized Defender products:
	- **MS Defender for Identity (MDI):** Protects on-premise AD infra. It utilizes sensors installed directly on DCs to detect lateral movement techniques such as Pass-the-Ticket, Golden Ticket, and remote SAMR enumeration. If an identity is compromised on-premises, analysts can utilize Defender XDR to instantly "Disable User", isolating the threat at the directory level.
	- **MS Defender for Cloud Apps (MDA):** Governs Software-as-a-Service (SaaS) environments and Shadow IT. In the event of a malicious OAuth app compromise or bulk data exfiltration, MDA can automatically revoke OAuth consent and invalidate user sessions in Entra ID to sever the attacker's connection.
	- [[Microsoft Purview|MS Purview:]] Utilized heavily for investigating insider risks and data loss compliance violations. If Purview Data Loss Prevention (DLP) flags an incident, analysts navigate to the incident details to determine the specific sensitivity labels and information types exposed during the event.
	- **MS Entra ID Protection:** Evaluates sign-in and user risk based on behavioral telemetry, identifying scenarios such as impossible travel or leaked credentials. It dynamically enforces adaptive access policies to remediate compromised identities autonomously.
	- **Defender for Cloud:** Provides cloud workload protection from Azure, AWS, and GCP resources. Analysts must investigate alerts originating from Kubernetes control planes, SQL dbs, and VM instances, often utilizing [[Microsoft Sentinel]] to cross correlate these alerts with network telemetry.
## Threat Investigation with MS Graph Security API
- The MS Graph Security API is an intermediary service (broker) that provides programmatic connections to multiple MS Graph Security Providers.
- It is a RESTful web API. After you register your app and get authentication tokens for a user or service, you can make requests to the MS Graph API.
- You can use the Graph Explorer to call the Security API but you must have the required permissions and be authenticated.
## Incidents
- Defender XDR groups related alerts, compromised assets, and automated investigations into a single pane of glass called an Incident.
- The Incident Graph visually maps the blast radius, illustrating the relationships between a malicious email delivery, the identity that clicked the payload, and the endpoint where execution occurred.
- The ***Evidence and Response*** tab in Defender XDR offers a centralized view of all entities associated with a security incident, including users, devices, emails, files, and URLs. It highlights the investigation outcome for each item and displays any automated actions, such as quarantining emails or isolating devices.
## Advanced Hunting
- It allows security analysts to perform cross-domain KQL queries directly against the unified XDR telemetry. From these queries, custom detection rules can be authored. To properly generate an incident, the custom detection query must project specific entity identifiers such as `Timestamp`, `DeviceId`, or `AccountObjectId`.
- This is a query based threat-hunting tool that lets you explore up to 30 days of raw data.
- You can proactively inspect events in your network to locate threat indicators and entities.
- The flexible access to data enables unconstrained hunting for both known and potential threats.
- `DeviceEvents`, `DeviceProcessEvents`, `DeviceNetworkEvents`: Contains raw endpoint telemetry from Defender for Endpoint.
- `EmailEvents`, `EmailAttachmentInfo`, `EmailPostDeliveryEvents`: Contains routing and attachment data from Defender for Office 365.
- `IdentityLogonEvents`, `IdentityQueryEvents`: Contains authentication and directory querying data from Defender for Identity and Entra ID.
- `CloudAppEvents`: Contains SaaS application telemetry from Defender for Cloud Apps.
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
## Threat Analytics & Hunting Graphs
- The Threat Analytics portal in Defender XDR provides detailed reports authored by MS Security researchers covering emerging threat campaigns, Advanced Persistent Threat (ATP) groups, and novel vulnerabilities.
- These reports include pre-built advanced hunting queries that analysts can instantly run to check their environment for exposure.
- Hunting graphs allow analysts to visually map the blast radius of an attack. By joining identity tables with device tables via [[Microsoft Sentinel#Kusto Query Language (KQL)|KQL]], the analyst can trace exactly which servers a compromised admin account authenticated to during a specified timeframe, revealing the full scope of lateral movement.
```sql
--// MITRE ATT&CK: T1078 - Valid Accounts
--// Purpose: Trace lateral movement blast radius by linking compromised identities to device logons
let compromisedIdentity = "admin_jdoe";
let timeFrame = ago(7d);
IdentityLogonEvents
| where Timestamp >= timeFrame
| where AccountName == compromisedIdentity
| where ActionType == "LogonSuccess"
--// Join with DeviceNetworkEvents to see what outbound connections those machines made shortly after logon
| join kind=inner (
    DeviceNetworkEvents
    | where Timestamp >= timeFrame
    | where RemoteIPType == "Public"
) on DeviceId
| project Timestamp, AccountName, DeviceName, RemoteIP, RemoteUrl, ActionType
| sort by Timestamp desc
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
- Defender for cloud apps is built to:
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
- AMA runs inside the VM guest OS.
- DCR is an ARM object that defines:
	- Data Sources: Windows Event Logs, Syslog facilities, performance counters.
	- Destinations: Specific Log Analytics workspaces.
  Without a DCR link, the agent remains completely dormant.
### Suppression Rules
- They allow orgs to automatically dismiss recurring or false positive security alerts, streamlining alert management and reducing noise. These rules can be configured for specific resources, alert types, or conditions, and can be applied at the subscription or management group level.
- Users with appropriate roles, such as Security Admin or Owner, can create and manage these rules through the Azure portal or REST API.
- To suppress alerts in MS Defender for Cloud:
	1. Navigate to the ***Security Alerts*** page in MS Defender for Cloud.
	2. Select the alert you want to suppress, click the three dots at the end of the row and choose ***Create Suppression Rule***.
	3. In the ***New Suppression Rules*** page, select the alert you wish to suppress.
	4. Choose the entities for which you want to suppress the alert, such as specific IP ranges, processes, resources, or user accounts.
	5. Enter the rule details, including the rule name, reason for suppression, comments, and an expiration date (up to 6 months in the future).
	6. Click ***Simulate*** to test the rule before applying it and ensure its correctness.
	7. Click ***Apply*** to finalize the suppression rule.
	8. To manage existing suppression rules, click the ***Suppression Rules*** button at the top of the ***Security Alerts*** page.
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
- MS Defender for Cloud provides Cloud Workload Protection (CWPP) through specialized plans:
	1. Plan 1: Deploys MS Defender for Endpoint to your servers and provides these capabilities:
		- MS Defender for Endpoint licenses are charged per hour instead of per seat, lowering costs for protecting VMs only when they are in use.
		- Defender for Endpoint deploys automatically to all cloud workloads so that you know they're protected when they spin up.
		- Alerts and vulnerability data from Defender for Endpoints is shown in Defender for Cloud.
	2. Plan 2: (Formerly Defender for Servers) Includes the benefits of Plan 1 and support for all of the other Defender for Servers features.
- The Defender for Servers plan extends advanced threat detection and behavioral analytics to VMs.
- Defender for Servers manages automatic provisioning (deploying the required monitoring agents/extensions) and provisions the necessary configs (like DCRs) to automatically pipe Windows Security events and Linux Syslogs  directly into workspace.
- Under ***Defender for Cloud > Environment settings > Defender plans***, enabling Servers (Plan 1 or Plan 2) activates server-side threat detection. It provides agentless machine scanning.
- Under ***Settings & Monitoring***, Defender for Cloud uses built-in Azure Policies behind the scenes to:
	1. Auto-provision the AMA / Defender for Endpoint extensions on all discovered VMs.
	2. Create and associate the default DCRs.
	3. Route security events to the designated workspace linked at the subscription level.
- Defender for Servers protects non-Azure machines by projecting them into Azure Resource Manager (ARM) via Azure Arc:
	- The machine gets an ARM Resource ID, appearing inside Azure just like a native Azure VM.
	- Once registered as an Arc-enabled server, Azure VM extensions (agents, configurations, scripts) can be managed, updated, and monitored centrally through ARM APIs and Azure Policy.
#### Component Reference Table

| **Component**                        | **Primary Function**                                             | **Handles Guest OS Security Logs?**                 |
| ------------------------------------ | ---------------------------------------------------------------- | --------------------------------------------------- |
| **Defender for Servers**             | CWPP threat detection, vulnerability management, auto-onboarding | **Yes** (manages agent deployment and DCR routing)  |
| **Azure Monitor Agent (Standalone)** | Telemetry forwarder inside VM guest OS                           | **Only when linked to a configured DCR**            |
| **VM Diagnostic Settings**           | Streams platform/host-level metrics and boot diagnostics         | **No** (host hypervisor only, not OS security logs) |
| **Workflow Automation**              | Triggers Logic Apps based on alerts/recommendations              | **No** (alert orchestration, not data ingestion)    |
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
- Defender for Resource Manager is a security capability that continuously monitors Azure Resource Manager (ARM) activities triggered via the Azure portal, REST APIs, CLI, or SDKs to detect malicious or unauthorized operations. This includes suspicious management actions like unusual IP access, disabling antimalware, or use of known cloud exploitation toolkits.
- It uses advanced analytics to flag potentially harmful activities before they impact workloads.
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
### MS Defender for DevOps
- In terms of DevOps security, Defender for Cloud offers a central console that assists security teams in protecting apps and resources throughout the dev process and into the cloud, covering multi-pipeline environments such as Azure DevOps, GitHub and GitLab.
- Capabilities of DevOps security include:
	- Unified visibility into DevOps security posture: Security admins have full visibility into DevOps inventory and the security posture of preproduction app code across multi-pipeline and multi-cloud environments. They can see findings from code, secrets, and open-source dependency vulnerability scans. They can also assess the security configs of their DevOps environment.
	- Strengthen cloud resource configs throughout the dev lifecycle: You can secure IaC templates and container images to minimize cloud misconfigs reaching prod environments.
	- Prioritize remediation of critical issues in code: Apply comprehensive code-to-cloud contextual insights within Defender for Cloud. Security admins help devs prioritize critical code fixes with pull request annotations and assign developer ownership by triggering custom workflows that feed directly into the tools devs use.
- You can link your GitHub orgs on the Environment settings page within Defender for Cloud. By connecting your GitHub envs to Defender for Cloud you enhance the security features for your GitHub resources and improve your overall security posture.
##  MS Defender for Endpoint (MDE)
- MDE affords analysts the capability to perform surgical containment and forensic evidence gathering on remote machines without alerting the adversary.
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
- The Automated Investigation and Response (AIR) feature automates responses to common incidents, such as investigating malware alerts, isolating devices, and blocking malicious files, reducing the need for manual intervention and allowing teams to focus on more complex tasks.
- Isolating the device from the network is crucial for preventing the spread of malware once it is detected. Although AIR automates the process of detecting and responding to threats, isolating a device is a manual action to ensure that the malware cannot propagate to other systems within the network. This manual intervention is necessary to ensure that the device is fully secured and contained before further remediation steps are taken.
- Hard deleting email messages involves permanently removing the email from the system. Defender for Office 365 handles this automatically when malicious emails are detected.
- Defender for Office 365 can quarantine emails automatically as part of its automated security measures.
- Defender for Endpoint manages scans automatically to detect and remove threats. A full system scan may be triggered manually in some situations, but most scans are handled by the system without requiring manual intervention unless specific actions are needed for a more in-depth review.
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
- The Portable Executable (PE) format is the file format used by Windows OS for executables, object code, and DLLs. It encapsulated the headers, code, data, and metadata needed by the Windows OS loader to run code or load drivers into memory.
- Common Windows PE extensions:
	- `.exe`
	- `.dll`
	- `.sys`
	- `.scr`
	- `.ocx` / `.cpl`
#### MDE Custom File Indicators (IoCs)
- File indicators enable SecOps teams to allow, detect/audit, warn, or block specific files across managed devices.
- Supported Actions: Allow, Audit, Warn, Block Execution, Block & Remediate.
- Enforcement Mechanism: When a files is accessed or executed, the Defender Antivirus client computes its hash and checks against enterprise custom indicators.
- Prerequisites for Hash Blocking:
	- MS Defender Antivirus in Active mode.
	- Cloud-delivered protection enabled.
	- Behavior monitoring enabled.
	- File hash computation turn on: `Set-MpPreference -EnableFileHashComputation $true`
	- The feature switch "Allow or block file" enabled under ***Settings > Endpoints > General > Advanced features***.
- Platform support matrix for file indicators:
	- Windows: PE files only.
	- macOS: Mach-O executables, POSIX shell scripts (`.sh`, `.bash`), AppleScript (`.scpt`).
	- Linux: ELF binaries and supported executable formats.
#### Non-PE Files
- Malicious non-PE files (`.docx`, `.xlsx`): Because hash indicators cannot block non-PE files, Microsoft relies on multi-layered defenses:
	- MS Defender for Office 365: Safe Attachments and Safe Links inspect docs in dynamic sandbox environments before delivery.
	- ASR Rules: Rules such as "Block Office applications from creating child processes" or "Block Office applications from injecting code into other processes".
	- Real-time Antivirus & Cloud ML: Defender Antivirus analyzes doc macros, embedded OLE objects, and CVE exploits using heuristics and cloud-based definitions, regardless of custom hash rules.
- Precedence of Indicators:
	- Windows Defender Application Control (WDAC) / AppLocker
	- Defender Antivirus Exclusions
	- Custom Block/Warn Indicators
	- SmartScreen Blocks
	- Custom Allow Indicators
- Hash Precedence: If conflicting indicators exist for the same file, Defender prioritizes the strongest hash algorithm: SHA-256 > SHA-1 > MD5.
### Investigation
#### Live Response & Investigation Packages
- Live Response provides a powerful remote cli directly to an endpoint. To utilize this capability, the machine must be running a supported OS and Live Response must be explicitly enabled in the MDE Advanced Features settings.
- Initiating a Live Response Session:
	- Navigate to the ***MS Defender Portal***.
	- Select ***Assets>Devices*** and search for the target compromised endpoint.
	- In the device menu, select ***Initiate Live Response Session***.
	- A console window will open, establishing a secure connection to the machine.

| Command     | Operational Description                                                                                                      | Execution Constraints & Limits                                                                              |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| `getfile`   | Retrieves a file from the remote device down to the analyst's local machine for offline malware reverse-engineering.         | The maximum file size limit for extraction is **3 GB**. Virtual files or reparse points are not supported.  |
| `putfile`   | Drops a file (e.g., a custom forensic scanning executable) from the central tenant library to the target endpoint.           | Files are saved to a temporary working directory and are securely deleted automatically upon system reboot. |
| `library`   | Lists the custom scripts and binaries currently available in the tenant's Live Response repository.                          | The total cumulative library size limit is **250 MB**.                                                      |
| `run`       | Executes a PowerShell script uploaded to the library on the target device. Parameters can be passed inline.                  | The session forcefully times out after **10 minutes** of script execution.                                  |
| `remediate` | Attempts to forcefully stop a malicious process, delete a file, remove a scheduled task, or wipe a registry persistence key. | Remediation actions vary dynamically based on the target entity type                                        |

- For intensive, long running commands (such as a full memory dump), analysts can append an `&` to send the command to the background, allowing the analyst to continue executing other commands concurrently.
- Using the `fg` command will bring the background process back to the foreground upon completion.
- For extensive forensic triage, analysts can trigger the collection of an Investigation Package. This automated action pulls a predefined set of artifacts, including process lists, active network connections, autorun configs, and system event logs, compiling them into a downloadable ZIP archive directly form the device page. Linux troubleshooting relies on downloading and executing the Python or binary-based Client Analyzer (`mde_support_tool.sh` or `MDESupportTool`) to generate diagnostic logs.
- Prefetch files is an integral part of the data collected within the investigation package and play a key role in understanding file execution on Windows systems.
  These files store records of apps that have been executed, including timestamps for the first and last time they were run. By analyzing prefetch files, you can trace the history of app executions on a device, which helps identify when specific files were launched.
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
	4. To exclude an entity, select ***Add User Exclusion***. Provide the User Principal Name (UPN).
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
---