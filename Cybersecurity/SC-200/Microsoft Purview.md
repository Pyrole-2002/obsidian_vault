# MS Purview Compliance Solutions
Security Operations Analysts can assist compliance and eDiscovery admins with:
- eDiscovery: Content Search
- Auditing Solutions: Audit Standard & Premium
- Information Protection: Data Loss Prevention (DLP)
- Insider Risk: Insider Risk Management
## MS Purview Data Loss Prevention (DLP) Policies
- With a DLP policy, you can:
	- Identify sensitive information.
	- Prevent the accidental sharing of sensitive information.
	- Monitor and protect sensitive information in the desktop versions of Office 365.
	- Help users learn how to stay compliant without interrupting their workflow.
	- View DLP alerts and reports showing content that matches your organization's DLP policies.
- DLP components:
	- Sensitive Information Types (SITs)
	- Sensitive Labels
	- DLP Policies
	- Defender for Cloud Apps File Policy
## Investigate & Remediate Insider Risk Threats
- Broad range of risks and violations from insiders:
	- Data Spillage
	- IP Theft
	- Fraud
	- Insider Trading
	- Sensitive Data Leaks
	- Security Violations
	- Confidentiality Violations
	- Workplace Violence
	- Policy Violations
	- Conflicts of Interest
	- Workplace Harassment
	- Regulatory Compliance Violations
- Insider risk policy templates:
	- Departing employee data theft
	- Data leaks
	- Security policy violations
	- Heath record misuse
	- Risky browser usage
- Policy settings:
	- Privacy and indicators
	- Policy timeframes
	- Intelligent detections
## Searching Content with MS Purview eDiscovery
- Tool in the MS Purview portal for searching, placing holds, and exporting content.
- Available to users assigned roles **eDiscovery Manager** or **eDiscovery Administrator**.
- Licensing effects feature availability (Core eDiscovery is included with E3/E5).
- eDiscovery helps security and compliance teams find, preserve, and export content during investigations, legal inquiries, or incident response.
### Prerequisites for using eDiscovery
- Only explicitly assigned users can access eDiscovery to ensure investigations are secure and auditable.
- Required roles:
	- eDiscovery Manager: Create and manage cases, search, and export.
	- eDiscovery Administrator: All manager permissions plus role assignment and settings control.
- To confirm access, navigate to eDiscovery in the portal. The *Cases* page should appear if the user has the correct role and license.
### Create an eDiscovery Search
- Every search must be part of a case, which provides a secure, auditable workspace for managing investigations.
- Cases provide access control, auditing, and a consistent investigation workspace.
- Only the case member can access a case, even with eDiscovery roles.
- The creator of the case is added automatically as a member.
### Conduct an eDiscovery Search
- Search across MS 365 services to locate content relevant to security incidents, regulatory requests, or internal investigations.
- Define criteria: Name the search and set filters using keywords or conditions like date, sender, or content type.
- Select data sources: Choose users, groups, sites, or tenant-wide sources to narrow or broaden your search scope.
- Build the query: Use the condition builder, write KQL, or try copilot or search by file to create your search logic.
- View statistics or a sample to validate your search, then refine, export, or move to the next step.
## Search & Investigate with MS Purview Audit
- MS Purview Audit helps orgs understand how MS 365 is being used by capturing user and admin activity to support security, compliance, and operational transparency.

| Audit (Standard)                            | Audit (Premium)                                 |
| ------------------------------------------- | ----------------------------------------------- |
| Enabled by default.                         | Custom retention policies.                      |
| 180 day log retention.                      | Default 1 year retention for core services.     |
| Access through portal, powershell, and API. | Intelligent insights into activity patterns.    |
| Export to CSV.                              | Higher API bandwidth for advanced integrations. |
### Configure & Manage MS Purview Audit
- If auditing isn't already enabled, turn it on through the MS Purview portal or by running the `Set-AdminAuditLogConfig` cmdlet in powershell.
- Verify licensing and subscription.
	- Audit (Standard): Included in MS 365 E3/E5/F1/F3 and Office 365 E1/E3/E5.
	- Audit (Premium): Requires MS 365 E5, E5 Compliance, or relevant add-on.
- Assign roles to manage audit logs:
	- Audit Reader: search and export only.
	- Audit Manager: Search, export, and manage audit settings.
### Conduct Searches with Audit (Standard)
- Use MS Purview portal or `Search-UnifiedAuditLog` cmdlet.
- Filter by activity type, user, file/site, date range, or workload.
- Export results or access logs via API for automation.
- Monitor job progress and view detailed results.
- Filter by action, user, IP, record type, or item details.
- Export data (up to 50 KB for Standard).
### Investigation with Audit (Premium)
- Audit premium provides deeper visibility into user activity, helping orgs investigate sensitive access, detect suspicious behavior, and respond to incidents.
- Example: Investigate Email Access
	- Use `MailItemsAccessed` to see what messages were accessed, by whom, and from where.
	- Bind access: Logs individual email views.
	- Sync access: Logs bulk download.
	- Logging pauses after 1000 bind events/day per mailbox (throttling).
	- Duplicate entries are filtered automatically to reduce noise.
- Search in MS Purview portal or use powershell for advanced filtering.
- Export results to CSV to review access type and throttling details in the raw audit data.
### Export Audit Log Data
- Exporting audit logs to CSV supports deeper analysis and helps meet client compliance requirements.
	1. Run a search in the MS Purview portal.
	2. Select Export to download up to 50000 audit records as a CSV file.
	3. Open the CSV in Excel.
	4. Use Power Query Editor to transform the `AuditData` column from JSON to readable columns.
### Configure Audit Retention with Audit (Premium)
- Custom retention policies help orgs meet regulatory requirements by controlling how long audit data is kept.
- Default Retention (Premium):
	- 1 year retention for Exchange, SharePoint, OneDrive, and MS Entra ID.
	- Default policy can't be changed, but custom policies can override it.
- Custom Retention Policies:
	- Create up to 50 policies for specific users, record types, or activities.
	- Retain data for 7 days to 10 years (10 year retention requires add-on license).
	- Retention is determined when data is logged and changes only apply to new data.
### Investigate MS 365 Activities to Identify Threats
Threat actors frequently target MS 365 infra directly via Business Email Compromise (BEC) and AiTM phishing. Uncovering these threats requires deep inspection of app telemetry.
- MS Purview Audit (Standard vs. Premium): Retains logs for SharePoint, OneDrive, Exchange, and Entra ID authentications. Purview Audit Premium is essential for advanced investigations because it provides high value operational logs such as `MailItemsAccessed`. This specific log is critical during a BEC investigation, as it definitely proves whether an attacker actually read specific sensitive emails after compromising an inbox, thereby turning a basic compromise into a reportable data breach.
- eDiscovery (Content Search): Primarily a compliance tool, it is routinely repurposed for incident response to search the deep contents of mailboxes and SharePoint sites. If an attacker leverages a compromised account to send out malware laterally via internal MS Teams chats or Exchange emails, analysts utilize Content Search to identify and hard purge the malicious messages globally across the tenant.
- MS Graph Activity Logs: Provide granular visibility into app consent workflows and API calls. This is vital for hunting malicious OAuth apps deployed by attackers. Adversaries often trick users into granting perms to a rogue app, providing the attacker with persistent, MFA-bypassing access to read emails and access files via the [[Microsoft Defender XDR#Threat Investigation with MS Graph Security API|Graph API]].