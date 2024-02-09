---
copyright:
  years: 2023
lastupdated: "2024-2-6"

subcollection: vmware-cross-region-dr

keywords:
---
{{site.data.keyword.attribute-definition-list}}

# Network design

{: \#network-design}

This section covers Network design requirements and considerations.

1. **Firewall Configuration:**
   1. **Ports and Protocols:** Ensure that the necessary ports and protocols are open in the firewalls between Veeam components, VMware infrastructure, and any other relevant systems. Veeam provides documentation specifying the required ports for different components and functionalities.
2. **Network Segmentation:**
   1. **Isolation:** Segment the network to isolate Veeam components from other critical infrastructure. This can enhance security and prevent unauthorized access.
3. **Backup Repository Connectivity:**
   1. **Direct Access:** Ensure that Veeam backup repositories are directly accessible by Veeam components over the network. This is crucial for efficient data transfer and storage operations.
4. **Proxy Deployment:**
   1. **Strategic Proxy Placement:** Deploy Veeam proxies strategically within the network to optimize data transfer. Proxies can be placed closer to the data source to reduce the load on the central Veeam server.
5. **VMware vSphere Connectivity:**
   1. **vCenter and ESXi Host Connectivity:** Establish reliable connections to VMware vCenter and ESXi hosts to facilitate the discovery of VMs, configuration settings retrieval, and other interactions between Veeam and VMware.
6. **Secure Communication:**
   1. **TLS/SSL Encryption:** Enable TLS/SSL encryption for communication between Veeam components to ensure the confidentiality and integrity of data in transit.
   2. **Secure Communication with VMware:** Use secure communication protocols when interacting with VMware infrastructure, adhering to best practices for securing VMware environments.
7. **DNS Configuration:**
   1. **Name Resolution:** Ensure proper DNS configuration for Veeam components and VMware infrastructure to enable seamless name resolution. This is essential for the identification and communication between components.
8. **Time Synchronization:**
   1. **NTP (Network Time Protocol):** Synchronize the clocks across Veeam components, VMware hosts, and other relevant systems using NTP to ensure accurate timestamps for logs and data consistency.
9. **Redundancy and High Availability:**
   1. **Redundant Network Paths:** Configure redundant network paths to provide high availability and fault tolerance, ensuring that a failure in one path does not disrupt data transfer operations.

When deployed the Veeam bare metal server on the production site is connected to two IBM Cloud private VLANs, with no public connectivity by default.

Each Veeam® bare metal server is deployed with dual private only 10 GbE NICs with standard NIC teaming.

The Veeam bare metal servers are deployed to have connectivity to the following infrastructure VLANs:

Table 1. VLAN designations

| VLAN   | Designation Traffic type                            |
| ------ | --------------------------------------------------- |
| VLAN 1 | Private A ESXi management, management, Geneve (TEP) |
| VLAN 2 | Private B vSAN, NFS, vMotion, and Edge Geneve (TEP) |

The Veeam bare metal servers are natively provisioned to VLAN 1, and they need one IP address from the private primary subnet of VLAN 1 for the all-in-one server, which is deployed and configured during server provisioning of the non-tagged VLAN 1.

Each Veeam bare metal server also needs to be VLAN trunked to VLAN 2. They need one IP address from the private portable subnet of the VLAN 2 for the all-in-one server from the NFS or vSAN subnet, which is configured through the automation on the tagged VLAN 2.

The Veeam backup server must have network connectivity to:

\- all the backup/CDP proxies, including the ones located on the target site

\- all the ESXi hosts where VMs/replicas are/will be running

\- the vCenter servers in the production and DR environments

\- Optional – Internet connectivity

\- Optional – Connectivity to IBM Cloud Object Storage

In this pattern, connectivity between the production and DR environments is achieved by leveraging the IBM Cloud backbone, with no extra component needed and no cost associated to the traffic.

The Veeam backup server connectivity requirements are met by keeping all the backup/CDP proxies connected to an IBM Cloud Private VLAN.

The optional internet connectivity, which primary use case is to connect to Veeam support for updates, can be achieved by setting up a proxy or NAT connection to the public network.

If a Veeam scale-out backup repository is needed (not required for the replication), the ideal candidate is IBM Cloud Object Storage (COS). Access to COS can be achieved through the use of a private endpoint.

**Is a service endpoint sufficient or do we really need a proxy as mentioned in IBM Cloud documentation?:**

“If you want to access IBM Cloud Object Storage by using Veeam on a private-only vCenter Server instance, you must configure Veeam to access the internet through a proxy server. This way, Veeam can verify the TLS certificate that is used by IBM Cloud Object Storage.”

**Add bring up aspects to recover networking**
