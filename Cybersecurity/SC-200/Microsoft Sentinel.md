# Kusto Query Language (KQL)
A KQL query is a **read‑only** request to process data and return results. It follows a logical flow:
- Data source (table)
- Pipe (`|`) operator
- Transformation/Filtering/Shaping operators
- Final `project` or `render`
```sql
TableName
| where Condition
| summarize Aggregation by Grouping
| order by Column desc
| take 10
```
- Case‑sensitive for table/column names (usually PascalCase).
- Pipe (`|`) separates operators; data flows from left to right.
- Statements end with semicolon (`;`) only when separating multiple queries.
- Comments: `// single line`, `/* multi‑line */`.
## Essential KQL Operators
### Data Exploration

| Operator    | Purpose                                                             | Example                                                    |
| ----------- | ------------------------------------------------------------------- | ---------------------------------------------------------- |
| `search`    | Full-text search across all tables/columns (use sparingly in prod). | `search "malware"`                                         |
| `find`      | Find a string in specific columns across multiple tables.           | `find in (SigninLogs, AuditLogs) where * contains "admin"` |
| `union`     | Combine rows of two or more tables.                                 | `union SigninLogs, AuditLogs`                              |
| `range`     | Generate a table of values (for testing).                           | `range x from 1 to 10 step 1`                              |
| `datatable` | Create an inline table.                                             | `datatable (Name:string, Age:int) ["Alice",30]`            |
| `print`     | Output a single row.                                                | `print now()`                                              |
### Filtering & Conditioning
`where` filters rows using boolean expressions.
Operators: `==`, `!=`, `=~` (case-insensitive), `!~`, `contains`, `contains_cs`, `startswith`, `endswith`, `has` (whole word match), `has_cs`, `in`, `!in`, `between`, `and`, `or`, `not`.
```sql
SecurityEvent
| where EventID == 4625 and TimeGenerated > ago(1d)
| where Account contains "admin"
| where Computer in~ ("DC01", "DC02")
```
### Column Selection & Shaping

| Operator         | Purpose                                                      |
| ---------------- | ------------------------------------------------------------ |
| `project`        | Select columns to keep; can rename, create computed columns. |
| `project-away`   | Remove specific columns.                                     |
| `project rename` | Rename columns without dropping others.                      |
| `extend`         | Add new calculated columns.                                  |
| `distinct`       | Unique combinations of specified columns.                    |
| `take`/`limit`   | Return N rows (no guaranteed order).                         |
| `top`            | Return top N rows sorted by a column.                        |
| `sample`         | Random sample of rows.                                       |

```sql
SigninLogs,
| project TimeGenerated, UserPrincipalName,
	Location = tostring(LocationDetails.countryOrRegion)
| extend IsAdmin = iff(UserPrincipalName contains "admin", true, false)
```
### Aggregation & Summarization
`summarize` : Aggregate rows by grouping keys. Produces a single row per group.
Aggregation Functions: `count()`, `dcount()` (distinct count), `sum()`, `avg()`, `min()`, `max()`, `stdev()`, `variance()`, `percentile()`, `make_list()`, `make_set()`, `take_any()`, `arg_max()`, `arg_min()`, `bin()` (group by time buckets).
```sql
SecurityEvent
| where EventID == 4625
| summarize FailedAttempts = count() by bin(TimeGenerated, 1h), Account, Computer
| where FailedAttempts > 10
```
`arg_max`/`arg_min` : Return entire row for max/min value in a group.
```sql
DeviceProcessEvents
| summarize arg_max(TimeGenerated, *) by DeviceName
```
### Joins
- `join` combines rows from two tables based on matching columns.
- Join Flavors:
	- `inner` : only matching rows (default).
	- `innerunique` : deduplicates left side (default if not specified).
	- `leftouter`/`rightouter` : all from left/right + matches.
	- `fullouter` : all rows from both sides.
	- `leftanti`/`rightanti` : rows without a match in the other table.
	- `leftsemi`/`rightsemi` : left rows that have a match (without right columns).
```sql
SigninLogs
| where ResultType == 0
| join kind=leftanti (
    IdentityInfo
    | where AccountUpn != ""
) on $left.UserPrincipalName == $right.AccountUpn
```
### Formatting and Ordering
- `order by Column [asc/desc]`
- `sort by` (alias)
- `mv-expand` : expand multi‑value (dynamic/array) columns
- `parse` : extract substrings using patterns
- `extract` : regex extraction
- `parse_json()` : cast string to dynamic
- `tostring()`, `toint()`, `todouble()`, `todatetime()`, `tobool()`
### Time Operations
| Function                                          | Description                      |
| ------------------------------------------------- | -------------------------------- |
| `ago(timespan)`                                   | Relative time from now           |
| `now()`                                           | Current UTC time                 |
| `datetime(YYYY-MM-DD HH:MM:SS)`                   | Literal datetime                 |
| `startofday()`, `startofweek()`, `startofmonth()` | Truncate to period start         |
| `endofday()`, `endofweek()`, `endofmonth()`       | Period end                       |
| `format_datetime()`                               | String formatting                |
| `datetime_diff()`                                 | Difference between two datetimes |

```sql
let lookback = 7d;
SigninLogs
| where TimeGenerated > ago(lookback)
| extend Hour = floor(TimeGenerated, 1h)
```
## Advanced KQL
### `let` Statements
Define reusable variables, functions, or tables. Scoped to the query.
```sql
let suspiciousIPs = datatable (IP:string) ["1.2.3.4", "5.6.7.8"];
let timeframe = 6h;
SigninLogs
| where TimeGenerated > ago(timeframe)
| where IPAddress in (suspiciousIPs)
```
User-defined functions:
```sql
let isWeekend = (d:datetime) { dayofweek(d) >= 6 };
SigninLogs | where isWeekend(TimeGenerated)
```
### `materialize()`
Capture an intermediate result so it’s computed once when used multiple times. Useful in complex joins/subqueries.
```sql
let baseline = materialize(
    DeviceNetworkEvents
    | where TimeGenerated > ago(30d)
    | summarize count() by RemoteIP
);
DeviceNetworkEvents
| where TimeGenerated > ago(1d)
| join kind=leftanti baseline on RemoteIP
```
### `externaldata` Operator
Query external files (Azure Blob, GitHub, etc.) as a table. Often used to reference threat intelligence feeds.
```sql
let threatfeed = externaldata(IP:string, Category:string)
[@"https://raw.githubusercontent.com/.../ti.csv"]
with (ignoreFirstRecord=true, format="csv");
```
### Cross-Resource Queries
In Log Analytics, query across workspaces/apps using `workspace()`, `app()`.  
In Sentinel, you may query multiple workspaces:
```sql
union workspace('workspace1').SigninLogs, workspace('workspace2').SigninLogs
```
### Window Functions
`scan` – apply a sequential process over rows (like sessionization).  
`partition` – complex grouping inside operators (e.g., `partition by` in `summarize`).
### Query Performance
- Always filter on time first (`where TimeGenerated > ago(…)`).
- Use `has` instead of `contains` when searching full tokens.
- Prefer `==` over `=~` when case matters.
- Avoid `search *` in production.
- Use `materialize()` strategically.
- Limit `join` on large tables, use `hint.strategy=shuffle` for big data.
### String Functions
```sql
strcat("Hello", " ", "World")
strlen(column)
substring(column, start, length)
toupper(column) / tolower(column)
trim(column), trim_start(), trim_end()
replace(@"\s", "_", column)
split(column, ",")
parse_url(column)
parse_user_agent(column)
indexof(column, "needle")
endswith(column, "suffix")
startswith(column, "prefix")
has_any_ipv4(column)
```
### Date/Time Functions
```sql
ago(2d)
now()
datetime_add('hour', 1, TimeGenerated)
datetime_part('day', TimeGenerated)
format_datetime(TimeGenerated, 'yyyy-MM-dd')
dayofweek(TimeGenerated)
week_of_year(TimeGenerated)
datetime_diff('hour', end, start)
```
### IP Functions
```sql
ipv4_is_private(IP)
ipv4_netmask_suffix(IP, mask)
ipv4_is_match(IP, "10.0.0.0/8")
geo_info_from_ip_address(IP)
```
### Aggregation & Statistical
```sql
count(), dcount(column), dcountif(column, condition)
sum(), min(), max(), avg(), stdev()
percentile(column, 95), percentiles(column, 50, 95)
make_list(column), make_set(column)
arg_max(TimeGenerated, *)
bin(TimeGenerated, 1h)
autocluster()
diffpatterns()
```
### Security-Specific Functions
```sql
parse_json() / todynamic()
bag_keys(), bag_merge()
base64_decode_tostring()
url_decode(), url_encode()
utf8_encode(), utf8_decode()
hash_sha256()
```
## KQL in MS Sentinel & [[Microsoft Defender XDR|Defender XDR]]
### Sentinel Key Tables
- **SecurityEvent** – Windows security events (Event IDs like 4624, 4625, 4688)
- **SigninLogs** – Azure AD sign‑in logs
- **AuditLogs** – Azure AD audit logs
- **OfficeActivity** – Office 365 audit logs (SharePoint, Exchange, Teams)
- **CommonSecurityLog** – Syslog/CEF from firewalls, appliances
- **Syslog** – Standard syslog
- **Heartbeat** – Agent health
- **AzureActivity** – Azure Resource Manager operations
- **ThreatIntelligenceIndicator** – Threat intelligence imported into Sentinel
- **AWSCloudTrail** – AWS logs (if connected)
- **AzureDiagnostics** – Diagnostic logs from many Azure services
### Defender XDR Tables
All prefixed with `Device*`, `Identity*`, `Email*`, etc. Examples:
- `DeviceProcessEvents` – Process creation (great for C2, malware execution)
- `DeviceNetworkEvents` – Network connections
- `DeviceFileEvents` – File operations (creation, deletion, modification)
- `DeviceRegistryEvents` – Registry modifications
- `DeviceImageLoadEvents` – DLL loading
- `IdentityLogonEvents` – On‑premises authentication (Defender for Identity)
- `EmailEvents` / `EmailUrlInfo` – Emails and extracted URLs
- `AlertEvidence` / `AlertInfo` – Alerts from all Defender workloads
Cross-product query example:
```sql
union DeviceProcessEvents, IdentityLogonEvents
| where Timestamp > ago(1d)
| where ProcessCommandLine contains "mimikatz"
```
## Security Use Cases
### Impossible Travel (Azure AD)
Detect users logging in from two distant locations in a short time.
```sql
let ImpossibleTravelWindow = 1h;
SigninLogs
| where ResultType == 0  // success
| where TimeGenerated > ago(7d)
| project TimeGenerated, UserPrincipalName, 
          Country = tostring(LocationDetails.countryOrRegion),
          IPAddress
| sort by UserPrincipalName, TimeGenerated asc
| partition by UserPrincipalName (
    scan declare (prevTime: datetime, prevCountry: string) with 
    ( step s: true => prevTime = iff(isnull(s.prevTime), TimeGenerated, 
         prev(TimeGenerated)), 
         prevCountry = iff(isnull(s.prevCountry), Country, prev(Country)); )
)
| where prevTime != TimeGenerated
| extend TimeDiff = (TimeGenerated - prevTime) / 1h
| where TimeDiff < ImpossibleTravelWindow and Country != prevCountry
| project TimeGenerated, UserPrincipalName, Country, prevCountry, IPAddress
```
### Brute Force Detection (Windows Security)
Multiple failed logins followed by a success for the same account.
```sql
let failureWindow = 15m;
let successWindow = 5m;
SecurityEvent
| where EventID in (4624, 4625)
| where TimeGenerated > ago(1d)
| summarize Failed = countif(EventID == 4625),
            Success = countif(EventID == 4624),
            TimeOfFirstFailure = minif(TimeGenerated, EventID == 4625),
            TimeOfSuccess = minif(TimeGenerated, EventID == 4624)
    by Account, Computer, bin(TimeGenerated, 1m)
| where Failed >= 5
| where Success >= 1
| where (TimeOfSuccess - TimeOfFirstFailure) between (1m .. successWindow)
```
### Privilege Escalation via Special Logon (Admin or SYSTEM)
Detect admin/SYSTEM logons that are not normal.
```sql
SecurityEvent
| where EventID == 4672  // Special Logon (privileged)
| where TimeGenerated > ago(1d)
| parse Account with * '\\' AccountName
| where AccountName !in~ ("SYSTEM", "NETWORK SERVICE", "LOCAL SERVICE")
| join kind=leftanti (
    IdentityLogonEvents  // or a baseline of known admin accounts
    | where Timestamp > ago(7d)
    | distinct AccountUpn
) on $left.Account == $right.AccountUpn
| project TimeGenerated, Computer, Account, PrivilegeList
```
### Suspicious Powershell Execution (Defender for Endpoint)
Detect encoded commands, download cradles, etc.
```sql
DeviceProcessEvents
| where FileName =~ "powershell.exe"
| where ProcessCommandLine contains "-enc" or 
        ProcessCommandLine contains "FromBase64String" or
        ProcessCommandLine contains "IEX" or
        ProcessCommandLine contains "New-Object Net.WebClient"
| where Timestamp > ago(1d)
| project Timestamp, DeviceName, AccountName, ProcessCommandLine
| take 50
```
### C2 Beaconing Detection (Network)
Repeated small periodic connections to a rare IP.
```sql
let interval = 10m;
DeviceNetworkEvents
| where Timestamp > ago(1d)
| summarize ConnectionCount = count(),
            IntervalVariance = stdev(datetime_diff('second', Timestamp, prev(Timestamp))) 
            by DeviceName, RemoteIP, bin(Timestamp, interval)
| where ConnectionCount > 24 and IntervalVariance < 2  // low variance indicates beaconing
| join kind=leftanti (
    DeviceNetworkEvents
    | where Timestamp > ago(30d)
    | summarize count() by RemoteIP
    | where count_ > 1000  // common IPs excluded
) on RemoteIP
| project DeviceName, RemoteIP, ConnectionCount, IntervalVariance
```
### Azure Sentinel Near-Realtime Rule: Modified Forwarding Rule
```sql
OfficeActivity
| where Operation =~ "Set-InboxRule"
| where Parameters contains "ForwardTo" or Parameters contains "RedirectTo"
| where TimeGenerated > ago(1h)
| project TimeGenerated, UserId, ClientIP, Parameters
```
### Data Exfiltration via Email (Large Attachments)
```sql
EmailEvents
| where Timestamp > ago(1d)
| where AttachmentCount > 10
| project Timestamp, SenderFromAddress, RecipientEmailAddress, AttachmentCount, Subject
| order by AttachmentCount desc
```
---
# Microsoft Sentinel
- MS Sentinel is the centralized Security Information and Event Management (SIEM) and Security Orchestration, Automation, and Response (SOAR) platform.
- Sentinel significantly expands the hunting timeline beyond XDR's 30 day limitation, enabling deep historical analysis over months or years of data.
- **Summary Rules**
	- Querying TBs of raw network, firewall or proxy logs over long periods is exceptionally slow and computationally expensive.
	- Analytics rules relying on massive datasets frequently time out, causing the system to auto-disable the rule. Summary rules solve this architectural bottleneck by aggregating high volume logs at the point of ingestion, calculating summary stats, and writing the distilled results into a Custom Log (`_CL`) table.
	- Creating a Summary Rule:
		1. Navigate to ***MS Sentinel>Configuration>Summary Rules***.
		2. Select ***+Create*** to open the wizard.
		3. Name the rule.
		4. Write the KQL aggregation logic.
		5. Specify the destination custom table.
		6. Set the scheduling frequency, review the diagnostic settings prompt to ensure tracking of run failures, and save the rule.
```sql
--// Example: Summary Rule Logic aggregating Firewall logs to detect massive data transfers
let csl_columnmatch=(column_name: string) {
    summarized_CommonSecurityLog
    | where isnotempty(column_name)
    | extend Date = format_datetime(TimeGenerated, "yyyy-MM-dd")
    --// Aggregating connection counts and bytes reduces billions of rows to mere thousands
    | summarize ConnectionCount = count(), TotalBytes = sum(SentBytes) by SourceIP, DestinationIP, DestinationPort
};
--// The output of this query is continuously written to the specified _CL table for fast, cheap alerting.
```
- MS Sentinel architecture emphasizes multi-tier data logging to control ingestion and retention costs while seamlessly maintaining massive telemetry volumes for AI modeling.
- Automation is divided into 2 distinct components:
	- Automation Rules: Provide lightweight, built-in logic to triage incidents automatically, such as changing an incident's severity, assigning it to a specific analyst tier, or adding contextual tags based on the alert's properties.
	- Playbooks: Powered by Azure Logic Apps and handle complex, multi-step orchestration workflows that interact with third-party app programming interfaces (APIs), such as opening a ticket in ServiceNow or querying an external threat intelligence feed.
- RBAC in Sentinel relies on Azure resource roles.
	- The `Microsoft Sentinel Reader` role allows analysts to view data, incidents, and workbooks.
	- The `Microsoft Sentinel Contributor` role is required to create or edit analytics rules, summary rules, and automation workflows.
	- For advanced integrations, the `Microsoft Sentinel Automation Contributor` role allows automation rules to trigger Logic App playbooks seamlessly.
- Data ingested into Sentinel is stored in Azure Log Analytics workspaces, segmented into specific tables. To optimize the platform and reduce financial overhead, admins must balance query performance with storage costs by utilizing appropriate data tiers for each table.

| Storage Tier     | Interactivity & Retention Limits                                      | Bet Operational Use Case                                                                   | Performance & Feature Trade-offs                                                                 |
| ---------------- | --------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------ |
| Analytics (Hot)  | Up to 2 years interactive query capability. Full KQL support.         | High value telemetry requiring real-time custom detection rules and active threat hunting. | Highest ingestion and storage cost per GB.                                                       |
| Data Lake (Cold) | Up to 12 years retention. Consolidated legacy Auxiliary log features. | Compliance auditing, context generation, and long-term historical forensic investigations. | Slower query speeds via Search Jobs. Real-time analytics rules cannot run natively on this tier. |
| Basic Logs       | 30 days interactive query window.                                     | High-volume, low-security value telemetry.                                                 | Alerting is highly limited. Restricted KQL capabilities restrict complex joins.                  |
- Supported analytics tables are automatically mirrored into the Data Lake tier at no additional cost. This provides a unified, highly scalable location for large-scale AI ops and graph-based relationship analysis.
- Altering Log Analytics Table Tiers:
	1. Navigate to the ***MS Defender Portal***.
	2. Select ***MS Sentinel>Configuration>Tables***.
	3. Locate the target log table.
	4. Select the table to open the details panel, then select ***Data Retention Settings***.
	5. Switch the tier from ***Analytics*** to ***Data Lake***, specify the retention period spanning up to 12 years, and select ***Save***.
- Workbooks provide interactive data visualization within Sentinel, leveraging underlying KQL queries to render charts, graphs, and performance metrics.
- Sentinel also provides a built-in SOC optimization engine. This dynamically analyzes the workspace's data ingestion patterns and rule execution health to recommend architectural adjustments, such as identifying costly analytics rules that yield zero true-positive incidents, or suggesting specific data connectors based on newly detected Azure workloads.
- With MS Sentinel, your org can:
	- Collect data (using data connectors).
	- Detect previously undetected threats using threat intelligence and analytics.
	- Investigate threats with AI.
	- Respond to incidents rapidly with built-in orchestration and automation of common tasks.
- MS Sentinel Operational Workflows
	1. Data connectors
	2. Parsers
	3. Workbooks
	4. Analytic rules
	5. Hunting queries
	6. Notebooks
	7. Incidents and investigations
	8. Automation playbooks and Azure Logic Apps custom connectors
	9. Watchlists
- Use MS Sentinel if you want to:
	- Collect event data from various sources.
	- Perform security operations on that data to identify suspicious activity.
- Security ops could include:
	- Visualization of log data.
	- Anomaly detection.
	- Threat hunting.
	- Security incident investigation.
	- Automated response to alerts and incidents.
- Decide whether it's the right fit for you:
	- Cloud-native SIEM. There are no servers to provision, so scaling is effortless.
	- Benefits of MS research and machine learning.
	- Support for hybrid cloud and on-premises environments.
	- SIEM and a data lake all in one.
- Clear requirements:
	- Support for data from multiple cloud environments.
	- Features and functionality required for a SOC, without too much admin overhead.

| Feature       | Sentinel SIEM                                                     | Sentinel Platform                          |
| ------------- | ----------------------------------------------------------------- | ------------------------------------------ |
| Focus         | Operational SOC workflows.                                        | Strategic.                                 |
| Data Storage  | Log analytics workspace for data collection from 350+ connectors. | Data lake + tiered retention.              |
| Analytics     | KQL-based threat hunting.                                         | Graph based for attack path visualization. |
| Integration   | Defender portal, Copilot.                                         | Defender XDR, Purview & custom apps.       |
| Retention     | 30 days default (extendable).                                     | Up to 12 years in data lake.               |
| Customization | Analytics rules, playbooks, workbooks & connectors.               |                                            |

### Ingest Data into the MS Sentinel SIEM
- Telemetry ingestion is managed via Data Connectors. The selection of a connector depends entirely on the data source and the underlying architecture.
	- **Windows Security Events via AMA:** Replaces the legacy Log Analytics agent. It relies on Data Collection Rules (DCRs) to precisely scope which Windows Event IDs are forwarded to the SIEM. Admins can select from "All Events", "Common", or "Custom" filtering to exclude noisy, low-value event IDs at the source, significantly reducing ingestion costs.
	- **Windows Event Forwarding (WEF):** An alternative architectural pattern where endpoints forward logs to a centralized Windows collector, which then utilizes the Azure Monitor Agent (AMA) to forward the aggregated events to Sentinel.
	- **Syslog and Common Event Format (CEF) via AMA:** Utilized primarily for network apps such as Palo Alto or Cisco Firewalls. A dedicated Linux forwarder machine running the AMA service listens on port 514, parses the incoming CEF payload, and streams the structured data directly into the `CommonSecurityLog` table in Sentinel.
	- **Azure Policy and Diagnostic Settings:** Used to scale telemetry collection effortlessly across multiple Azure subscriptions. Azure policies can automatically enforce the deployment of diagnostic settings on newly provisioned cloud resources, guaranteeing that all Azure activity logs and resource logs are routed to the centralized SIEM without manual intervention.
	- **Threat Indicators:** Ingested via STIX / TAXII protocols or native threat intelligence API connectors. These indicators are populated into the `ThreatIntelligenceIndicator` table and are cross reference by analytics rules against incoming network traffic.
### Configure Detections
- Sentinel analytic rules serve as the analytical brain of the SIEM, evaluating disparate data streams on predefined schedules to generate actionable alerts and incidents.
	- **Scheduled Analytics Rules:** Standard KQL queries executed at defined intervals.
	- **Near-Real-Time (NRT) Rules:** Designed for high-velocity threats, executing every minute. Due to their frequency, NRT rules are highly constrained regarding query complexity and execution time limits to prevent platform latency.
	- **Anomaly Rules:** Utilize Microsoft's built-in ML algos to establish behavioral baselines, triggering deviations without requiring explicit threshold programming.
- Detections must be continually analyzed for attack vector coverage utilizing the MITRE ATT&CK matrix. The Sentinel platform natively maps rules to specific tactics and techniques, allowing security engineers to visualize blind spots in their detection posture.
### Workspace Manager
- MS Sentinel's workspace manager enables users to centrally manage multiple MS Sentinel workspaces within one or  more Azure tenants.
- The central workspace can consolidate content items to be published at scale to member workspaces. Workspace manager is enabled in settings.
- Plan for the MS Sentinel workspace:
	- Single-tenant with a single MS Sentinel workspace.
	- Single-tenant with regional MS Sentinel workspaces.
	- Multi-tenant.
### Azure Lighthouse
- If you manage MS Sentinel workspaces (and other Azure resources) across multiple Entra ID tenants, Azure Lighthouse provides access to subscription level management tools.
- It allows you to select all the subscriptions containing workspaces you manage.
### MS Sentinel Tables
- **SecurityAlert:** Contains alerts generated from sentinel analytics rules. Also it could include alerts created directly from a sentinel data connector.
- **SecurityIncident:** Alerts can generate incidents. Incidents are related to alerts.
- **ThreatIntelligenceIndicator:** Contains user-created or data connector ingested indicators such as file hashes, IP addresses, domains.
- **Watchlist:** A sentinel watchlist contains imported data.
### KQL Search Jobs in the Data Lake
- When an analyst needs to hunt through the massive Data Lake (Cold Tier), when investigating a dormant backdoor implanted 14 months ago, standard interactive queries will likely time out.
- Instead, the analyst executes a Search Job. This triggers an asynchronous backend process that scans the long-term retention data.
- The results of the search are deposited into a temp, restorable table ending with the suffix `_SRCH`.
- The analyst can then query this smaller, focused table at interactive speeds without incurring massive compute penalties.
### Sentinel MCP Server & Notebooks
- To democratize advanced threat hunting, Microsoft introduced the Sentinel MCP Server. The MCP provides a unified, hosted integration standard allowing external AI agents to securely interface with the Sentinel SIEM graph and telemetry using natural language.
- This negates the need to construct complex infra pipelines to feed security data into ML models.
- Connecting VS Code to the Sentinel MCP Server:
	1. Install the MCP extension and the GitHub Copilot extension in VS Code.
	2. `Ctrl+Shift+P` to open the command palette, and type `MCP: Add Server`.
	3. Select the connection type as HTTP (Server-Sent Events).
	4. Enter the specific Sentinel MCP endpoint URL corresponding to the tenant.
	5. Authenticate via MS Entra ID (at least `Security Reader` role to authorize the connection).
	6. Open the agent chat interface and configure to Agent mode. Prompt something like: *Analyze the Data Lake. Find the top three users that are at a risk of a pass-the-hash attack and explain why based on IdentityLogonEvents.*

|           | **Playbooks**                                                                                                                                                                       |                                   **Workbooks**                                    | **Notebooks**                                                                                                                                                                                                               |
| :-------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------------------------------: | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Roles** | <ul><li>SOC Engineers</li><li>Analysts of all tiers</li></ul>                                                                                                                       | <ul><li>SOC engineers</li><li>Analysts of all tiers</li><li>SOC managers</li></ul> | <ul><li>Threat hunters/Tier 2-3 analysts</li><li>Incident investigators</li><li>Cyber data scientists</li><li>Security researchers</li></ul>                                                                                |
| **Uses**  | Automation of simpler, repeatable tasks:<ul><li>Ingestion – bring in external data</li><li>Enrichment (TI, GeoIP lookups, etc.)</li><li>Investigation</li><li>Remediation</li></ul> |                                   Visualization                                    | <ul><li>Querying Microsoft Sentinel & external data</li><li>Enrichment (TI, GeoIP, WhoIs lookups, etc.)</li><li>Investigation</li><li>Visualization</li><li>Hunting</li><li>Machine Learning & big data analytics</li></ul> |

#### Common Tables
- **AzureActivity:** Entries from the Azure activity log.
- **AzureDiagnostics:** Stores resource logs for services that use Azure diagnostics mode.
- **AuditLogs:** Audit log for [[Microsoft Defender XDR#Entra ID Protection|Entra ID]].
- **CommonSecurityLog:** Syslog messages using the Common Event Format (CEF).
- **OfficeActivity:** Audit logs for Office 365 tenants (Exchange, SharePoint and Teams).
- **SecurityEvent:** Security events collected from windows devices.
- **SigninLogs:** Entra ID sign in logs.
- **Syslog:** Syslog events on Linux computers using the log analytics agent.
- **Event:** Sysmon events collected from a Windows host.
- **WindowsFirewall:** Windows firewall events.
- **CloudAppEvents:** Events in cloud apps and Microsoft Defender for Cloud Apps.
- **DeviceEvents:** Device events table contains information about various event types.
- **DeviceFileEvents:** File creation, modification, and other file system events.
- **DeviceInfo:** Including their OS version, active users, and computer name.
- **DeviceLogonEvents:** User logons and other authentication events.
- **DeviceNetworkEvents:** Network connections and related events.
- **DeviceProcessEvents:** Process creation and related events.
- **DeviceRegistryEvents:** Creation and modification of registry entries.
- **DeviceTvm*:** Microsoft Defender Vulnerability Management Security & Software information.
- **EmailEvents:** Microsoft 365 email events, including email delivery and blocking events.
- **IdentityInfo:** Account information from various sources, including Entra ID.