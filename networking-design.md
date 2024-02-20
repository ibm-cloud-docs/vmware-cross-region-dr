---
copyright:
  years: 2023
lastupdated: "2024-2-01"

subcollection: vmware-cross-region-dr

keywords:
---
{{site.data.keyword.attribute-definition-list}}

# Network design

{: \#network-design}

This pattern expands on the network aspect of the IBM Architecture framework in respect of the Veeam for disaster recovery for VMware workloads pattern.

## Requirements

The requirements for the network aspect for the Veeam for disaster recovery are as follows:

- The Veeam Backup & Replication server must have network connectivity to:
  - All the Veeam VMware Backup and CDP proxies.
  - All the ESXi hosts where VMs/replicas are/will be running.
  - The vCenter appliances in the protected and recovery sites.
  - Optionally, Internet connectivity for; license renewals, software updates and certificate chain validation for object storage.
  - Optionally, connectivity to IBM Cloud Object Storage.
- The Veeam VMware Backup and CDP proxies must communicate with their peers at the remote sites.
- The Veeam VMware Backup and CDP proxies must communicate with the ESXi hosts located at their site.

## Veeam networking design considerations

Here are some key areas that you must keep in mind while designing Veeam backup and replication for Disaster recovery environment on IBM Cloud.

| **Area**                              | **Description**                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Firewall Configuration:**           | **Ports and Protocols:** Ensure that the necessary ports and protocols are open in the firewalls between Veeam components, VMware infrastructure, and any other relevant systems. Veeam provides documentation specifying the required ports for different components and functionalities.                                                            |
| **Network Segmentation:**             | **Isolation**: Segment the network to isolate Veeam components from other critical infrastructure. This can enhance security and prevent unauthorized access.                                                                                                                                                                                         |
| **Backup Repository Connectivity:**   | **Direct Access:** Ensure that Veeam backup repositories are directly accessible by Veeam components over the network. This is crucial for efficient data transfer and storage operations.                                                                                                                                                            |
| **Proxy Deployment:**                 | **Strategic Proxy Placement:** Deploy Veeam proxies strategically within the network to optimize data transfer. Proxies can be placed closer to the data source to reduce the load on the central Veeam server.                                                                                                                                       |
| **VMware vSphere Connectivity:**      | **vCenter and ESXi Host Connectivity:** Establish reliable connections to VMware vCenter and ESXi hosts to facilitate the discovery of VMs, configuration settings retrieval, and other interactions between Veeam and VMware.                                                                                                                        |
| **Secure Communication:**             | **TLS/SSL Encryption:** Enable TLS/SSL encryption for communication between Veeam components to ensure the confidentiality and integrity of data in transit. **Secure Communication with VMware:** Use secure communication protocols when interacting with VMware infrastructure, adhering to best practices for securing VMware environments. |
| **DNS Configuration:**                | **Name Resolution:** Ensure proper DNS configuration for Veeam components and VMware infrastructure to enable seamless name resolution. This is essential for the identification and communication between components                                                                                                                                 |
| **Time Synchronization:**             | **NTP (Network Time Protocol):** Synchronize the clocks across Veeam components, VMware hosts, and other relevant systems using NTP to ensure accurate timestamps for logs and data consistency.                                                                                                                                                      |
| **Redundancy and High Availability:** | **Redundant Network Paths:** Configure redundant network paths to provide high availability and fault tolerance, ensuring that a failure in one path does not disrupt data transfer operations.                                                                                                                                                       |
