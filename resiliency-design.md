---
copyright:
  years: 2023
lastupdated: "2023-12-26"

subcollection: pattern-sap-on-vpc

keywords:
---

{{site.data.keyword.attribute-definition-list}}

# Resiliency design

{: \#resiliency-design}

Resilency Considerations

Types of replication (standard vs CDP)

Veeam on IBM cloud is based on Veeam Backup and Replication 12 OS Add-on and Veeam Availability Suite 12 Replication mode (virtual appliance replication mode, network replication mode)

## Resiliency Region

-   Ensure the location of all service components and their required regulatory compliance can be achieved. Example, with a single campus MZR or data center the placement of the KMIP for VMware and Hyper Protect Crypto Services is not local to the VMWare (VCS) instance.
-   Geographical distance - the risk that the same physical event impacts multizone regions is low.
-   Resiliency - logical services in each region are independent, such that a failure in one service does not impact the service in the other region.
-   Management cluster requires sufficient free capacity to host the protected management applications.
-   Workload cluster requires sufficient free capacity to host the protected workloads.
-   In normal operations, workload VMs can run in the recovery region if required. However, there needs to be enough initial capacity in the recovery region to run the recovered workload VMs upon DR invocation. These workloads might be test and development workloads that are sacrificial upon DR.
-   Recovery Point Objective (RPO) and Recovery Time Objective (RTO) depend on many variables. Therefore, the vCenter Server dual region design provides no standard Service Level Agreement (SLA) for RPO or RTO. However, review the following information about RPO and RTO:
-   The VMware vSphere clusters in the recovery region are provisioned and are available to run workloads as soon as these workload VMs are started after DR invocation.
-   The core management components in the recovery region (vCenter Server and the NSX-T™ Manager cluster) are running, so there is no infrastructure deployment wait time.
-   The recovery infrastructure is being monitored through the management toolset (ideally VMware Aria® Operations™), so that the recovery infrastructure resources are healthy, and ready to be used.

**CDP Proxy Reference architecture for a Disaster recovery solution on IBM cloud (Across different Cloud Region)**

-   operates as a data mover and transfers data between the source and target hosts.
-   Role can be assigned to any Windows-managed virtual or physical server added to your Veeam

## Backup infrastructure.

**Backup**

The industry best practice for backup resiliency is to follow the 3:2:1 rule:

1.  3: at least three copies of your data (original production data and two backups)
2.  2: at least two different types of media to store the copies of your data (e.g.: local disk and cloud)
3.  1: at least one backup off-site (e.g.: in the cloud)

In this pattern, the all-in-one bare metal Veeam server acts as the backup repository and stores the backup data of the protected virtual machines on its local disks.

It is strongly recommended to setup an additional backup target to maintain the capacity to restore the VMs in the event of a complete loss of the all-in-one Veeam bare metal server. The recommended way to achieve this in the present pattern is to set up a Veeam scale-out backup repository leveraging IBM Cloud Object Storage (COS) and use the Veeam backup copy functionality (see https://helpcenter.veeam.com/docs/backup/vsphere/backup_copy.html?ver=120).

It is also recommended to not only backup the protected virtual machines data to COS but also the configuration of the Veeam solution (see https://helpcenter.veeam.com/docs/backup/vsphere/export_vbr_config.html?ver=120).

**Replication**

To provide resiliency for the replication tasks, it is recommended to use at least 2 Veeam backup/CDP proxies in each site (4 Veeam backup/CDP proxies in total).

In the event of one of the backup/CDP proxies becoming unavailable, **(only new?)** replication jobs would be distributed to the remaining proxy.

In the current pattern this translates into the following proxies being deployed:

Production site:

-   1x Veeam backup/CDP proxy running on the Veeam all-in-one bare metal server
-   1x Veeam backup/CDP proxy running in a standard IBM Cloud linux VSI

DR site:

-   1x Veeam backup/CDP proxy running in a standard IBM Cloud linux VSIs

**Recovery from a loss of the Veeam all-in-one bare metal**

To be able to quickly restore the backup and replication functionalities in the event of the loss of the Veeam all-in-one bare metal, the recommendation is:

\- To regularly back up the Veeam configuration to a cloud location (see the resiliency backup section above)

\- To have an already provisioned VSI or bare metal with all the Veeam components installed but not configured in the DR site, that will act as a “standby” Veeam all-in-one.

In the event of a loss of the Veeam bare metal located in the production site, its recovery would then simply consists in importing the backed up Veeam configuration from COS to the “standby” Veeam all-in-one server in the DR site

*@Neil Taylor:*

**Do we need the “standby” Veeam all-in-one to be exactly identical (deployment type, storage…)?**
