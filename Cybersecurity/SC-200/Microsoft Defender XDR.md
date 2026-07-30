### Rear Wheel Drive Engine Idle Clutch Accelerate
![[Pasted image 20260727023449.png|576]]
![[Pasted image 20260727171504.png|856]]
# Intro to Threat Protection with Microsoft Defender XDR (Extended Detection & Response)
![[Pasted image 20260727192525.png|1221]]
## Threat Investigation with MS Graph Security API
- The MS Graph Security API is an intermediary service (broker) that provides programmatic connections to multiple MS Graph Security Providers.
- It is a RESTful web API. After you register your app and get authentication tokens for a user or service, you can make requests to the MS Graph API.
- You can use the Graph Explorer to call the Security API but you must have the required permissions and be authenticated.
## Incidents
- An incident in MS Defender XDR is a collection of correlated alerts and associated data that make up the story of an attack.
## Advanced Hunting
- This is a query based threat-hunting tool that lets you explore up to 30 days of raw data.
- You can proactively inspect events in your network to locate threat indicators and entities.
- The flexible access to data enables unconstrained hunting for both known and potential threats.
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
##  MS Defender for Endpoint
- It is a platform designed to help enterprise networks prevent, detect, investigate, and respond to advanced threats on their endpoints.
![[Pasted image 20260729215408.png|866]]
- Defender for Endpoint detection and response capabilities provide advanced attack detections that are near real-time and actionable.
- When a threat is detected, alerts are created in the system for an analyst to investigate. Alerts with the same attack techniques or attributed to the same attacker are aggregated into an entity called an incident.
- Inspired by the "assume breach" mindset, Defender for Endpoint continuously collects behavioral cyber telemetry. This includes process information, network activities, deep optics into the kernel and memory manager, user sign activities, registry and file system changes, etc.
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
- Attack surface reduction rules.
- Hardware-based isolation.
- Application control.
- Exploit protection.
- Network protection.
- Windows defender firewall.
- Web protection.
- Controlled folder access.
- Removable storage protection.
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
#### Deployment Options
- MS Configuration Manager
- Group Policy
- PowerShell
- MS Intune
- Mobile Device Management (MDM)
### Investigation
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
#### Configure Automated Investigation & Remediation Capabilities
- Full: remediate threats automatically.
- Semi: require approval for any remediation.
- Semi: require approval for core folders remediation.
- Semi: require approval for non-temp folders remediation.
- No automated response.
### MS Endpoints Manager
- Turn on the MS Intune connection from MS Defender XDR Portal.
- Turn on the Defender for Endpoint integration in Intune Admin Center.
- Create the compliance policy in Intune Admin Center.
- Assign the policy.
- Create an Entra ID Conditional Access Policy.
