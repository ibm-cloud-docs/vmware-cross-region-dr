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

This document expands on the network aspect of the IBM Architecture framework in respect of the Veeam for disaster recovery for VMware workloads pattern.

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

When deployed the Veeam bare metal server is connected to the IBM Cloud primary private VLAN only, with no public connectivity by default. If you need to connect to the IBM Cloud secondary private VLAN, then you will need to trunk this VLAN to the host. Additionally static routes will need to be added for efficient routing

Each Veeam bare metal server is deployed with dual private only 10 GbE NICs with standard NIC teaming.

## Veeam bare metal server to VCS connectivity

After deployement, the Veeam bare metal server is connected to 2 IBM Cloud private VLANs already existing in the VCS environment:

- ESXi/vCenter management and NSX TEPs VLAN
- vSAN/NFS, vMotion, NSX Edge TEPs VLAN

More information regarding how the Veeam bare metal server is connected to these VLANs is available here:
[https://cloud.ibm.com/docs/vmwaresolutions?topic=vmwaresolutions-veeam-bms-archi-physical\#veeam-bms-archi-physical-network](https://cloud.ibm.com/docs/vmwaresolutions?topic=vmwaresolutions-veeam-bms-archi-physical#veeam-bms-archi-physical-network)

## Cross-region connectivity

In this pattern, the connectivity between the production and DR environments is provided by leveraging the IBM Cloud backbone, with no extra component or configuration needed and no cost associated to the traffic.

The Veeam Backup & Replication server, Veeam VMware Backup and CDP proxies connectivity requirement is met by connecting these components to IBM Cloud Private portable subnets rather than to NSX overlay segments.

To ensure efficient and performant replication, replication traffic should bypass the firewall by utilizing the IBM Cloud secondary private VLAN.

## Optional connectivity

The optional internet connectivity can be achieved by configuring a SNAT rule on the the vSRX or Fortigate gateway appliances along with an outbound HTTPS policy. A reverse proxy can also be used.

For IBM Cloud Object Storage access, for use as either a backup repository or configuration backup repository can be achieved through the use of a private endpoint.

If a Veeam scale-out backup repository is needed (not required for the replication), the ideal candidate is IBM Cloud Object Storage (COS). Access to COS can be achieved through the use of a private endpoint.

If IBM Cloud Object Storage is accessed via an IBM Cloud service private endpoint, a public internet connectivity (direct or through a proxy and gateway) is needed for the validation of the COS TLS certificate by Veeam. Therefore, internet connectivity becomes mandatory.
{: note}
