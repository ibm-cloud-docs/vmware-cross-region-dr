---
copyright:
  years: 2023, 2024
lastupdated: "2024-02-29"

subcollection: vmware-cross-region-dr

keywords:
---

{{site.data.keyword.attribute-definition-list}}

# Architecture decisions for networking

{: #networking-architecture}

The following tables summarize the networking architecture decisions for the Veeam pattern.

| Architecture decision                       | Requirement                                                                                  | Option                                                                                        | Decision          | Rationale                                                                                                                                                                                                                                                                                                                                                                                                                           |
|-------------------------------------------------|--------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Veeam VMware backup and Continuous Data Protection (CDP) proxies VLAN assignment | The Veeam VMware backup and CDP proxies need to be connected to a subnet on one of the private VLANs | Primary VLAN subnet, Secondary VLAN subnet, NSX Overlay segment that connects to primary VLAN subnet | Secondary VLAN subnet | As this pattern uses a gateway cluster that protects subnets on the primary VLAN, a Secondary VLAN subnet that bypasses the gateway cluster should be used for the Veeam VMware backup and CDP proxies to maximize throughput. Traffic on the subnets on the primary VLAN and NSX overlay segments traverse the gateway cluster, reducing throughput. The Secondary VLAN's purpose is for storage traffic, including replication. |
| Veeam VMware backup and CDP proxies subnet type     | The Veeam VMware backup and CDP proxies need to be connected to a subnet on the secondary VLAN       | Existing subnet, New subnet                                                                       | New subnet            | A new portable subnet on the secondary VLAN should be ordered. The IP address schemas on the other subnets are managed by {{site.data.keyword.Bluemix_notm}} and customers should not directly provision virtual machines onto these subnets.                                                                                                                                                                                                                     |

{: caption="Table 1. Architecture decisions for networking" caption-side="bottom"}
