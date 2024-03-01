---
copyright:
  years: 2023, 2024
lastupdated: "2024-02-28"

subcollection: <vmware-cross-region-dr>

keywords:
---
{{site.data.keyword.attribute-definition-list}}

# Architecture decisions for storage
{: #storage-decisions}

The following are storage architecture decisions for the Veeam for disaster recovery for VMware workloads pattern.

| **Architecture decision** | **Requirement**                           | **Option**                                                                                                          | **Decision**      | **Rationale**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| ------------------------------- | ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- | ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Backup Storage                  | Provide storage for the Veeam backup repository | Direct attached storage, if Bare Metal Server. iSCSI, if VM or VSI Veeam deployment. vmdk file, if manually deployed VM. | Direct attached storage | However, a backup repository is not required for continuous data protection it is required to store metadata for the Veeam VMware Backup proxy in the protected site. It is recommended that this repository is placed on the protected site. In this pattern, a Veeam backup repository, created as a VM by using a vmdk as the local repository is sufficient for this purpose. If the pattern has been extended for use with backup and replication, then use one of the repositories that are used for backup on the protected site. |
{: caption="Table 1. Architecture decisions for storage" caption-side="bottom"}