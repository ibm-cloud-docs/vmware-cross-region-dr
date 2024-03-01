---
copyright:
  years: 2023
lastupdated: "2024-02-26"

subcollection: vmware-cross-region-dr

keywords:
---
{{site.data.keyword.attribute-definition-list}}

# Network design
{: #network-design}

The following are network design requirements and considerations for the VMware Disaster Recovery with Veeam pattern.

1. **Firewall Configuration:**
   - **Ports and Protocols:** Make sure that the necessary ports and protocols are open in the firewalls between Veeam components, VMware infrastructure, and any other relevant systems. Veeam provides documentation specifying the required ports for different components and functions.
2. **Network segmentation:**
   - **Isolation:** Segment the network to isolate Veeam components from other critical infrastructure. This can enhance security and prevent unauthorized access.
3. **Backup Repository Connectivity:**
   - **Direct Access:** Make sure that Veeam backup repositories are directly accessible by Veeam components over the network. This is crucial for efficient data transfer and storage operations.
4. **Proxy Deployment:**
   - **Strategic Proxy Placement:** Deploy Veeam proxies strategically within the network to optimize data transfer. Proxies can be placed closer to the data source to reduce the load on the central Veeam server.
5. **VMware vSphere Connectivity:**
   - **vCenter and ESXi Host Connectivity:** Establish reliable connections to VMware vCenter and ESXi hosts to facilitate the discovery of VMs, configuration settings retrieval, and other interactions between Veeam and VMware.
6. **Secure Communication:**
   - **TLS/SSL Encryption:** Enable TLS/SSL encryption for communication between Veeam components to make sure the confidentiality and integrity of data in transit.
   - **Secure Communication with VMware:** Use secure communication protocols when you are interacting with VMware infrastructure, adhering to best practices for securing VMware environments.
7. **DNS Configuration:**
   - **Name Resolution:** Make sure proper DNS configuration for Veeam components and VMware infrastructure to enable seamless name resolution. This is essential for the identification and communication between components.
8. **Time Synchronization:**
   - **NTP (Network Time Protocol):** Synchronize the clocks across Veeam components, VMware hosts, and other relevant systems by using NTP to make sure accurate timestamps for logs and data consistency.
9. **Redundancy and High Availability:**
   - **Redundant Network Paths:** Configure redundant network paths to provide high availability and fault tolerance, ensuring that a failure in one path does not disrupt data transfer operations.

When deployed the Veeam bare metal server on the production site is connected to two IBM Cloud Private VLANs, with no public connectivity by default. Each Veeam bare metal server is deployed with dual private only 10 GbE NICs with standard NIC teaming. The Veeam bare metal servers are deployed to have connectivity to the following infrastructure VLANs:

| VLAN   | Designation traffic type                            |
| ------ | --------------------------------------------------- |
| VLAN 1 | Private A ESXi management, management, Geneve (TEP) |
| VLAN 2 | Private B vSAN, NFS, vMotion, and Edge Geneve (TEP) |
 {: caption="Table 1. Traffic type designations" caption-side="bottom"}

The Veeam bare metal servers are natively provisioned to VLAN 1, and they need one IP address from the private primary subnet of VLAN 1 for the all-in-one server, which is deployed and configured during server provisioning of the nontagged VLAN 1.

Each Veeam bare metal server also needs to be VLAN trunked to VLAN 2. They need one IP address from the private portable subnet of the VLAN 2 for the all-in-one server from the NFS or vSAN subnet, which is configured through the automation on the tagged VLAN 2.

The Veeam backup server must have network connectivity to:

- All the backup and CDP proxies, including the ones on the target site

- All the ESXi hosts where VMs and replicas are running

- The vCenter servers in the production and DR environments

- Optional: Internet connectivity

- Optional: Connectivity to IBM Cloud Object Storage

In this pattern, connectivity between the production and DR environments is achieved by using the IBM Cloud backbone, with no extra components needed and no cost associated to the traffic.

The Veeam backup server connectivity requirements are met by keeping all the backup/CDP proxies connected to an IBM Cloud Private VLAN.

The optional internet connectivity, which primary use case is to connect to Veeam support for updates, can be achieved by setting up a proxy or NAT connection to the public network.

If a Veeam scale-out backup repository is needed (not required for the replication), the ideal candidate is IBM Cloud Object Storage (Cloud Object Storage). Access to Cloud Object Storage can be achieved by using a private endpoint.

**Is a service endpoint sufficient or do we really need a proxy as mentioned in IBM Cloud documentation?**

“If you want to access IBM Cloud Object Storage by using Veeam on a private-only vCenter Server instance, you must configure Veeam to access the internet through a proxy server. This way, Veeam can verify the TLS certificate that is used by IBM Cloud Object Storage.”

**Add aspects to recover networking**
