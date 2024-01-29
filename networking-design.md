---
copyright:
  years: 2023
lastupdated: "2023-12-26"

subcollection: vmware-cross-region-dr

keywords:
---

{{site.data.keyword.attribute-definition-list}}

# Network design

{: \#network-design}

## Requirements

The Veeam backup server must have network connectivity to:

-   all the backup/CDP proxies, including the ones located on the disaster recovery site
-   all the ESXi hosts where VMs/replicas are/will be running
-   the vCenter servers in the production and DR environments
-   Optional – Internet connectivity
-   Optional – Connectivity to IBM Cloud Object Storage

## Veeam Networking Design considerations

Here are some key areas that you must keep in mind while designing Veeam backup and replication for Disaster recovery environment on IBM Cloud.

| **Area**                              | **Description**                                                                                                                                                                                                                                                                                                                                 |
|---------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Firewall Configuration:**           | **Ports and Protocols:** Ensure that the necessary ports and protocols are open in the firewalls between Veeam components, VMware infrastructure, and any other relevant systems. Veeam provides documentation specifying the required ports for different components and functionalities.                                                      |
| **Network Segmentation:**             | **Isolation**: Segment the network to isolate Veeam components from other critical infrastructure. This can enhance security and prevent unauthorized access.                                                                                                                                                                                   |
| **Backup Repository Connectivity:**   | **Direct Access:** Ensure that Veeam backup repositories are directly accessible by Veeam components over the network. This is crucial for efficient data transfer and storage operations.                                                                                                                                                      |
| **Proxy Deployment:**                 | **Strategic Proxy Placement:** Deploy Veeam proxies strategically within the network to optimize data transfer. Proxies can be placed closer to the data source to reduce the load on the central Veeam server.                                                                                                                                 |
| **VMware vSphere Connectivity:**      | **vCenter and ESXi Host Connectivity:** Establish reliable connections to VMware vCenter and ESXi hosts to facilitate the discovery of VMs, configuration settings retrieval, and other interactions between Veeam and VMware.                                                                                                                  |
| **Secure Communication:**             | **TLS/SSL Encryption:** Enable TLS/SSL encryption for communication between Veeam components to ensure the confidentiality and integrity of data in transit. **Secure Communication with VMware:** Use secure communication protocols when interacting with VMware infrastructure, adhering to best practices for securing VMware environments. |
| **DNS Configuration:**                | **Name Resolution:** Ensure proper DNS configuration for Veeam components and VMware infrastructure to enable seamless name resolution. This is essential for the identification and communication between components                                                                                                                           |
| **Time Synchronization:**             | **NTP (Network Time Protocol):** Synchronize the clocks across Veeam components, VMware hosts, and other relevant systems using NTP to ensure accurate timestamps for logs and data consistency.                                                                                                                                                |
| **Redundancy and High Availability:** | **Redundant Network Paths:** Configure redundant network paths to provide high availability and fault tolerance, ensuring that a failure in one path does not disrupt data transfer operations.                                                                                                                                                 |

When deployed the Veeam bare metal server on the production site is connected to two IBM Cloud private VLANs, with no public connectivity by default.

Each Veeam® bare metal server is deployed with dual private only 10 GbE NICs with standard NIC teaming.

## Veeam bare metal server to VCS connectivity

After deployement, the Veeam bare metal server is connected to 2 IBM Cloud private VLANs already existing in the VCS environment:

-   ESXi/vCenter management and NSX TEPs VLAN
-   vSAN/NFS, vMotion, NSX Edge TEPs VLAN

More information regarding how the Veeam bare metal server is connected to these VLANs is available here:  
[https://cloud.ibm.com/docs/vmwaresolutions?topic=vmwaresolutions-veeam-bms-archi-physical\#veeam-bms-archi-physical-network](https://cloud.ibm.com/docs/vmwaresolutions?topic=vmwaresolutions-veeam-bms-archi-physical#veeam-bms-archi-physical-network)

## Cross-region connectivity

In this pattern, the connectivity between the production and DR environments is provided by leveraging the IBM Cloud backbone, with no extra component or configuration needed and no cost associated to the traffic.

The Veeam backup server backup/CDP proxies connectivity requirement is met by keeping all the backup/CDP proxies connected to an IBM Cloud Private VLAN (as we are NOT deploying the backup/CDP proxies as virtual machines running on VCS).

## Optional connectivity

The optional internet connectivity, which primary use cases are to connect to Veeam support for updates and to enable automatic Veeam license update can be achieved by setting up a proxy or NAT connection (for instance by using one of the vSRX or Fortigate gateway appliances available from IBM Cloud catalog) to internet.

If a Veeam scale-out backup repository is needed (not required for the replication), the ideal candidate is IBM Cloud Object Storage (COS). Access to COS can be achieved through the use of a private endpoint.

@Note: that even if COS is accessed via an IBM Cloud service endpoint, a public internet connectivity (direct or through a proxy and gateway) is needed for the validation of the COS TLS certificate by Veeam.

Therefore, if COS is used as a Veeam scale-out backup repository, internet connectivity becomes mandatory.
