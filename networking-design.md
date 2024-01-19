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

In this pattern, connectivity between the production and DR environments is achieved by leveraging the IBM Cloud backbone, with no extra component needed and no cost associated to the traffic.

The Veeam backup server connectivity requirements are met by keeping all the backup/CDP proxies connected to an IBM Cloud Private VLAN.

The optional internet connectivity, which primary use case is to connect to Veeam support for updates, can be achieved by setting up a proxy or NAT connection to the public network.

If a Veeam scale-out backup repository is needed (not required for the replication), the ideal candidate is IBM Cloud Object Storage (COS). Access to COS can be achieved through the use of a private endpoint.

**Is a service endpoint sufficient or do we really need a proxy as mentioned in IBM Cloud documentation?:**

“If you want to access IBM Cloud Object Storage by using Veeam on a private-only vCenter Server instance, you must configure Veeam to access the internet through a proxy server. This way, Veeam can verify the TLS certificate that is used by IBM Cloud Object Storage.”

**Add bring up aspects to recover networking**
