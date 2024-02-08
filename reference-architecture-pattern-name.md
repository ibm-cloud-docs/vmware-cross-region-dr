## copyright: years: 2024 lastupdated: "2024-02-06" subcollection: vmware-cross-region-dr

# VMware disaster recovery on IBM Cloud using Veeam.

This pattern describes the use of Veeam for a disaster recovery solution for VMware workloads where both the protected and recovery sites are in IBM Cloud. Veeam was selected for the disaster recovery product because of the following:

-   Veeam is an add-on additional service that is orderable via the IBM Cloud Catalog.
-   Veeam Replication is a technology that creates an exact copy of the protected VMware virtual machine at the recovery site. The replication maintains this copy in sync with the protected VM with Recovery Point Objective (RPO) of hours. Replication provides minimum Recovery Time Objective (RTO) as the recovery replicas are in a ready-to-start state. See [Replication](https://helpcenter.veeam.com/docs/backup/vsphere/replication.html?ver=120){: external}.
-   Veeam Continuous Data Protection (CDP) is a technology that helps protect VMware virtual machines where data loss for seconds or minutes, not hours, is required. CDP also provides minimum RTO as the CDP replicas are in a ready-to-start state. See [Continuous Data Protection (CDP)](https://helpcenter.veeam.com/docs/backup/vsphere/cdp_replication.html?ver=120){: external}.
-   Veeam supports VM encryption, and in this pattern, protected workloads must use data encryption as General Data Protection Regulation (GPDR), and other regulations requires Personally Identifiable Information (PII) or Sensitive Personal Information (SPI) data to be protected.

This pattern focuses on replication of virtual machines for disaster recovery only. Veeam supports both backup and replication, however, backup is not discussed in this pattern. {: important}

This pattern builds on Veeam best-practice guidance as follows:

-   Veeam Backup & Recovery server should be a virtual machine so that it can benefit from vSphere HA and be restarted due to hardware failure.
-   For replication, the Veeam Backup & Recovery server should be placed in the recovery site.
-   Veeam VMware Backup and CDP proxies should be placed "close" to the data source as the data is not yet compressed or de-duplicated.

## Veeam Architecture overview

{: \#architecture-diagram}

The disaster recovery pattern overview for VMware workloads on IBM Cloud Classic (VCS) architecture is shown below.

![](image/VCS_Veeam_cross_region.drawio.svg)

Figure 1Veeam Disaster Recovery Architecture on IBM Cloud

Figure 1 Veeam Disaster Recovery solution for VMware Workloads on IBM Cloud Classic (VCS) architecture

The key features of this pattern are as follows:

1.  **IBM Cloud Infrastructure:**
    1.  Two VMware-based virtualized environments hosted on IBM Cloud. Region 1 hosts the protected environment, and Region 2 hosts the recovery environment. Potentially the recovery region can also host development and test workloads that can be “sacrificed” when a disaster recovery is invoked or tested.
    2.  Multiple ESXi bare metal servers forming a cluster hosting virtual machines.
2.  **Veeam Backup & Replication Server:**
    1.  Responsible for managing the replication jobs.
    2.  Deployed onto a Microsoft Windows operating system.
    3.  Deployed within the recovery VMware environment as a virtual machine.
    4.  Veeam is deployed using the [Simple Deployment](https://helpcenter.veeam.com/docs/backup/vsphere/simple.html?ver=120){: external} scenario, also know as all-in-one.
    5.  The virtual machine deployment was selected to leverage the benefit from vSphere HA.
    6.  The recovery site was selected so that the server is available to quickly restore protected virtual machines.
3.  **Veeam Repository:**
    1.  The backup repository is responsible for storing replication metadata of the Veeam VMware Replication proxies.
    2.  The backup repository stores replica metadata that contains information on the read data blocks. See [Backup Repository](https://helpcenter.veeam.com/docs/backup/vsphere/replication_components.html?ver=120#backup-repository){: external}.
    3.  Only backup repository at the protected site is required.
    4.  In this pattern the backup repository is installed on a Linux virtual machine.
    5.  Backup repositories can be hosted on Microsoft Windows or Linux operating systems.
    6.  The repository has a single network interface, one on an IBM Cloud portable subnet used for proxies and on the IBM Cloud Private VLAN - Primary (Management) to enable efficient traffic flow from the Veeam VMware Backup proxy to the backup replication.
    7.  See [VMware Backup Proxies](https://helpcenter.veeam.com/docs/backup/vsphere/backup_proxy.html?ver=120){: external}.
4.  **Veeam Backup Proxy:**
    1.  The backup proxy is responsible for replication of virtual machines.
    2.  A minimum of one backup proxy per site is required, however, multiple backup proxies should be deployed for availability and scaling.
    3.  In this pattern backup proxies are installed on Linux virtual machines.
    4.  Backup proxies can be hosted on Microsoft Windows or Linux operating systems.
    5.  The proxies have two network interfaces; one on an IBM Cloud portable subnet used for proxies and on the IBM Cloud Private VLAN - Primary (Management) and a second on an IBM Cloud portable subnet on the IBM Cloud Private VLAN - Secondary (Storage/vMotion). This is to enable efficient traffic flow from the ESXi hosts' vmk0 interfaces to the proxies and the from the proxies to the remote proxies bypassing the firewalls.
    6.  See [VMware Backup Proxies](https://helpcenter.veeam.com/docs/backup/vsphere/backup_proxy.html?ver=120){: external}.
5.  **Veeam VMware CDP Proxy:**
    1.  The VMware CDP backup proxy is responsible for replication of virtual machines using CDP.
    2.  VMware CDP backup proxies are only needed if RPO in seconds is needed.
    3.  A minimum of one VMware CDP proxy per site is required, however, multiple VMware CDP proxies should be deployed for availability and scaling.
    4.  In this pattern VMware CDP proxies are installed on Linux virtual machines.
    5.  Backup proxies can be hosted on Microsoft Windows or Linux operating systems.
    6.  The proxies have two network interfaces; one on an IBM Cloud portable subnet used for proxies and on the IBM Cloud Private VLAN - Primary (Management) and a second on an IBM Cloud portable subnet on the IBM Cloud Private VLAN - Secondary (Storage/vMotion). This is to enable efficient traffic flow from the ESXi hosts' vmk0 interfaces to the proxies and the from the proxies to the remote proxies bypassing the firewalls.
    7.  See [VMware CDP Proxies](https://helpcenter.veeam.com/docs/backup/vsphere/cdp_proxy.html?ver=120){: external}
6.  **I/O filter:**
    1.  You must install the I/O filter on each cluster to be able to use CDP.
    2.  The I/O filter reads and processes I/O operations in transit between the protected virtual machines and their underlying datastore and sends/receives data to/from the VMware CDP proxies.
    3.  The I/O filter leverages vSphere API for I/O filtering (VAIO).
    4.  See [I/O Filter](https://helpcenter.veeam.com/docs/backup/vsphere/cdp_io_filter_install.html?ver=120){: external}
7.  **Veeam Backup Console:**
    1.  Console for configuring and monitoring replication jobs.
    2.  Installed by default on the Veeam Backup & Replication Server.
    3.  It is recommended that it is uninstalled from the Veeam Backup & Replication Server and installed on DevOps consoles.
    4.  See [Installing Veeam Backup & Replication Console](https://helpcenter.veeam.com/docs/backup/vsphere/install_console.html?ver=120){: external}
8.  **Veeam ONE:**
    1.  Veeam ONE, part of the Veeam Availability Suite, provides visibility into Veeam-protected workloads.
    2.  Veeam ONE provides; monitoring, reporting, alerting, diagnostics with automated resolutions and infrastructure utilization and capacity planning.

## Considerations

Consider the following when reviewing the pattern:

-   Network connectivity from on-premises to the IBM Cloud environments is considered as out of scope for this pattern.
-   The Veeam solution available from IBM Cloud VMware Solutions catalog leverages Veeam Availability Suite 12 which consists of Veeam Backup & Replication and Veeam ONE.
-   While the IBM Cloud automation deploys the optional add-on Veeam service, it deploys it as an-all-in-one deployment scenario. While applicable for some use-cases it only provides the base for this disaster recovery pattern. Therefore, additional post deployment tasks are required, including; ordering additional private portable subnets, deploying virtual machines, deploying Veeam services onto ESXi hosts and virtual machines.
-   The configuration of the Veeam Backup & Replication server should be configured for a scheduled backup. This pattern recommends using an IBM Cloud Object Storage bucket.
-   For more information on the Veeam components see the following:
    -   [Requirements and limitations for VMware backup proxies](file:////docs/vmwaresolutions%3ftopic=vmwaresolutions-veeam_proxies_req).
    -   [Transport Modes](file:////docs/vmwaresolutions%3ftopic=vmwaresolutions-veeam_proxies_transp_modes).
    -   [VMware backup proxy](file:////docs/vmwaresolutions%3ftopic=vmwaresolutions-veeam_proxies_vmware_backup_proxy).
    -   [VMware CDP proxy](file:////docs/vmwaresolutions%3ftopic=vmwaresolutions-veeam_proxies_vmware_cdp_proxy).
    -   [Object storage repositories](file:////docs/vmwaresolutions%3ftopic=vmwaresolutions-veeam_repo_obj_storage).
-   For CDP, an I/O filter needs to be installed on every VMware cluster where protected or recovered virtual machines will be hosted. See [Installing I/O Filter](https://helpcenter.veeam.com/docs/backup/vsphere/cdp_io_filter_install.html?ver=120){: external}.
-   Veeam recommends having at least two Veeam VMware backup or CDP proxies on each site to provide some level of redundancy. Replication performance will increase when additional proxies are added as the replication jobs then get distributed across the proxies. This pattern only shows the minimum components needed for a functional replication between the 2 regions, the exact number and types of Veeam proxies needed depend on each customer’s environment and requirements.
-   While any Windows or Linux bare metal server, virtual server instance or virtual machine can be leveraged for a backup or CDP proxy, this pattern assumes a Linux virtual machine are used. If your requirements are different consider a different operating system or hardware environment.
