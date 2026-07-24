# Computing Services
## 1. Azure Virtual Machines
- Windows, macOS or Linux VMs, most common type of compute, you choose your OS, memory, CPU, storage, you share hardware with other customers.
- Current limit on a per subscription basis is 20 VMs per region.
- Azure VMs are billed at an hourly rate.
- Single instance VMs have an availability of 99.9% when all storage disks are premium.
- 2 instances deployed in availability set will give 99.95% availability.
- Can attach multiple managed disks to your Azure VMs.
- When you launch an Azure VM, other networking components will be either created or associated to your VM.
	1. Network Security Group (NSG): attached to the NIC, virtual firewall with rules around ports and protocols.
	2. Network Interface (NIC): A device that handles IP protocols and network communication.
	3. VM instance is the actual running server.
	4. Public IP address is the address that you use publicly to access your VM.
	5. Virtual Network (VNet) is the network where your VM resides.
- Azure Scale Sets allows you to automatically increase or decrease your VM capacity.
- Create scaling policies to automatically add or remove capacity based on host metrics.
- Create health checks and set a repair policy to replace unhealthy instances.
- Associate a load balancer to distribute VMs across AZs.
- You can scale 100s to 1000s of VMs using scale sets.
- A load balancer can be associated with a scale set which allows to evenly distribute VMs across multiple Availability Zones (AZs) to make your application highly available. Load balancer probe checks can be used for more robust health checks.
- You have 2 different load balancer choices:
	1. Application Gateway is an HTTP/HTTPS web traffic load balancer with URL-based routing, SSL termination, session persistence, and web app firewall.
	2. Azure Load Balancer supports all TCP/UDP network traffic port forwarding and outbound flows.
- A scaling policy determines when a VM should be added or removed to meet current capacity requirements.
- Scale Out: When an instance should be added to the scale set to increase capacity, when CPU threshold (%) greater than X for Y minutes, add Z servers.
- Scale In: When an instance should be removed from the scale set to decrease capacity, when CPU threshold (%) less than X for Y minutes, remove Z servers.
- Health monitoring can be enabled to determine if your server is healthy or unhealthy.
- There are 2 modes of health monitoring:
	1. Application health extension pings an HTTP request to a specific path and excepts a status 200.
	2. Load balancer probe allows you to check based on TCP, UDP or HTTP requests.
- Automatic repair policy: If an instance is found to be unhealthy, then delete it and launch a new instance.
## 2. Azure Container Instances
Docker as a Service, run containerized apps on Azure without provisioning servers or VMs.
## 3. Azure Kubernetes Service (AKS)
Kubernetes as a Service, easy to deploy, manage and scale containerized applications. Uses the open Kubernetes (K8) software.
## 4. Azure Service Fabric
Tier-1 Enterprise Containers as a Service, distributed systems platform, runs in Azure or on-premises, easy to package, deploy and manage scalable and reliable microservices.
## 5. Azure Functions
Event-driven, serverless compute (functions) run code without provisioning or managing servers, you pay only for the compute time you consume.
## 6. Azure Batch
Plans, schedules and executes your batch computer workloads across running 100+ jobs in parallel, uses Spot VMs to save money (previously used low-priority VMs to save compute).
## 7. Azure Virtual Desktop
- Desktop and app virtualization service that runs on the cloud.
- Works across devices, Windows, macOS, iOS, Android, and Linux with apps that you can use to access remote desktops and apps.
- You can use browsers to access Azure VD hosted experiences.
- Use Azure VD for specific needs like when security is a concern because all data is saved on the server and cannot be left on the device of a user.
## 8. Azure App Service
- An HTTP based service for hosting web apps, REST APIs and mobile backends.
- Can choose your programming language and either a Windows or Linux environment.
- Platform as a Service.
Takes care of underlying infrastructure:
- Security patches for OS and languages
- Load balancing
- Autoscaling
- Automated manager
Makes it easy to implement common integrations and features like:
- Azure DevOps
- Github Integration
- Docker Hub Integration
- Package Management
- Easy to setup staging environments
- Custom domains
- Attaching TLS/SSL Certificates
You pay based on an Azure App Service Plan:
- Shared Tier: Free, Shared (Linux not supported)
- Dedicated Tier: Basic, Standard, Premium, PremiumV2, PremiumV3
- Isolated Tier
When you create your app you need to choose a unique name since it becomes a fully qualified domain.
- A runtime software/instructions are executed while your program is running. A runtime for Azure App Services will be a predefined container that has your programming language and commonly used library for that language installed.
Azure App Service allows you to define custom containers for Windows or Linux to use a different runtime or bundle in a package or software.
**Deployment Slots** allow you to create different environments of your application associated to different hostnames. Useful for staging or QA environments. You can also swap environments, useful for performing Blue/Green deploy.
### App Service Environment (ASE)
- ASE is an Azure App Service feature that provides a fully isolated and dedicated environment for securely running App Service apps at high scale.
- ASE allows hosting of Windows or Linux webapps, docker containers, mobile apps, functions.
- ASE are appropriate for app workloads that require very high scale, isolation and secure network access and high memory utilization.
- Customers can create multiple ASE within a single Azure region or across multiple regions making ASE ideal for horizontally scaling stateless application tiers in support of high requests per second (RPS) workloads.
- ASE comes with its own pricing tier (isolated tier).
- ASE can be used to configure security architecture.
- Apps running on ASE can have their access gated by upstream devices such as web app firewalls (WAF).
- ASE can be deployed into AZ using zone pinning.
- ASE has 2 deployment types: External ASE and ILB ASE.
## 9. Azure DNS
- It is a service responsible for resolving a service name to its IP address.
- It is a hosting service for DNS domains that provides name resolution by using Microsoft Azure infrastructure.
- **Public DNS** is internet-facing, allows managing domains for internet accessible domains. Pointing your domain to your website, setting records to prove you own the domain, records to connect your domain to your email server.
- **Private DNS** is internal-facing, allows using your own custom domains instead of the Azure provided domains. Many Azure services use fully qualified domain name (FQDN) to identify services on the network, example, Azure Storage Accounts FQDN: *http:/storageaccount.file.core.windows.net/*
## 10. Virtual Network Gateways
- A Virtual Private Network (VPN) extends a private network across a public network and enables users to send and receive data across shared or public networks as if their computing devices were directly connected to the private network.
- A virtual network gateway is the software VPN device for your Azure virtual network. When you deploy a virtual network gateway, it will deploy two or more specialized VMs in specific subnet you need to create called a gateway subnet. These deployed VMs contain routing tables and run specific gateway services.
- You choose a gateway type and that will determine if its a VPN gateway or an ExpressRoute gateway.
- Azure Private Links allows you to establish secure connections between Azure resources so traffic remains within the Azure network.
### Azure ExpressRoute
- Creates private connections between Azure datacenters and infrastructure on your premises or in a colocation environment.
- ExpressRoute connections don't go over the public internet and as a result can offer more reliability, faster speeds, consistent latencies and higher security.
- Connectivity can be from an any-to-any (IP VPN) network, a point-to-point ethernet network, or virtual cross-connection through a connectivity provider at a colocation facility.
- ExpressRoute Direct allows for greater bandwidth connections from 50 Mbps to 10Gbps, ideal for hybrid solutions with massive amounts of data or where latency matters.
---
# Storage Services
- Azure Blob Storage is object serverless storage. Stores very large files and large amounts of unstructured files. Pay for what you store, unlimited storage, no-resizing volumes, filesystem protocols.
	- Block blobs store text and binary data. Made up of blocks of data that can be managed individually. Store up to 4.75 TB data.
	- Append blobs are optimized for append operations. Ideal for logging data from VM.
	- Page blobs store random access files up to 8TB in size. Store virtual hard drive (VHD) files and serve as disks for Azure VM.
	- AzCopy is a cli utility to copy blobs or files to or from a storage account.
- Azure Disk Storage is a virtual volume. Choose SSD or HDD, encryption by default, attach volume to VMs.
- Azure File Storage is a shared volume that you can access and manage like a file server.
- Azure Queue Storage is a messaging queue, a data store for queuing and reliably delivering messages between applications.
- Azure Table Storage is a wide-column NoSQL db. A NoSQL store that hosts unstructured data independent of any schema.
- Azure Data Box Heavy is a rugged briefcase computer and storage designed to move TBs or PBs of data.
- Azure Archive Storage is a long term cold storage for when you need to hold onto files for years on the cheapest storage options.
- Azure Data Lake Storage is a centralized repo that allows you to store all your structured and unstructured data at any scale.
- Storage accounts types and their differences:
![[Pasted image 20260706134053.png]]
- There are 2 types of performance tiers:
	- Premium Performance is stored on SSD, optimized for low latency, higher throughput, higher Input/Output Operations per Sec (IOPS).
	- Standard Performance is stored on HDD, varied performance based on access tier, lower IOPS.
- There are 3 types of access tiers for Standard Storage: Cool, Hot and Archive
	- Hot is used for data that's accessed frequently. Highest storage cost, lowest access cost.
	- Cool is used for data that's infrequently accessed and stored for at least 30 days. Lower storage cost, higher access cost.
	- Archive is used for data that's rarely accessed and stored for at least 180 days. Lowest storage cost, highest access cost.
- Account Level Tiering: Any blob that doesn't have an explicitly assigned tier infers the tier from the storage account access tier setting.
- Blob Level Tiering: You can upload a blob to the tier of your choice. Changing tiers happens instantly with the exception from moving out of archive.
- Rehydrating a blob: When moving a blob out of archive into another tier it can take several hours known as rehydrating.
- Blob lifecycle management: You can create rule based policies to transition data to different tiers.
- Replication stores multiple copies of your data so that it is protected from unplanned events, transient hardware failures, network or power outages, massive natural disasters. The greater level of redundancy the more expensive the cost of replication.
	- Primary Region Redundancy is locally redundant storage (LRS) and zone redundant storage (ZRS). Data is replicated 3 times.
	- Secondary Region Redundancy is geo redundant storage (GRS) and geo zone redundant storage (GZRS). Replicates to a secondary region in case of primary regional disaster. Secondary region determined based on primary's pair region. Secondary region isn't available for read or write access except in event of failover.
	- Secondary Region Redundancy with Read Access is read access geo redundant storage (RA-GRS) and read access geo zone redundant storage (RA-GZRS). Copies data synchronously to both primary and secondary regions and secondary region has read access.

| **LRS**                                                                    | **ZRS**                                                   | **GRS**                                                                                       | **GZRS**                                                                                                   |
| -------------------------------------------------------------------------- | --------------------------------------------------------- | --------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| Copies Data synchronously in primary region.<br>It is the cheapest option. | Copies data synchronously across 3 AZs in primary region. | Copies data synchronously in primary region.<br>Copies data asynchronously to another region. | Copies data synchronously across 3 AZs in primary region.<br>Copies data asynchronously to another region. |
| 99.999999999% (11 nines) durability.                                       | 99.9999999999% (12 nines) durability.                     | 99.99999999999999% (16 nines) durability.                                                     | 99.999999999999999% (16 nines) durability.                                                                 |
| ![[Pasted image 20260707100615.png\|200]]                                  | ![[Pasted image 20260707100628.png\|200]]                 | ![[Pasted image 20260707102616.png\|300]]                                                     | ![[Pasted image 20260707102628.png\|300]]                                                                  |
## Azure Files
- Azure files is a fully managed file share in the cloud.
- A file share is a centralized server for storage that allows multiple connections.
- To connect to the file share a network protocol is used:
	- Server Message Block (SMB)
	- Network File System (NFS)
- When a connection is established, the file share's filesystem will be accessible in the specific directory within your own directory tree (known as mounting).
- Completely replace or supplement on-premise file servers Network Attach Storage (NAS) devices.
- Lift-and-Shift your on-premise storage to the cloud via Classic or Hybrid Lift. Lift-and-Shift means when you move workloads without rearchitecting, e.g. importing local VMs to the cloud.
	- Classic Lift is where both the app and its data are moved to Azure.
	- Hybrid Lift is where the app data is moved to Azure and the app continues to run on-premises.
- Azure File Sync is a service that allows you to cache Azure file shares on an on-premises Windows Server or cloud VM.
- You can use any protocol that's available on Windows Server to access your data locally, including SMB, NFS, FTPS.
- You can have as many caches as you need across the world.
---
# Microsoft Entra ID (Azure Active Directory)
- Azure AD or Microsoft Entra ID is Microsoft's cloud based identity and access management service which helps your employees sign in and access resources.
- Microsoft introduced Active Directory Domain Services (AD DS) in Windows 2000 to give orgs the ability to manage multiple on-premises infra components and systems using a single identity per user.
- Entra ID takes this approach to the next level by providing orgs with an Identity as a Service (IDaaS) solution for all their apps across cloud and on-premises.
- A **Domain** is an area of a network organized by a single authentication db. An AD domain is a logical grouping of AD objects on a network.
- A **Domain Controller (DC)** is a server that authenticates user identities and authorizes their access to resources.
- A **Domain Computer** is a computer registered with a central authentication db. It is an AD object.
- An **AD Object** is the basic element of AD such as users, groups, printers, computers, shared folders.
- **Group Policy Object (GPO)** is a virtual collection of policy settings. It controls what AD objects have access to.
- **Organization Units (OU)** is a subdivision within an AD into which you can place AD objects and other OUs.
- **Directory Service** such as AD DS provides the methods for storing directory data and making this data available to network users and admins. A DS runs on a DC.
## Single Sign-On (SSO) in Entra ID
- SSO is a feature that allows users to authenticate once with Azure AD and then access multiple apps and services without having to authenticate again.
- When a user signs into Entra ID with their credentials, Entra ID creates a security token that can be used to access other resources within the same org. This token can be used to authenticate the user to other cloud based or on-premises apps that have been integrated with Entra ID.
- SSO protocols supported in Azure:
	- OpenID Connect and OAuth is an identity layer built on top of OAuth 2.0. It allows for auth of users in a secure and standardized manner. Used for web and mobile apps.
	- Security Assertion Markup Language (SAML) is an XML based protocol used for exchanging auth data between an identity provider and a service provider. Used for federated auth scenarios.
	- Password based auth refers to the traditional username/password auth method where users provide their credentials directly to authenticate.
	- Linked authentication provides the ability to link multiple accounts from different identity providers to a single user identity. This allows users to authenticate using any of their linked accounts.
	- Integrated Windows Authentication (IWA) lets users access apps using their Windows domain creds, utilizing their current Windows session for auth.
	- Header based auth is the method in which app accepts an auth token in the form of a header in each request. The token in validated by the app to authenticate the user.
## Zero Trust Methodologies
- The zero trust model operates on the principle of *"Trust No One, Verify Everything"*.
- The 3 principles of Microsoft's Zero Trust Model:
	1. **Verify Explicitly:** Always authenticate and authorize based on all available data points.
	2. **Least Privileged Access:** Principle of Least Privilege (PoLP), limit user access with Just-In-Time (JIT) and Just-Enough-Access (JEA), risk based adaptive policies and data protection.
	3. **Assume Breach:** Minimize blast radius and segment access. Verify end-to-end encryption and use analytics to get visibility, drive threat detection and improve defenses.
- The 6 pillars of Microsoft's Zero Trust Model:
	1. Identities
	2. Endpoints (Devices)
	3. Apps
	4. Data
	5. Infrastructure
	6. Networks
## Key Vault
- Azure Key Vault helps you safeguard cryptographic keys and other secrets used by cloud apps and services.
- Secrets Management: Store and tightly control access to tokens, passwords, certificates, API keys, and other secrets.
- Key Management: Create and control the encryption keys used to encrypt your data.
- Certificate Management: Easily provision, manage, and deploy public and private SSL certificates for use with Azure and internal connected resources.
- Hardware Security Module: An HSM is a piece of hardware designed to store encryption keys. Secrets and keys can be protected either by software or FIPS 140-2 Level 2 validated HSMs.
  HSMs that are multi-tenant are FIPS 140-2 Compliant (multiple customers virtually isolated on an HSM).
  HSMs that are single-tenant are FIPS 140-3 Compliant (single customer on a dedicated HSM).
## Azure DDoS Protection
- DDoS (Distributed Denial of Service) Attack is a malicious attempt to disrupt normal traffic by flooding a website with large amounts of fake traffic.
- Volumetric Attacks:
	- Volume based attacks that flood the network with legitimate looking traffic.
	- Exhausts available bandwidth.
	- Legitimate uses cannot access website.
	- Measured in bits per second (bps).
- Protocol Attacks:
	- Exhausting server resources with false protocol requests that exploit weakness (UDP and TCP flooding on Layer 3 and Layer 4).
	- Measured in packages per second (psp).
- Application Layer Attacks:
	- Attacks that occur at the application layer (Layer 7).
	- HTTP floods, SQL injections, cross-site scripting (XSS), parameter tampering, Sowloris Attacks.
	- Web Application Firewalls (WAFs) are used as means of protection.
## Azure Firewall
- Azure Firewall is a managed, cloud based network security service that protects your Azure VNet resources.
- It is a fully stateful Firewall as a Service (FWaaS) with built in high availability and unrestricted cloud scalability.
- You can centrally create, enforce and log app and network connectivity policies across subscriptions and virtual networks.
- Azure Firewall uses a static public IP address for your VNet resources allowing outside firewalls to identify traffic originating from your virtual network. The service is fully integrated with Azure Monitor for logging and analytics.
- Azure Firewall is launched in its own VNet. Other VNets pass through this central VNet. It allows Microsoft Threat Intelligence, blocks known malicious IPs and FQDNs.
## Azure Application Gateway
- It's an application level routing and load balancing service.
- Operates on Layer 7 (Application Layer).
- Azure Web Application Firewall (WAF) policies can be attached to an Application Gateway to provide additional security.
- Configuring frontend:
	- Private IP will create an Internal Load Balancer.
	- Public IP will create an External Load Balancer.
- Configuring backend:
	- Need to create backend pools.
	- A backend pool is a collection of resources to which your app gateway can send traffic.
	- A backend pool can contain:
		- VMs
		- VM scale sets
		- IP addresses
		- Domain names
		- App Service
- Configuring routing rules:
	- Listeners list incoming traffic and pass it to rules.
	- Rules say who should we pass data to.
	- HTTP/S settings say how we should handle HTTP requests.
### Routing Rules
- 