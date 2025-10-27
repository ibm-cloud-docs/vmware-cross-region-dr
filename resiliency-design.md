---
copyright:
  years: 2024
lastupdated: "2024-02-29"

subcollection: vmware-cross-region-dr

keywords:
---
{{site.data.keyword.attribute-definition-list}}

# Resiliency design
{: #resiliency-design}

The following are resiliency design requirements and considerations for the VMware Disaster Recovery with Veeam pattern.

## Requirements
{: #resiliency-requirements}

- Replicate VMware workloads from a protected site to a recovery site that's in a different region for failover of workloads from a failure in the protected site.
- Failover that meets the required RTO and RPO of the application.

Veeam Backup and Replication support two types of replications, Veeam “standard” replication and Veeam Continuous Data Protection (CDP) replication.

## Veeam Replication
{: #resiliency-veeam-rep}

Veeam replication is based on vSphere snapshots. During the first replication cycle, a full replica of the data of the protected VM is created on the disaster recovery (DR) site. The following replication cycles are incremental. Only changed blocks are copied. Veeam uses VMware vSphere Changed Block Tracking (CBT), introduced in vSphere 7.

![Veeam replication architecture](image/Veeam-Replication-architecture.drawio.svg){: caption="Veeam standard replication architecture." caption-side="bottom"}


Veeam replication is recommended to protect VMs with a recovery point objective (RPO) in hours. If a more aggressive RPO is needed, Veeam Continuous Data Protection must be used.

## Veeam Continuous Data Protection
{: #continuous-protection-resiliency}

Veeam Continuous Data Protection (CDP) constantly replicates the I/O operations of the VMs. It uses vSphere APIs for I/O filtering (VAIO) to read and process the communications between the protected VMs and their storage. CDP requires the installation of an I/O filter on the ESXi clusters where the protected workloads are running. The I/O filter is not automatically installed on the VCS clusters when the Veeam all-in-one is deployed, this needs to be manually done. For more information, see [Installing I/O Filter](https://helpcenter.veeam.com/docs/backup/vsphere/cdp_io_filter_install.html?ver=120){: external}.

CDP does not create or use snapshots and allows a lower RPO (near zero) than standard snapshots-based replication. {: note}

The I/O operations data is stored in the target data store and associated to short-term restore points, allowing recovering to seconds or minutes before a disaster. Short-term restore points are kept for a maximum of 24 hours. To recover VMs to an older state, extra “long term” restore points can be defined to allow recovering a VM state from hours or days ago. For more information, see [Continuous Data Protection (CDP)](https://helpcenter.veeam.com/docs/backup/vsphere/cdp_replication.html?ver=120){: external}.

![Veeam CDP replication architecture](image/Veeam-CDP-Architecture.svg){: caption="Figure 2.Veeam CDP replication architecture." caption-side="bottom"}

CDP works for only powered on VMs. {: note}

## Resiliency considerations
{: #resiliency-considerations}

Review a few key considerations for resiliency:

- Make sure the location of all components and their required regulatory compliance can be achieved. For example, with a single campus MZR or data center the placement of the KMIP for VMware, Key Protect, and Hyper Protect Crypto Services is not local to the VMware (VCS) instance.
- If you use different availability zones in a multi-zone region for the protected and recovery sites, make sure that the geographical distance is sufficient for any compliance and that the risk of the same physical event that impacts both sites is low.
- Services in each region are considered independent, such that a failure in one region does not impact the service in the other region.
- Management applications in the vSphere management cluster don't require protection.
- The workload cluster in the recovery region requires sufficient free capacity to host the protected workloads.
- In normal operations, workload virtual machines can run in the recovery region if required. However, there needs to be enough initial capacity in the recovery region to run the recovered workload on virtual machines upon disaster recovery invocation. These workloads might be test and development workloads that are sacrificial upon invocation or test.
- Recovery Point Objective (RPO) and Recovery Time Objective (RTO) depend on many variables. Therefore, the vCenter Server dual-region design provides no standard Service Level Agreement (SLA) for RPO or RTO. However, review the following information about RPO and RTO:
   - The VMware vSphere clusters in the recovery region are provisioned and are available to run workloads when these workload VMs are started after disaster recovery invocation.
   - The core management components in the recovery region (vCenter Server and the NSX-T™ Manager cluster) are running which causes no infrastructure deployment wait time.
   - The recovery infrastructure is being monitored through the management toolset, ideally VMware Aria® Operations™, so that the recovery infrastructure resources are healthy, and ready to be used.
- To provide resiliency for the replication tasks, it is recommended to use at least 2 Veeam backup and CDP proxies in each site (4 Veeam backup and CDP proxies in total). In the pattern this translates into the following proxies being deployed:
   - Protected site: 2 x Veeam backup and CDP proxy running on virtual machines.
   - Recovery site: 1 x Veeam backup and CDP proxy running on the Veeam Back and Replication server and 1 x Veeam backup and CDP proxy on a virtual machine
- Ideally, proxies per ESXi host should be deployed.
- In the event that one of the backup and CDP proxies becoming unavailable, replication jobs would be distributed to the remaining proxy.
- To be able to quickly restore the replication functionalities in the event of the loss of the Veeam Backup and Replication server, the recommendation is to regularly back up the Veeam configuration to an {{site.data.keyword.cos_full_notm}} bucket.
   - If you decide to deploy the Veeam Backup and Replication server to the protected site, deploy a Veeam Backup and Replication server in the recovery site but not configured. That will act as a “standby” Veeam all-in-one. When the loss of the Veeam Backup and Replication server that is located in the protected site occurs, recovery would consist of importing the backed up Veeam configuration from the {{site.data.keyword.cos_full_notm}} bucket to the “standby” Veeam server in the recovery site. The “standby” Veeam server does not have to be identical to the server in the protected site.

## Recovery Scenarios
{: #recovery-scenarios}

The following are steps that are typically needed to recover the protected VMware workloads from a disaster. These steps are only provided for general guidance as each customer’s workload and scenario are unique.

Scenario 1:  A limited number of VMs become unavailable and corrupted in the production site

If a backup of the VMs covering the wanted recovery point is available in Veeam, restore the affected from backup in the protected site.

Scenario 2: The VMware environment in the protected site becomes unavailable

- Enable routing so that NSX overlay IP address ranges at the protected site are routed to the recovery site.
- Using Veeam, perform a failover of the production VMs ideally by using predefined failover plan.

When the protected VMware environment is available, reverse the network routing. Using Veeam, perform a failback if changes made to the recovered VMs during the failover need to be kept or undo the failover if the changes made to the recovered VMs do not need to be kept.

Scenario 3: VMware protected environment becomes unavailable and the Veeam Backup and Replication server in the protected site becomes unavailable

- Open the standby Veeam server in the recovery site or create one if it's not existing.
- Power off the recovery site Veeam server if it is still running to avoid any potential for a split-brain situation(divided control plane for Veeam).
- Import the Veeam configuration backup from {{site.data.keyword.cos_full_notm}}.
- Enable routing so that NSX overlay IP address ranges at the protected site are routed to the recovery site.
- In Veeam, perform a failover of the protected VMs ideally by using predefined failover plan.

When the protected VMware environment is available, reverse the network routing. Using Veeam, perform a failback if changes made to the recovered VMs during the failover need to be kept or undo the failover if the changes made to the recovered VMs do not need to be kept and revert the DNS changes if any were made.

For more information, see [Failback](https://helpcenter.veeam.com/docs/backup/vsphere/failover_failback.html?ver=120){: external}.

In addition, consider verifying that the required firewall ports are open between the recovered VMs, for example, NSX-T distributed firewall rules and their eventual external dependencies.

## Backup considerations
{: #backup-considerations-resiliency}

While backup is not part of this disaster recovery pattern, consider the following when combining backup and disaster recovery:

- The industry best practice for backup resiliency is to follow the 3:2:1 rule:
   - 3: At least three copies of your data (original production data and two backups).
   - 2: At least two different types of media to store the copies of your data, for example, local disk and cloud.
   - 1: At least one backup off-site, for example, in the cloud.
- It is recommended to setup an additional backup target to maintain the capacity to restore the VMs if a complete loss of the all-in-one Veeam bare metal server. The recommended way to achieve this in the present pattern is to set up a Veeam scale-out backup repository by using {{site.data.keyword.cos_full_notm}} and use the Veeam backup copy functionality. For more information, see [Backup Copy](https://helpcenter.veeam.com/docs/backup/vsphere/backup_copy.html?ver=120).{: external}
- Backup the protected virtual machines data to {{site.data.keyword.cos_full_notm}} and back up the configuration of the Veeam solution. For more information, see [Creating Configuration Backups](https://helpcenter.veeam.com/docs/backup/vsphere/export_vbr_config.html?ver=120){: external}
