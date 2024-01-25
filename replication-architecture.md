---
copyright:
  years: 2023
lastupdated: "2023-12-18"

subcollection: 

keywords:
---
# Architecture decisions for replication

{: \#resiliency-architecture}

The following sections summarize the Veeam Disaster Recovery on IBM Cloud for VMware architecture.

## Architecture decisions for high availability

{: \#high-availability}

| Architecture decision                          | Requirement                                                                              | Option                                                                                                                                  | Decision                               | Rationale                                                    |
| ---------------------------------------------- | ---------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------- | ------------------------------------------------------------ |
| High availability deployment of Veeam          | Ensure availability of resources if outages occur. Support SLA targets for availability. | If Primary Veeam is deployed as Bare Metal, HA can be a VSI instance. Its required to migrate all configs to VSI for recovery purposes. | Standard Veeam deployment on IBM Cloud | ??                                                           |
| Replication type between the IBM Cloud regions | Replicate the VM data between the IBM regions so that the VMs could be recovered.        | Standard Veeam replication  Veeam Continuous Data Protection (CDP)                                                                      | Dependent on the RPO/RTO target        | If RPO in minutes, CDP, otherwise standard Veeam replication |
