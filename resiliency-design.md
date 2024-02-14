---
copyright:
  years: 2024
lastupdated: "2024-01-29"

subcollection: vmware-cross-region-dr

keywords:
---
{{site.data.keyword.attribute-definition-list}}

# Resiliency design

{: \#resiliency-design}

This document expands on the resiliency aspect of the IBM Architecture framework in respect of the Veeam for disaster recovery for VMware workloads pattern.

## Requirements

The requirements for the resiliency aspect for the Veeam for disaster recovery for VMware workloads pattern focuses on the following:

- Replicate VMware workloads from a protected site to a recovery site in a different region for failover of workloads in the event of failure in the protected site.
- Failover that meets the required RTO/RPO of the application.

Veeam Backup & Replication supports two types of replications, Veeam “standard” replication and Veeam Continuous Data Protection (CDP) replication.

## Veeam Replication

Veeam replication is based on vSphere snapshots. During the first replication cycle a full replica of the data of the protected VM is created on the DR site. The following replication cycles are incremental, only changed blocks are copied (Veeam leverages VMware vSphere Changed Block Tracking (CBT), introduced in vSphere 7)

![A green and white logo on a black background Description automatically generated](image/Veeam-Replication-architecture.drawio.svg)

Figure 1 Veeam standard replication architecture

To adapt to IBM Cloud/recreate (remove the wan accelerators) – “standard” replication architecture.

To adapt to IBM Cloud/recreate – “standard” replication architecture.

Veeam Replication is recommended to protect VMs with a recovery point objective (RPO) in hours. If a more aggressive RPO is needed, Veeam Continuous Data Protection must be used.

## Veeam Continuous Data Protection

Veaam Continuous Data Protection (CDP) constantly replicates the I/O operations of the VMs. It uses vSphere APIs for I/O filtering (VAIO) to read and process the communications between the protected VMs and their storage. CDP requires the installation of an I/O filter on the ESXi clusters where the protected workloads are running. The I/O filter is not automatically installed on the VCS clusters when the Veeam all-in-one is deployed, this needs to be manually done, see [Installing I/O Filter](https://helpcenter.veeam.com/docs/backup/vsphere/cdp_io_filter_install.html?ver=120){: external}.

CDP does not create or use snapshots and allows a much lower RPO (near zero) than standard snapshots based replication. {: note}

The I/O operations data is stored in the target datastore and associated to short-term restore points (allowing recovering to seconds or minutes before a disaster). Short term restore points are kept for a maximum of 24 hours. To recover VMs to an older state, additional “long term” restore points can be defined to allow recovering a VM state from hours or days ago. See[Continuous Data Protection (CDP)](https://helpcenter.veeam.com/docs/backup/vsphere/cdp_replication.html?ver=120%3e){: external}.

![A black background with white text Description automatically generated](image/Veeam-CDP-Architecture.svg)

Figure 2 Veeam CDP replication architecture

CDP only works for powered on VMs. {: note}

## Resiliency considerations

Some key considerations for resiliency are listed below.

- Ensure the location of all components and their required regulatory compliance can be achieved. For example, with a single campus MZR or data center the placement of the KMIP for VMware, Key Protect and Hyper Protect Crypto Services is not local to the VMware (VCS) instance.
- If using different availability zones in a multi-zone region for the protected and recovery sites, ensure that the geographical distance is sufficient for any compliance and that the risk of the same physical event impacting both sites is low.
- Services in each region are considered independent, such that a failure in one region does not impact the service in the other region.
- Management applications in the vSphere management cluster do not require to be protected.
- The workload cluster in the recovery region requires sufficient free capacity to host the protected workloads.
- In normal operations, workload virtual machines can run in the recovery region if required. However, there needs to be enough initial capacity in the recovery region to run the recovered workload virtual machines upon disaster recovery invocation. These workloads might be test and development workloads that are sacrificial upon invocation or test.
- Recovery Point Objective (RPO) and Recovery Time Objective (RTO) depend on many variables. Therefore, the vCenter Server dual region design provides no standard Service Level Agreement (SLA) for RPO or RTO. However, review the following information about RPO and RTO:
  - The VMware vSphere clusters in the recovery region are provisioned and are available to run workloads as soon as these workload VMs are started after disaster recovery invocation.
  - The core management components in the recovery region (vCenter Server and the NSX-T™ Manager cluster) are running, so there is no infrastructure deployment wait time.
  - The recovery infrastructure is being monitored through the management toolset (ideally VMware Aria® Operations™), so that the recovery infrastructure resources are healthy, and ready to be used.
- To provide resiliency for the replication tasks, it is recommended to use at least 2 Veeam backup/CDP proxies in each site (4 Veeam backup/CDP proxies in total). In the pattern this translates into the following proxies being deployed:
  - Protected site - 2 x Veeam backup/CDP proxy running on virtual machines.
  - Recovery site - 1 x Veeam backup/CDP proxy running on the Veeam Back & Replication server and 1 x Veeam backup/CDP proxy on a virtual machine
- Ideally proxies per ESXi host should be deployed.
- In the event of one of the backup/CDP proxies becoming unavailable, **(only new?)** replication jobs would be distributed to the remaining proxy.
- To be able to quickly restore the replication functionalities in the event of the loss of the Veeam Backup & Replication server, the recommendation is:
  - To regularly back up the Veeam configuration to a IBM Cloud Object Storage bucket.
  - If you have decided to deploy the Veeam Backup & Replication server to the protected site, deploy a Veeam Backup & Replication server in the recovery site but not configured, that will act as a “standby” Veeam all-in-one. In the event of the loss of the Veeam Backup & Replication server located in the protected site, recovery would then simply consists in importing the backed up Veeam configuration from the IBM Cloud Object Storage bucket to the “standby” Veeam server in the recovery site. The “standby” Veeam server does not have to be identical to the server in the protected site.

## Recovery Scenarios

Listed below are some high level steps typically needed to recover the protected VMware workloads from a disaster, these steps are only provided for general guidance as each customer’s workload is unique and scenario is unique.

**Scenario 1** - A limited number of VMs become unavailable/corrupted in the production site

- If a backup of the VM(s) covering the desired recovery point is available in Veeam restore the affected from backup in the protected site.

**Scenario 2** - The VMware environment in the protected site becomes unavailable:

- Enable routing so that NSX overlay IP address ranges at the protected site are routed to the recovery site.
- Using Veeam, perform a failover of the production VMs (ideally using predefined failover plan).

When the protected VMware environment is available, reverse the network routing and using Veeam perform a failback (if changes made to the recovered VMs during the failover need to be kept) or undo the failover (if the changes made to the recovered VMs do not need to be kept)

**Scenario 3** - VMware protected environment becomes unavailable and the Veeam Backup & Replication server in the protected site becomes unavailable;

- Bring up the standby Veeam server in the recovery site (or create one if not already existing).
- Power off the recovery site Veeam server if it still running to avoid any potential for a split brain situation.
- Import the Veeam configuration backup from IBM Cloud Object Storage.
- Enable routing so that NSX overlay IP address ranges at the protected site are routed to the recovery site.
- In Veeam, perform a failover of the protected VMs (ideally using predefined failover plan)

When the protected VMware environment is available, reverse the network routing and using Veeam perform a failback (if changes made to the recovered VMs during the failover need to be kept) or undo the failover (if the changes made to the recovered VMs do not need to be kept) and revert the DNS changes if any were made)

See [Failback](https://helpcenter.veeam.com/docs/backup/vsphere/failover_failback.html?ver=120){: external} for more information regarding failover operations with Veeam and a detailed decision tree.

**Additional recovery considerations;**

- Ensure that the required firewall ports are open between the recovered VMs (e.g.: NSX-T distributed firewall rules) and their eventual external dependencies.

## Backup considerations

While backup is not part of this disaster recovery pattern, consider the following when combining backup and disaster recovery

- The industry best practice for backup resiliency is to follow the 3:2:1 rule:
  - 3: at least three copies of your data (original production data and two backups)
  - 2: at least two different types of media to store the copies of your data (e.g.: local disk and cloud)
  - 1: at least one backup off-site (e.g.: in the cloud)
- It is strongly recommended to setup an additional backup target to maintain the capacity to restore the VMs in the event of a complete loss of the all-in-one Veeam bare metal server. The recommended way to achieve this in the present pattern is to set up a Veeam scale-out backup repository leveraging IBM Cloud Object Storage (COS) and use the Veeam backup copy functionality (see https://helpcenter.veeam.com/docs/backup/vsphere/backup_copy.html?ver=120){: external}
- It is also recommended to not only backup the protected virtual machines data to COS but also the configuration of the Veeam solution (see https://helpcenter.veeam.com/docs/backup/vsphere/export_vbr_config.html?ver=120){: external}
