---
copyright:
  years: 2023
lastupdated: "2024-02-23"

subcollection: vmware-cross-region-dr

keywords:
---

{{site.data.keyword.attribute-definition-list}}

# Architecture decisions for compute
{: #compute-decisions}

The following are compute architecture decisions for the VMware Disaster Recovery with Veeam pattern.

| **Architecture decision**                            | **Requirement**                                                     | **Options**                                                                                     | **Decision**                                      | **Rationale**                                                                                                                                                                                                                                                                                                              |
|------------------------------------------------------|---------------------------------------------------------------------|-------------------------------------------------------------------------------------------------|---------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Veeam all-in-one solution deployment site            | Select the most appropriate deployment site to maximize performance | Protected site, recovery site                                                                   | **Recovery site**                                 | The location of the Veeam Backup and Replication server for replication scenarios is not based on performance but on availability for recovery. By placing the Veeam Backup and Replication server at the recovery site means that it is available to recover when needed, therefore, lowering the recovery time objective. |
| Veeam solution deployment type on the recovery site  | Provide an all-in-one automated Veeam deployment                    | {{site.data.keyword.baremetal_long}} VSI Virtual machine (VM) hosted on the VMware environment                             | Virtual machine                                   | Use ESX DRS and HA, hence the logical sense to use VMware VM as a hosting environment                                                                                                                                                                                                                                       |
| Veeam solution deployment type on the protected site | Protect VMs on the protected site with replication                  | {{site.data.keyword.baremetal_short_sing}}, IBM Cloud VSI, VM hosted on the VMware environment. No all-in-one deployment | No all-in-one deployment (Veeam Proxies)   | Veeam best practice for replication is to replace the Veeam Backup and replication server at the recovery site.                                                                                                                                                                                                              |
| Veeam VMware Backup and Continuous Data Protection (CDP) proxies deployment type      | Host the necessary Veeam proxies                                    | {{site.data.keyword.baremetal_short_sing}}, IBM Cloud VSI, VM hosted on the VMware environment                           | VM hosted on the VMware environment               | Adopting the strategy of one VM proxy per host is preferred for Network File System (NFS) storage and vSAN deployments.                                                                                                                                                                                                                                  |
| Initial number of Veeam VMware proxies               | Provide sufficient level of resiliency and performance              | One Veeam VMware Backup and CDP proxy per site, Multiple Veeam VMware Backup and CDP proxies per site     | Multiple Veeam VMware Backup and CDP proxies per site | Provide redundancy to avoid replication tasks to be blocked when one of the backup and CDP proxies becomes unavailable. Veeam advise when using virtual machine proxies and NFS v3 or vSAN to use a proxy per host and use VM-Host affinity rules.                                                                             |

{: caption="Table 1. Architecture decisions for compute" caption-side="bottom"}

These architecture decisions are addressing pure disaster recovery use case scenarios. For backup use cases the deployment requirements can be different, such as have a separate dedicated Veeam backup environment. This pattern don’t address the backup design and solution aspect
