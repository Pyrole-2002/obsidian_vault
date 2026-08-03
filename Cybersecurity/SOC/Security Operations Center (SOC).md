A Security Operations Center is a centralized unit within an org that deals with security issues, incidents, and events.
- **Monitor:**
	- Continuous observation for unusual activity.
	- Utilization of monitoring tools for real-time visibility.
	- Early detections of potential security threats.
- **Detect:**
	- Confirm security events identified during monitoring.
	- Utilize threat detection techniques.
	- Identify known Indicators of Compromises (IOCs).
- **Analyse:**
	- Perform in-depth investigations to understand security incidents.
	- Examine affected systems, build a timeline of events.
	- Trace Tactics, Techniques, and Procedures (TTPs).
- **Respond:**
	- Formulate a response plan based on findings.
	- Contain threats, mitigate impact, and restore normal ops.
	- Collaborate with internal teams and stakeholders.
##### CIA Triad
The SOC aims to maintain the ***CIA Triad: Confidentiality, Integrity, Availability*** of all assets within the org.
- Confidentiality:
	- Protection of sensitive info from unauthorized access.
	- Ensures that data is only accessible to those with proper authorization.
- Integrity:
	- Ensures that data remains accurate, reliable, and consistent.
- Availability:
	- Ensures that data and resources are accessible and usable when needed.
##### AAA Framework
- Authentication:
	- Verifies the identity of users attempting to access a system or resource.
	- Password, key, or other authentication methods.
- Authorization:
	- What actions or ops are we allowed to perform.
	- Access levels based on roles, perms, or privileges.
- Accounting:
	- Tracking and recording activities within a system.
	- Login attempts, resource access, audit logs.
##### Vulnerability
- Weaknesses in systems, networks, or processes.
- Can be exploited by threats to compromise CIA.
- Exposes the org to threats.
##### Threat
- Any potential danger to info or systems.
- Malware, phishing, DoS.
- Takes advantage of a vulnerability.
##### Risk
- Likelihood of a threat exploiting a vulnerability.
- Potential for loss and damage when a threat occurs.
##### Logs
- A record of events or actions that have occurred within a system.
- Used for monitoring and troubleshooting security incidents.
##### Security Event
- Any observable occurrence that has a potential significance for security.
- All incidents are events, but not all events are incidents.
##### Security Incident
- An occurrence that harms or jeopardizes info or systems.
- Constitutes a violation of law, security policies, or acceptable use.
##### SOC Metrics
- **Mean Time to Detect (MTTD)**
- **Mean Time to Resolution (MTTR)**
- **Mean Time to Attend and Analyze (MTTA&A)**
- **Incident Detection Rate**
- **False Positive Rates (FPR)**
- **False Negative Rates (FNR)**
- **Key Performance Indicators (KPIs)**
- **Key Risk Indicators (KRIs)**
- **Service Level Agreements (SLAs)**
#### SOC Tools
- **Security Information and Event Management (SIEM)**
	- Log management.
	- Real-time monitoring.
	- Alerting and notifs.
	- Incident response.
	- Dashboards, reports, and visualizations.
	- Threat intelligence integration.
- **Security Orchestration, Automation, and Response (SOAR)**
	- Orchestration, such as workflows and collaboration.
	- Automation, such as alert triage, artifact collection, and data enrichment.
	- Incident response, such as assess and prioritize.
	- Integration, such as TIPs, EDR, Firewalls.
	- Analytics and intelligence.
- **Incident Management Tools**
	- Incident ticketing, such as documentation, tracking, assigning.
	- Alert management.
	- Workflow automation.
	- Collaboration.
- **Network Security Monitoring (NSM)**
	- Packet capture and analysis. Real-time, or with captures.
	- Network traffic analysis, such as statistical analysis, machine learning, and behavior analysis.
	- Intrusion detection, such as IDS functionality.
	- Integration with SIEM. Correlate network events with endpoint telemetry.
- **Endpoint Detection and Response (EDR)**
	- Real-time endpoint monitoring.
	- User Entity Behavior Analytics (UEBA).
	- Threat detection and prevention.
	- Incident investigation.
	- Remediation and response.
	- Integration with SIEM.
- **Firewalls**
	- **Network Firewalls**
		- Layer 3; examine packets; make decisions based on rules.
	- **Next-Generation Firewalls (NGFW)**
		- Layer 7; stateful packet inspection; deep packet inspection.
	- **Web Application Firewall (WAF)**
		- Layer 7; Inspect HTTP(S) traffic; protect webapps from attacks.
- **Intrusion Detection and Prevention Systems (IDS / IPS)**
	- Passive or active monitoring.
	- Generate alerts.
	- Actively block and prevent threats.
	- Logging and reporting.
- **Threat Intelligence Platforms (TIP)**
	- Data aggregation and enrichment.
	- Indicators of Compromise (IOCs).
	- Normalization and Standardization.
	- Analysis and prioritization.
	- Integration with SIEM.
- **Forensic Analysis Tools**
	- Data acquisition and imaging.
	- File system analysis.
	- Memory forensics.
	- Registry forensics.
	- Network traffic forensics.
- **Malware Analysis Tools**
	- Dynamic analysis.
	- Static analysis.
	- Behavioral analysis.
	- Signature and pattern matching.
	- Integration with TIPs.
#### Security Controls
##### Defense in Depth
- Strategy of layered security.
- Multiple barriers to threats.
##### Admin Controls
- Security policies.
- Change management procedures.
- Incident response plans.
##### Technical Controls
- Firewalls, EDR.
- IDS.
- 2-Factor Authentication.
##### Physical Controls
- Access control systems.
- Surveillance cameras.
- Biometrics.
#### Security Control Functions
##### Preventative Control
- Eliminate or reduce likelihood of an attack succeeding.
- ACLs, Firewalls, EDR, IPS.
##### Detective Control
- Identify and record attempted or successful intrusions.
- ISD, SIEM, logs, surveillance cams.
##### Corrective Control
- Eliminate or reduce the impact of an intrusion.
- Backups, IR plan, patch management.
##### Deterrent Control
- Discourages intrusion attempts.
- Physical barriers, signage, tamper seals.
##### Compensating Control
- Acts as an alt means for a principal control.
- Network segmentation, data masking.
#### Risk Control Strategies
##### Risk Transference
- Shifting responsibility to a third-part.
- Cybersecurity insurance, cloud service providers.
##### Risk Acceptance
- Acknowledge and tolerate a risk.
- Exempt or except.
##### Risk Avoidance
- Proactively eliminate or avoid exposure to a risk.
- Limiting the type of data stored on a server.
##### Risk Mitigation
- Reduce the likelihood or impact of a risk.
- Implementing patch management.
#### Security Policies
##### Acceptable Use Policy (AUP)
- What is and isn't allowed within the org.
- Bring your Own Device (BYOD).
##### Password Policy
- Specific requirements for creating and managing passwords.
- Complexity, length, expiration, reuse.
##### Data Classification Policy
- Categorize data based on sensitivity and importance.
- How to handle data at each level.
##### Change Management Policy
- Planning and implementing changes to systems or processes.
##### Disaster Recovery Policy
- Recovering IT systems and data.
- Natural disasters, cyber attacks, etc.
#### SOC Models
##### Internal SOC
- Owned and operated by the org it serves.
- Monitors and defends internal networks, systems, and data.
- Requires significant upfront investment in training and resources.
##### Managed SOC (MSOC)
- Third-party provider of security ops.
- Basic monitoring or comprehensive threat detection and response.
- Subscription-based SLAs, more cost-effective.
##### Hybrid SOC
- Elements of internal and managed SOC services.
- Incident response, forensic analysis, malware analysis.
- Flexible compromise - call in the experts as needed.
#### Common Threats & Attacks
##### Social Engineering
- Exploiting humans.
- Spoofing.
- Phishing
	- Spear Phishing.
	- Whaling.
- Vishing (Voice Phishing).
- SMiShing (SMS Phishing).
- Quishing (QR Code Phishing).
##### Malware
- **Worm:**
	- Self replicating.
	- Infect and propagate.
	- Spreads across networks (often without requiring user interactions).
	- Stuxnet, Blaster.
- **Spyware / Adware**
	- Monitor user activity.
	- Display unwanted ads.
- **Trojan:**
	- Disguised malware.
	- Acting as legitimate software.
	- RAT - Remote Access Trojan.
	- Botnets.
	- Deliver ransomware.
- **Ransomware:**
	- Infection.
	- Ransom demand.
	- Payment.
	- Decryption (if lucky).
- **Botnet:**
	- Network of compromised *zombies*.
	- Controlled by a remote attacker.
	- Used to coordinate attacks: DDoS, spam campaigns, spread malware.
- **Fileless Malware:**
	- Memory-based malware.
	- No traces on disk.
	- Evade detection and logging.
	- Living Off the Land; PowerShell, WMI, code injection.
##### Identity and Account Compromise
- Usernames, passwords, SSN, PII.
- Impersonation, fraud, theft.
- Methods such as, phishing, brute force, credential stuffing, social engineering, malware.
##### Insider Threats
- Threats form the *inside* such as, current or former employees, contractors, and partners.
- Malicious, careless, or compromised insiders.
- Can lead to severe exposure and damage such as, data breaches, IP theft, reputational damage, legal issues.
##### Advanced Persistent Threats (APTs)
- Highly skilled, well funded adversaries.
- Sophisticated.
- Persistent; long-term, quiet, and undetected access.
- Targeted.
- Strategic objectives.
- https://www.crowdstrike.com/en-us/adversaries/
- https://cloud.google.com/security/resources/insights/apt-groups
- https://attack.mitre.org/
##### Denial-of-Service Attacks (DoS)
- Disrupt the availability of systems.
- Flood of traffic and requests; exhaust a system's resources and bandwidth.
- Intentional or accidental.
- Distributed Denial-of-Service (DDoS)
	- Utilize multiple compromised systems.
	- Amplification.
	- Hard to defend and block.
##### Data Breaches
- Data exposure, theft, or compromise.
- PII, credentials, financial records, IP.
- Malicious actions and human error
	- Misconfiguration.
	- Inadequate security controls.
- Reputational damage, regulatory trouble.
##### Zero-Days
- Vulnerabilities previously unknown to a vendor.
- No patches, no mitigations:
	- Zero days to patch.
	- Zero days to protect.
- Risk mitigation, risk avoidance.
##### Supply Chain Attacks
- Exploits up the chain; compromises security downstream.
- Suppliers, vendors, partners.
- Malware propagates down; hard to detect; malware from *trusted* entities.
#### Phishing Analysis
- Phishing is the act of attempting to obtain sensitive info from individuals by using social engineering tactics over various communication platform such as email, SMS, phone calls, and the internet.
- Impersonation: Posing as legitimate orgs or individuals.
- Stealing sensitive info: Passwords, cc nums, sensitive files.
- Deliver and install malware: Via attachments, embedded files, or URLs.
- Exploiting humans: Preys on emotions, human psychology.
##### Email Fundamentals
- Email headers are lines of metadata attached to an email and contain many useful strings of info for analysts and investigators.
- Email body is the main content of an email message that is visible to the recipient. It contains the message that the sender wants to convey, including text, images, links and any attachments.
- Email Protocols:
	- [[SMTP]]:
		- Simple Mail Transfer Protocol.
		- Used to send outgoing mail.
		- Port 25 (or 465, 587).
	- [[POP3]]:
		- Post Office Protocol (version 3).
		- Downloads emails, then deletes them.
		- Port 110 (or 995 for POP3S).
	- [[IMAP]]:
		- Internet Message Access Protocol.
		- Advanced email sync.
		- Port 143 (or 993 for IMAPS).
- **Mail Agents:**
	- Mail Transfer Agent (MTA): Route and transfer email msgs across mail servers. Determine appropriate route and relays.
	- Mail User Agent (MUA): Compose, send, receive and manage email msgs. Gmail, Outlook, Yahoo, Thunderbird.
	- Mail Delivery Agent (MDA): Accepting incoming email msgs from MTAs, places the email in the recipient's inbox.
	- Mail Submission Agent (MSA).
	- Mail Retrieval Agent (MRA).
