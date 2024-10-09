---
copyright:
  years: 2024
lastupdated: "2024-02-29"

subcollection: 

keywords:

---

# Architecture decisions for resiliency and replication
{: #resiliency-architecture}

The following tables summarize the architecture decisions for the Veeam Disaster Recovery on {{site.data.keyword.Bluemix_notm}} for VMware.


## Architecture decisions for resiliency
{: #high-availability}

| Architecture decision                            | Requirement                                                                                                                                                    | Option                                                         | Decision                    | Rationale                                                                                                                               |
|------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|---------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Replication type between the {{site.data.keyword.Bluemix_notm}} regions       | Replicate the virtual machine (VM) data between the IBM regions so that the VMs can be recovered.                                                                                  | Standard Veeam replication, Veeam Continuous Data Protection (CDP) | Dependent on the RPO and RTO target | If RPO is in minutes, use CDP. Otherwise, use standard Veeam replication.                                                                                |
| High availability (HA) of Veeam Backup and Replication      | Make sure availability of resources if outages occur. Support SLA targets for availability.                                                                           | Bare metal, virtual server instance, virtual machine               | Virtual machine                 | Use a virtual machine deployment for Veeam Backup and Replication so the virtual machine can use vSphere HA and be restarted on failure. |
| High availability of Veeam VMware Backup and CDP proxies | Make sure availability of resources if outages occur. Support SLA targets for availability.                                                                           | Single proxy, multiple proxies                                     | Multiple Veeam proxies          | Deploy multiple Veeam VMware Backup and CDP proxies and allow Veeam to select the appropriate proxy.                                            |
| High availability (HA) of Veeam Backup repository         | Make sure availability of resources if outages occur. Support SLA targets for availability.                                                                           | Bare metal, virtual server instance, virtual machine               | Virtual machine                 | Use a virtual machine deployment for Veeam Backup and Replication so the virtual machine can use vSphere HA and be restarted on failure. |
| Veeam Backup and Replication database backup           | Back up the Veeam Backup and Replication database so that if recovery is needed, the database backup can be recovered to a new Veeam Backup and Replication | Repository server, {{site.data.keyword.cos_full_notm}}                        | {{site.data.keyword.cos_full_notm}}        | The use of {{site.data.keyword.cos_full_notm}} means that the database can be backed up regionally or cross-regionally as required.                    |
{: caption="High availability architecture decisions" caption-side="bottom"}

## Architecture decisions for replication
{: #resiliency-architecture}

The following are replication architecture decisions for the VMware Disaster Recovery using Veeam.

| Architecture decision  | Requirement                                                                       | Option                                                            | Decision                        | Rationale                                                                                                                                                                                                              |
|------------------------|-----------------------------------------------------------------------------------|-------------------------------------------------------------------|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Replication type       | Replicate the VM data between the {{site.data.keyword.IBM_notm}} regions so that the VMs can be recovered. | Standard Veeam replication Veeam Continuous Data Protection (CDP) | Dependent on the RPO and RTO target | If RPO is in minutes, use CDP. Otherwise, use standard Veeam replication.                                                                                                                                                           |
| Recovery site location | Replicate the virtual machines to another {{site.data.keyword.IBM_notm}} location.                           | Availability zone in the same region, different region            | Different region                | While it is unlikely that two availability zones will suffer an outage at the same time, there might be a risk that a client considers to be unacceptable. Therefore, select another {{site.data.keyword.IBM_notm}} region for the recovery location. |
{: caption="Replication decisions" caption-side="bottom"}
