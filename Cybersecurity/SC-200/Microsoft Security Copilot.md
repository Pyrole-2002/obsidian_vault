- AI imitates human behavior by using machine learning to interact with the environment and execute tasks without explicit directions on what to output.
- Generative AI creates original content, such as GenAI that has been built into chat apps. GenAI apps take in natural language input, and return appropriate responses in a variety of formats.
- GenAI apps are powered by LLMs which are a specialized type of ML model that you can use to perform NLP tasks.
- Copilots are often integrated into other apps and provide a way for users to get help with common tasks from a GenAI model.
- Copilot is deeply embedded into the Defender XDR incident queue. It acts as an intelligent orchestrator, instantly summarizing complex multi-stage attacks and translating obscure, obfuscated PowerShell scripts into natural language explanations.
- **Compute Billing Architecture (SCU):** Security Copilot is governed by Security Compute Units (SCU). Microsoft announced that MS 365 E5 and E7 license holders receive an embedded capacity of 400 SCUs per month for every 1000 paid user licenses, capped at an upper limit of 10000 SCUs.
- **Provisioned vs. Overage Models:** For orgs requiring dedicated capacity beyond the E5 inclusion, SCUs can be purchased. Provisioned SCUs are billed hourly at a flat rate, regardless of utilization. Overage SCUs are consumed on-demand and billed fractionally to handle unexpected workload surges during major incident response engagements.
- **Analyst Efficiency Metrics:** Empirical data indicates that leveraging copilot reduces Mean Time to Respond (MTTR) dramatically. Orgs report up to a 68% decrease in the probability of an incident reopening and a 22% decrease in the number of subsequent alerts generated per incident, as copilot enables analysts to identify the root cause earlier in the kill chain.
### Azure OpenAI
- It is Microsoft's cloud solution for deploying, customizing, and hosting LLMs.
- It consists of:
	- Pre-trained GenAI models.
	- Customization capabilities.
	- Built-in tools to detect and mitigate harmful use cases so users can implement AI responsibly.
	- Enterprise-grade security with RBAC and private networks.
# Microsoft Security Copilot
- It is an AI-powered, cloud based security analysis tool that enables analysts to respond to threats quickly, process signals at machine speed and assess risk exposure more quickly than may otherwise be possible.
- It combines powerful LLMs with a security specific model from Microsoft.
- It integrates with Microsoft and non-Microsoft sources.
- Copilot learns at machine speed to help analysts identify and respond to emerging threats.
- Enterprise data is protected by comprehensive enterprise compliance and security controls.
- Microsoft products like [[Microsoft Defender XDR]] embed Copilot directly inside their UI.
- In Defender XDR, incidents and advanced hunting are examples of embedded copilot.
- **Session:** A particular conversation within MS Security Copilot.
- **Prompt:** A specific user statement or question within a session.
- **Capability:** A function MS Security Copilot uses to solve part of a problem.
- **Plugin:** A collection of capabilities by a particular resource.
- **Orchestrator:** Used to compose skills together, to answer a user's prompt.
## Using MS Security Copilot
- To start using MS Security Copilot, orgs need to take steps to onboard the service and users.
	1. Navigate to https://securitycopilot.microsoft.com.
	2. Choose an Azure subscription and choose or create a new resource group.
	3. Provision copilot capacity: name your capacity and add at least 1 security compute unit (SCU).
	4. Set up the default environment.
	5. Assign role permissions.
### Provisioning Capacity
- Before users can start using copilot, admins need to provision and allocate capacity.
- To provision capacity:
	- You must have an Azure subscription.
	- You must be an Azure owner or Azure contributor, at a resource group level, as a minimum.
- There are 2 options for provisioning capacity:
	- Provision within Security Copilot (recommended.
	- Provision capacity through Azure portal.
- Copilot provides a usage monitoring dashboard for capacity owners.
### Set up Default Environment
- To set up the default environment, you need to have one of the following [[Azure Fundamentals#Microsoft Entra ID (Azure Active Directory)|MS Entra ID]] roles:
	- Global Administrator
	- Security Administrator
- You're prompted to configure settings including:
	- The SCU capacity to allocate.
	- Geographic location of tenant, customer data collected is stored there.
	- Opt-in or opt-out of data sharing options.
	- Roles.
### Role Permissions
- Copilot introduces two roles that function like access groups but aren't MS Entra ID roles:
	- Copilot Owner
	- Copilot Contributor
- Copilot roles are defined and managed within copilot and grant access only to copilot features.
## Embedded Experiences of MS Security Copilot
- Copilot is accessible directly from some MS Security products like Defender XDR, Microsoft Entra, [[Microsoft Purview]].
- Copilot invokes the product specific capabilities directly, providing processing efficiency.
- Easily transition to the standalone experience to pursue a more detailed, cross product investigation that brings to bear all the copilot capabilities enabled for your role.
- MS plugin for the specific solution must be enabled and the user must have role permission to access copilot plus any role permission required to access data associated with the specific solution.
- Guided responses recommend actions in one or more of the following categories:
	- **Triage:** Includes a recommendation to classify incidents as informational, true positive, or false positive.
	- **Containment:** Includes recommended actions to contain an incident.
	- **Investigation:** Includes recommended actions for further investigation.
	- **Remediation:** Includes recommended response actions to apply to specific entities involved in an incident.
- Copilot can generate incident reports containing following information:
	- The main incident management actions' timestamps.
	- The analysts involved in incident response.
	- Incident classification, including analysts' comments on how the incident was evaluated and classified.
	- Investigation actions applied by analysts and noted in the incident logs.
	- Remediation actions done.
	- Follow up actions.
## Security Copilot Agents in MS Defender
- The security store in the MS Defender portal offers various agents that help you perform your security tasks efficiently.
- These include MS Security Copilot agents published by MS and partners.
- These agents integrate with MS Defender and carry out various security operations (SOC) tasks, such as incident triage, investigation, threat hunting, and threat intelligence.

| Phishing Triage Agent                                                                                | Threat Intelligence Briefing Agent                                                                                                                                                     | Threat Hunting Agent                                                                                                                                                                                                         | Dynamic Threat Detection Agent                                                                                                                                          |
| ---------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Helps SOC analysts triage and<br>classify user-submitted incidents. The agent operates autonomously. | Provides security teams with regular, customized threat intelligence briefings. The agent autonomously gathers and synthesizes relevant threat intelligence data from various sources. | Assists with threat hunting by enabling threat investigations using natural language from start to finish. It generates KQL queries and also interprets results, surfaces insights, and guides you through hunting sessions. | An always-on, adaptive backend service that uncovers hidden threats across Defender and Sentinel environments. It uses AI to identify gaps and uncover false negatives. |
| MS Defender for Endpoint P2 license required and Security Admin to setup.                            | The Security Admin role is required to set up and manage the agent.                                                                                                                    | Built into Security Copilot in Defender XDR.                                                                                                                                                                                 | Bult into Security Copilot in Defender XDR.                                                                                                                             |
