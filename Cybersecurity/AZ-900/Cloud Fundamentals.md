| Dedicated Server                                     | Virtual Private Server                                                                                      | Shared Hosting                                          | Cloud Hosting                                                    |
| ---------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------------------------------- | ---------------------------------------------------------------- |
| One physical machine dedicated to a single business. | One physical machine dedicated to a single business. The physical machine is virtualized into sub-machines. | One physical machine shared by hundreds of businesses.  | Multiple physical machines that act as one system.               |
| Runs a single web-app/site.                          | Runs multiple web-apps/sites.                                                                               | Relies on most tenants under-utilizing their resources. | The system is abstracted into multiple cloud services.           |
| Very expensive, high maintenance, high security.     | Better utilization and isolation of resources.                                                              | Very cheap, limited functionality, poor isolation.      | Flexible, scalable, secure, cost-effective, high configurability |
The most common types of cloud services for Infrastructure as a Service (IaaS) are **compute**, **networking**, **storage**, and **databases**.
![[Pasted image 20260521024853.png|713]]

| **Public Cloud**                          | **Private Cloud**                                                    | **Hybrid**                                         |
| ----------------------------------------- | -------------------------------------------------------------------- | -------------------------------------------------- |
| Everything built on the cloud provider.   | Everything built on company's datacenters, also known as on-premise. | Using both on-premise and a cloud service provider |
| ![[Pasted image 20260521025437.png\|338]] | ![[Pasted image 20260521025453.png\|357]]                            | ![[Pasted image 20260521025512.png\|676]]          |

![[Pasted image 20260521025118.png|765]]
##### Business Continuity Plan (BCP)
BCP is a document which outlines how a business will continue operating during an unplanned disruption in services.
###### Recovery Point Objective (RPO)
The maximum acceptable amount of data loss after an unplanned data loss incident, expressed as an amount of time. Answers the question, how much data are you willing to lose?
###### Recovery Time Objective (RTO)
The maximum amount of downtime your business can tolerate without incurring a significant financial loss. Answers the question, how much time are you willing to go down?
![[Pasted image 20260521135608.png]]
### Disaster Recovery Options

| **Backup & Restore**                           | **Pilot Light**                                                     | **Warm Standby**                                              | **Multi-Site Active/Active**                                |
| ---------------------------------------------- | ------------------------------------------------------------------- | ------------------------------------------------------------- | ----------------------------------------------------------- |
| RPO/RTO time in order of Hours.                | RPO/RTO time in order of 10 min.                                    | RPO/RTO time in order of min.                                 | RPO/RTO in real-time.                                       |
| Backup data and restore it to infrastructure.  | Data is replicated to another region with minimal services running. | Scaled down copy if infrastructure running ready to scale up. | Scaled up copy of infrastructure running in another region. |
| Low priority use cases.                        | Less stringent RPO/RTO. Core services.                              | Business critical services.                                   | Zero downtime. Mission critical services.                   |
| Restore data and deploy resources after event. | Start and scale resources after event.                              | Scale resources after event.                                  | Automatic failover.                                         |
| Cost \$                                        | Cost \$\$                                                           | Cost \$\$\$                                                   | Cost \$\$\$\$                                               |
##### Dedicated Server
- A physical server wholly utilized by a single customer.
- Need to guess the capacity and you will overpay for an underutilized server.
- Upgrading beyond your capacity will be slow and expensive.
- You are limited by your OS.
- Multiple apps can result in conflicts in resource sharing.
- Have a guarantee of security, privacy and full utility of underlying resources.
![[Pasted image 20260521141539.png|400]]
##### Virtual Machine (VM)
- Can run multiple VMs on one machine. Hypervisor is the software layer that lets run the VMs.
- A physical server shared by multiple customers.
- You pay for a fraction of the server and will overpay for an underutilized VM.
- You are limited by your Guest OS.
- Multiple apps on a single VM can result in conflicts in resource sharing.
![[Pasted image 20260521141844.png|400]]
##### Containers
- VM running multiple containers. Docker Daemon is the name of the software layer that lets you run multiple containers.
- You can maximally utilize the available capacity which is more cost effective.
- Containers share the same underlying OS so containers are more efficient than multiple VMs.
- Multiple apps can be run side by side without being limited to the same OS requirements and will not cause conflicts during resource sharing.
![[Pasted image 20260521180409.png|400]]
##### Functions
- Known as serverless compute.
- You upload code and choose the amount of memory and duration.
- Customer is only responsible for code and data.
- Very cost effective, only pay for the time your code is running, VM's only run when there is code to be executed.
- Cold-starts is a side effect.
![[Pasted image 20260521180901.png|500]]