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

When deployed the Veeam bare metal server on the production site is connected to two IBM Cloud private VLANs, with no public connectivity by default.

Each Veeam® bare metal server is deployed with dual private only 10 GbE NICs with standard NIC teaming.

The Veeam bare metal servers are deployed to have connectivity to the following infrastructure VLANs:

Table 1. VLAN designations

VLAN Designation Traffic type

VLAN 1 Private A ESXi management, management, Geneve (TEP)

VLAN 2 Private B vSAN, NFS, vMotion, and Edge Geneve (TEP)

The Veeam bare metal servers are natively provisioned to VLAN 1, and they need one IP address from the private primary subnet of VLAN 1 for the all-in-one server, which is deployed and configured during server provisioning of the non-tagged VLAN 1.

Each Veeam bare metal server also needs to be VLAN trunked to VLAN 2. They need one IP address from the private portable subnet of the VLAN 2 for the all-in-one server from the NFS or vSAN subnet, which is configured through the automation on the tagged VLAN 2.

**Requirements**

The Veeam backup server must have network connectivity to:

\- all the backup/CDP proxies, including the ones located on the target site

\- all the ESXi hosts where VMs/replicas are/will be running

\- the vCenter servers in the production and DR environments

\- Optional – Internet connectivity

\- Optional – Connectivity to IBM Cloud Object Storage

---



Here are key networking requirements:

1. **Firewall Configuration:**
   * **Ports and Protocols:** Ensure that the necessary ports and protocols are open in the firewalls between Veeam components, VMware infrastructure, and any other relevant systems. Veeam provides documentation specifying the required ports for different components and functionalities.
2. **Network Latency:**
   * **Low Latency:** Maintain low network latency between Veeam components, VMware hosts, and data repositories to ensure efficient data transfer and timely backup and CDP operations.
3. **Bandwidth Requirements:**
   * **Adequate Bandwidth:** Provide sufficient network bandwidth to accommodate data transfer requirements, especially during backup and replication processes. Consider the volume of data being transferred and the desired RPO (Recovery Point Objective).
4. **Network Segmentation:**
   * **Isolation:** Segment the network to isolate Veeam components from other critical infrastructure. This can enhance security and prevent unauthorized access.
5. **Backup Repository Connectivity:**
   * **Direct Access:** Ensure that Veeam backup repositories are directly accessible by Veeam components over the network. This is crucial for efficient data transfer and storage operations.
6. **Proxy Deployment:**
   * **Strategic Proxy Placement:** Deploy Veeam proxies strategically within the network to optimize data transfer. Proxies can be placed closer to the data source to reduce the load on the central Veeam server.
7. **VMware vSphere Connectivity:**
   * **vCenter and ESXi Host Connectivity:** Establish reliable connections to VMware vCenter and ESXi hosts to facilitate the discovery of VMs, configuration settings retrieval, and other interactions between Veeam and VMware.
8. **Secure Communication:**
   * **TLS/SSL Encryption:** Enable TLS/SSL encryption for communication between Veeam components to ensure the confidentiality and integrity of data in transit.
   * **Secure Communication with VMware:** Use secure communication protocols when interacting with VMware infrastructure, adhering to best practices for securing VMware environments.
9. **DNS Configuration:**
   * **Name Resolution:** Ensure proper DNS configuration for Veeam components and VMware infrastructure to enable seamless name resolution. This is essential for the identification and communication between components.
10. **Load Balancing:**
    * **Load-Balanced Configurations:** If deploying multiple instances of Veeam components (e.g., proxies, repositories), consider implementing load balancing to distribute workloads evenly and improve overall performance.
11. **Time Synchronization:**
    * **NTP (Network Time Protocol):** Synchronize the clocks across Veeam components, VMware hosts, and other relevant systems using NTP to ensure accurate timestamps for logs and data consistency.
12. **Quality of Service (QoS):**
    * **QoS Policies:** Implement Quality of Service policies to prioritize Veeam traffic, especially during peak backup and replication periods, to ensure that critical data transfer is not adversely affected by other network activities.
13. **Redundancy and High Availability:**
    * **Redundant Network Paths:** Configure redundant network paths to provide high availability and fault tolerance, ensuring that a failure in one path does not disrupt data transfer operations.

In this pattern, connectivity between the production and DR environments is achieved by leveraging the IBM Cloud backbone, with no extra component needed and no cost associated to the traffic.

The Veeam backup server connectivity requirements are met by keeping all the backup/CDP proxies connected to an IBM Cloud Private VLAN.

The optional internet connectivity, which primary use case is to connect to Veeam support for updates, can be achieved by setting up a proxy or NAT connection to the public network.

If a Veeam scale-out backup repository is needed (not required for the replication), the ideal candidate is IBM Cloud Object Storage (COS). Access to COS can be achieved through the use of a private endpoint.

**Is a service endpoint sufficient or do we really need a proxy as mentioned in IBM Cloud documentation?:**

“If you want to access IBM Cloud Object Storage by using Veeam on a private-only vCenter Server instance, you must configure Veeam to access the internet through a proxy server. This way, Veeam can verify the TLS certificate that is used by IBM Cloud Object Storage.”

**Add bring up aspects to recover networking**
