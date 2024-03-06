---
copyright:
  years: 2023
lastupdated: "2023-12-18"

subcollection: 

keywords:
---

# Architecture decisions for replication

{: #resiliency-architecture}

The following are replication architecture decisions for the VMware Disaster Recovery using Veeam pattern.

| Architecture decision  | Requirement                                                                       | Option                                                            | Decision                        | Rationale                                                                                                                                                                                                              |
|------------------------|-----------------------------------------------------------------------------------|-------------------------------------------------------------------|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Replication type       | Replicate the VM data between the IBM regions so that the VMs could be recovered. | Standard Veeam replication Veeam Continuous Data Protection (CDP) | Dependent on the RPO/RTO target | If RPO in minutes, CDP, otherwise standard Veeam replication                                                                                                                                                           |
| Recovery site location | Replicate the virtual machines to another IBM location.                           | Availability zone in the same region, different region            | Different region                | While it is unlikely that two availability zones suffer an outage at the same time, there may be some risk that a client considers to be unacceptable. therefore, select another IBM region for the recovery location. |

{: caption="Table 1. Replication decisions" caption-side="bottom"}
