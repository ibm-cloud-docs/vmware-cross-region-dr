---
copyright:
  years: 2023, 2024
lastupdated: "2024-02-29"

subcollection: vmware-cross-region-dr

keywords:
---
{{site.data.keyword.attribute-definition-list}}

# Network design
{: #network-design}

This pattern expands on the network aspect for the Veeam for disaster recovery for VMware workloads pattern.

## Requirements
{: #network-requirements}

The requirements for the network aspect for the Veeam for disaster recovery are as follows:

- The Veeam Backup and Replication server must have network connectivity to:
   - All the Veeam VMware Backup and Continuous Data Protection (CDP) proxies.
   - All the ESXi hosts where VMs and replicas are running.
   - The vCenter appliances in the protected and recovery sites.
   - Optional: Internet connectivity for license renewals, software updates, and certificate chain validation for object storage.
   - Optional: Connectivity to {{site.data.keyword.cos_full_notm}}.
- The Veeam VMware Backup and CDP proxies must communicate with their peers at the remote sites.
- The Veeam VMware Backup and CDP proxies must communicate with the ESXi hosts at their site.

## Veeam networking design considerations
{: #networking-design-consider}

Keep in mind the following key areas when you design Veeam backup and replication for Disaster recovery environment on {{site.data.keyword.Bluemix_notm}}.

| Area                              | Description                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Firewall configuration:**           | **Ports and protocols:** Ensure that the necessary ports and protocols are open in the firewalls between Veeam components, VMware infrastructure, and any other relevant systems. Veeam provides documentation specifying the required ports for different components and functions.                                                            |
| **Network segmentation:**             | **Isolation**: Segment the network to isolate Veeam components from other critical infrastructure. This can enhance security and prevent unauthorized access.                                                                                                                                                                                         |
| **Backup repository connectivity:**   | **Direct access:** Ensure that Veeam backup repositories are directly accessible by Veeam components over the network. This is crucial for efficient data transfer and storage operations.                                                                                                                                                            |
| **Proxy deployment:**                 | **Strategic proxy placement:** Deploy Veeam proxies strategically within the network to optimize data transfer. Proxies can be placed closer to the data source to reduce the load on the central Veeam server.                                                                                                                                       |
| **VMware vSphere connectivity:**      | **vCenter and ESXi host connectivity:** Establish reliable connections to VMware vCenter and ESXi hosts to facilitate the discovery of VMs, configuration settings retrieval, and other interactions between Veeam and VMware.                                                                                                                        |
| **Secure communication:**             | **TLS/SSL encryption:** Enable TLS/SSL encryption for communication between Veeam components to ensure the confidentiality and integrity of data in transit. **Secure communication with VMware:** Use secure communication protocols when you interact with VMware infrastructure, adhering to best practices for securing VMware environments. |
| **DNS configuration:**                | **Name resolution:** Ensure proper DNS configuration for Veeam components and VMware infrastructure to enable seamless name resolution. This is essential for the identification and communication between components                                                                                                                                 |
| **Time synchronization:**             | **NTP (Network Time Protocol):** Synchronize the clocks across Veeam components, VMware hosts, and other relevant systems by using NTP to verify accurate timestamps for logs and data consistency.                                                                                                                                                      |
| **Redundancy and high availability:** | **Redundant network paths:** Configure redundant network paths to provide high availability and fault tolerance, verify that a failure in one path does not disrupt data transfer operations.                                                                                                                                                       |
{: caption="Networking design decisions" caption-side="bottom"}
