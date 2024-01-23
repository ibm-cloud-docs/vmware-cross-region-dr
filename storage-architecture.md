---
copyright:
  years: 2023
lastupdated: "2023-12-26"

subcollection: vmware-cross-region-dr

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Architecture decisions for storage

{: \#storage-decisions}

The following are storage architecture decisions for the VMware Disaster Recovery on IBM Cloud pattern.

| **Architecture decision** | **Requirement**                                           | **Option**                                                                           | **Decision**            | **Rationale**                                                                                                         |
|---------------------------|-----------------------------------------------------------|--------------------------------------------------------------------------------------|-------------------------|-----------------------------------------------------------------------------------------------------------------------|
| Backup Storage            | Provide storage for the Veeam Backup storage repository   | Direct Attached Storage (if Bare Metal Server) iSCSI (if VM or VSI veeam deployment) | Direct Attached Storage | Direct Attached Storage provided by the all-in-one Veeam bare metal deployment. Provides the best backup performances |
| Scale-out backup storage  | Provide storage for the Veeam Scale-out backup repository | COS                                                                                  | COS                     | Can be directly accessed via service endpoint, scalable, multiple tiers                                               |

\---

{: caption="Table 1. Architecture decisions for storage" caption-side="bottom"}
