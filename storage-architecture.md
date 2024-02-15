---
copyright:
  years: 2023
lastupdated: "2024-02-08"

subcollection: <vmware-cross-region-dr>

keywords:
---
{{site.data.keyword.attribute-definition-list}}

# Architecture decisions for storage

{: \#storage-decisions}

The following are storage architecture decisions for the VMware Disaster Recovery on IBM Cloud pattern.

| **Architecture decision** | **Requirement**                           | **Option**                                                                                                          | **Decision**      | **Rationale**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| ------------------------------- | ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- | ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Backup Storage                  | Provide storage for the Veeam backup repository | Direct Attached Storage (if Bare Metal Server) iSCSI (if VM or VSI veeam deployment), vmdk file (if manually deployed VM) | Direct Attached Storage | A backup repository is not required for continuous data protection, however, it is required to store metadata for the Veeam VMware Backup proxy in the protected site. It is recommended that this repository is placed on the protected site. In this pattern, a Veeam backup repository, created as a VM using a vmdk as the local repository is sufficient for this purpose. If the pattern has been extended for use with backup as well as replication then use one of the repositories used for backup on the protected site |

{: caption="Table 1. Architecture decisions for storage" caption-side="bottom"}