---
copyright:
  years: 2023
lastupdated: "2023-12-18"

subcollection: 

keywords:

---

# Architecture decisions for resiliency

{: \#resiliency-architecture}

The following sections summarize the Veeam Disaster Recovery on IBM Cloud for VMware architecture.

## Architecture decisions for high Resiliency

{: \#high-availability}

| **Architecture decision**                            | **Requirement**                                                                                                                                                    | **Option**                                                         | **Decision**                    | **Rationale**                                                                                                                               |
|------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|---------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Replication type between the IBM Cloud regions       | Replicate the VM data between the IBM regions so that the VMs could be recovered.                                                                                  | Standard Veeam replication, Veeam Continuous Data Protection (CDP) | Dependent on the RPO/RTO target | If RPO in minutes, CDP, otherwise standard Veeam replication                                                                                |
| High availability of Veeam Backup & Replication      | Ensure availability of resources if outages occur. Support SLA targets for availability.                                                                           | Bare metal, virtual server instance, virtual machine               | Virtual Machine                 | Use a virtual machine deployment for Veeam Backup & Replication so the virtual machine can leverage vSphere HA and be restarted on failure. |
| High availability of Veeam VMware Backup/CDP proxies | Ensure availability of resources if outages occur. Support SLA targets for availability.                                                                           | Single proxy, multiple proxies                                     | Multiple Veeam Proxies          | Deploy multiple Veeam VMware Backup/CDP proxies and allow Veeam to select the appropriate proxy.                                            |
| High availability of Veeam Backup repository         | Ensure availability of resources if outages occur. Support SLA targets for availability.                                                                           | Bare metal, virtual server instance, virtual machine               | Virtual Machine                 | Use a virtual machine deployment for Veeam Backup & Replication so the virtual machine can leverage vSphere HA and be restarted on failure. |
| Veeam Backup & Replication database backup           | The Veeam Backup & Replication database should be backed up so that if recovery is needed the database backup can be recovered to a new Veeam Backup & Replication | Repository server, IBM Cloud Object Storage                        | IBM Cloud Object Storage        | The use of IBM Cloud Object Storage means that the database can be backed up regionally or cross-regionally as required.                    |

{: caption="Table 1. High availability architecture decisions" caption-side="bottom"}
