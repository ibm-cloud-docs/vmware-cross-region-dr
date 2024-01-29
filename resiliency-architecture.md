---
copyright:
  years: 2023
lastupdated: "2023-12-18"

subcollection: 

keywords:

---

# Architecture decisions for resiliency

{: \#resiliency-architecture}

The following sections summarize the Veeam Disaster Recovery on IBM Cloud for VMware architecture.

## Architecture decisions for high Resilency

{: \#high-availability}

| **Architecture decision**                                                         | **Requirement**                                                                                          | **Option**                                                                                                                                                                                                                                                                                                         | **Decision**                                                                                 | **Rationale**                                                                                                                                                                                                                                                                          |
|-----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Replication type between the IBM Cloud regions                                    | Replicate the VM data between the IBM regions so that the VMs could be recovered.                        | Standard Veeam replication Veeam Continuous Data Protection (CDP)                                                                                                                                                                                                                                                  | Dependent on the RPO/RTO target                                                              | If RPO in minutes, CDP, otherwise standard Veeam replication                                                                                                                                                                                                                           |
| Veeam all-in-one solution deployment site disaster recovery instance (resiliency) | Provide a way to recover the functions of the Veeam all-in-one bare metal in case it becomes unavailable | “Standby” Veeam bare metal in the DR site with Veeam components installed on it but without any configuration “Standby” Veeam VSI in the DR site with Veeam components installed on it but not configured Provision a Veeam Bare Metal at DR site at failure time Provision a Veeam VSI at DR site at failure time | “Standby” Veeam VSI in the DR site with Veeam components installed on it but not configured. | This second Veeam all-in-one instance is only needed when the main all-in-one bare metal becomes unavailable. Allows to optimize costs while keeping the networking and recovery operations as simple as possible (only need to import the backed up configuration) and to improve RTO |
| High availability deployment of Veeam                                             | Ensure availability of resources if outages occur. Support SLA targets for availability.                 | If Primary Veeam is deployed as Bare Metal, HA can be a VSI instance. Its required to migrate all configs to VSI for recovery purposes.                                                                                                                                                                            | Standard Veeam deployment on IBM Cloud                                                       | ?? Comments welcome                                                                                                                                                                                                                                                                    |

{: caption="Table 1. High availability architecture decisions" caption-side="bottom"}
