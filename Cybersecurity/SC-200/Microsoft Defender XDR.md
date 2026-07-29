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