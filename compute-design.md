---
copyright:
  years: 2024
lastupdated: "2024-02-27"

subcollection: vmware-cross-region-dr

keywords:
---
{{site.data.keyword.attribute-definition-list}}

# Compute design

{: compute-design}

The following are compute design requirements and considerations for the VMware Disaster Recovery with Veeam pattern.

## Requirement
{: requirement-design}

The requirements for the compute aspect for the Veeam for disaster recovery for VMware workloads pattern focuses on the following:

- The compute required for the Veeam components.
- The compute aspect for the recovered workloads.

## Veeam Architecture Components
{: #arch-veeam}

Review the requirements for the Veeam components:

- Use the add-on optional service deployment that is possible to enable a quicker and easier deployment.
- Deploy additional components only if required for resiliency or performance.

 On {{site.data.keyword.Bluemix_notm}}, the add-on optional Veeam service has three compute deployment options:

- Bare metal server with local storage.
- {{site.data.keyword.Bluemix_notm}} classic Virtual Server Instance (VSI) with iSCSI storage.
- A virtual machine with iSCSI storage hosted on the {{site.data.keyword.Bluemix_notm}} VMware deployment.

All of the deployment options are “all-in-one”, for example, several Veeam components are all installed on the same compute and contain the minimum number of components that are needed for backing up the workloads that are hosted on the vCenter server instance.

All the deployments deploy the Veeam components onto a Microsoft Windows operating system.

Also, an optional Linux hardened repository, that uses a bare metal server with local storage, can be deployed along with the bare metal server, VSI, or virtual machine. {: note}

### The bare metal server option
{: #bare-server-option}

- Managed service providers who don't want the dependency of hosting their service on a customer-managed cluster.
- 10 GB network interfaces on the same network as the ESXi hosts enabling high-performance efficient data transfer by using the network transport mode.
- "All-in-one" deployments for VMware cluster that uses {{site.data.keyword.Bluemix_notm}} storage-backed data stores. It's a good practice for backup copies of data to be on different storage to the primary copy, having backups on local storage and the primary copy on{{site.data.keyword.Bluemix_notm}} storage adheres to this best practice.
- The bare metal server option can't be used where data sovereignty requirements dictate that shared storage, such as {{site.data.keyword.Bluemix_notm}} storage services.

### The virtual machine option
{: #virtual-machine-option}

- Using vSphere high availability (HA) for resiliency of the hosted Veeam components from hardware failure.
- While this option uses iSCSI storage for the backup repository, a Linux hardened repository can be ordered and used to hold backups of the primary copy on different storage media. Alternatively, {{site.data.keyword.cos_full_notm}} can be used to provide a different storage media.
- "All-in-one" deployments where the VMware Backup Proxy can use the Virtual Appliance (hot-add) transport mode.

### The virtual server instance option
{: #virtual-server-option}

- The Windows operating system license is included in the charges for the VSI.
- 1 GB network interface on the same network as the ESXi hosts enabling efficient data transfer by using the network transport mode.

Use the following decision tree to decide which option to select.

![A diagram of a server description automatically generated](image/decision_tree-veeam_deployment.drawio.svg){: caption="Figure 1. A diagram of a server description automatically generated" caption-side="bottom"}

For this disaster recovery pattern, there was no requirement for physical isolation of the backup and the virtual machine option was selected. The recovery site was selected for the deployment so that the Veeam Backup and Replication server were available instantly for disaster recovery invocation.

If this pattern was to include backup and physical isolation as a requirement, then consider the following:

- Deploy a separate instance of Veeam at the protected site either by using a bare metal server, VSI, or virtual machine with a Linux hardened repository. This topology does enable separation of responsibilities between backup and disaster recovery, but at the expense of Veeam licensing.
- Deploy bare metal servers in the protected region for backup repositories.
- Use {{site.data.keyword.cos_full_notm}} with Veeam direct access or gateway access. For more information, see [Object Storage Repository Deployment](https://helpcenter.veeam.com/docs/backup/vsphere/object_storage_repository.html?ver=120#object-storage-repository-deployment){: external}.

## Recovery Site
{: #recovery-site}

The recovery environment must include enough bare metal ESXi hosts created to be able to bring up the virtual machine replicas of the protected workloads when a disaster renders the protected VMware environment unavailable.

To optimize the cost of having bare metal hosts constantly created on the recovery site without an actual workload, you can run “sacrificial” development or test workloads, for which no disaster recovery is needed and can be powered off to free capacity if of a disaster recovery invocation or test. Offloading this type of workload to the DR site also reduces the number and size of ESXi hosts needed on the protected site.
