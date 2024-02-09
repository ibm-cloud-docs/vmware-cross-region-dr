---
copyright:
  years: 2023
lastupdated: "2023-12-18"

subcollection: vmware-cross-region-dr

keywords:
---

{{site.data.keyword.attribute-definition-list}}

# Architecture decisions for networking

{: \#networking-architecture}

The following tables summarize the networking architecture decisions for the Veeam pattern.

| **Architecture decision**                       | **Requirement**                                                                                  | **Option**                                                                                        | **Decision**          | **Rationale**                                                                                                                                                                                                                                                                                                                                                                                                                           |
|-------------------------------------------------|--------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Veeam VMware Backup/CDP proxies VLAN assignment | The Veeam VMware Backup/CDP proxies need to be connected to a subnet on one of the private VLANs | Primary VLAN subnet, Secondary VLAN subnet, NSX Overlay segment (connects to primary VLAN subnet) | Secondary VLAN subnet | As this pattern uses a gateway cluster that protects subnets on the Primary VLAN, then a Secondary VLAN subnet that bypasses the gateway cluster should be used for the Veeam VMware Backup/CDP proxies to maximize throughput. Traffic on the subnets on the Primary VLAN and NSX overlay segments will traverse the gateway cluster, reducing throughput. The Secondary VLAN's purpose is for storage traffic, including replication. |
| Veeam VMware Backup/CDP proxies subnet type     | The Veeam VMware Backup/CDP proxies need to be connected to a subnet on the secondary VLAN       | Existing subnet, New subnet                                                                       | New subnet            | A new portable subnet on the Seconday VLAN should be ordered. The IP address schemas on the other subnets are managed by IBM Cloud and customers should not directly provision virtual machines onto these subnets.                                                                                                                                                                                                                     |

{: caption="Table 1. Architecture decisions for networking" caption-side="bottom"}
