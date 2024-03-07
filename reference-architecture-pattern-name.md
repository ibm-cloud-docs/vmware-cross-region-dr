---
copyright:
  years: 2023
lastupdated: "2024-02-24"

subcollection: vmware-cross-region-dr

keywords:
---
{{site.data.keyword.attribute-definition-list}}

# Deploy IBM Cloud resiliency design for Veeam on VMware

{: #deploy-veeam-arch}
{: toc-content-type="reference-architecture"}
{: toc-use-case="cloud"}
{: toc-version="1.0"}

This pattern describes the use of Veeam for a disaster recovery solution for VMware workloads where both the protected and recovery sites are in {{site.data.keyword.Bluemix_notm}}. Veeam was selected for the disaster recovery product because of the following:

- Veeam is an add-on additional service that can be ordered from the {{site.data.keyword.Bluemix_notm}} catalog.
- Veeam Replication is a technology that creates an exact copy of the protected VMware virtual machine at the recovery site. The replication maintains this copy in sync with the protected VM with Recovery Point Objective (RPO) of hours. Replication provides a minimum Recovery Time Objective (RTO) as the recovery replicas that are in a ready-to-start state. For more information, see [Replication](https://helpcenter.veeam.com/docs/backup/vsphere/replication.html?ver=120){: external}.
- Veeam Continuous Data Protection (CDP) is a technology that helps protect VMware virtual machines where data loss for seconds or minutes, not hours, is required. CDP also provides a minimum RTO as the CDP replicas that are in a ready-to-start state. For more information, see [Continuous Data Protection (CDP)](https://helpcenter.veeam.com/docs/backup/vsphere/cdp_replication.html?ver=120){: external}.
- Veeam supports VM encryption, and in this pattern, protected workloads must use data encryption as General Data Protection Regulation (GPDR), and other regulations requires Personally Identifiable Information (PII) or Sensitive Personal Information (SPI) data to be protected.

This pattern focuses on replication of virtual machines for only disaster recovery. However, veeam supports both backup and replication. Backup is not discussed in this pattern. {: important}

This pattern builds on Veeam best practice guidance:

- The Veeam Backup and Recovery server should be a virtual machine so that it can benefit from vSphere High Availability (HA) and can be restarted if hardware failure occurs.
- For replication, the Veeam Backup and Recovery server should be placed in the recovery site.
- Veeam VMware Backup and CDP proxies should be placed close to the data source as the data is not yet compressed or de-duplicated.

## Veeam Architecture details
{: #architecture-diagram}

The following image is the disaster recovery pattern architecture for VMware workloads on {{site.data.keyword.Bluemix_notm}} Classic (VCS).

![Veeam Disaster Recovery Architecture](image/VCS_Veeam_cross_region.drawio.svg){: caption="Figure 1. Veeam Disaster Recovery solution for VMware Workloads on {{site.data.keyword.Bluemix_notm}} Classic (VCS) architecture" caption-side="bottom"}

Review the key features of this pattern:

1. **IBM Cloud Infrastructure:**
   - Two VMware-based virtualized environments hosted on {{site.data.keyword.Bluemix_notm}}. Region 1 hosts the protected environment, and Region 2 hosts the recovery environment. Potentially, the recovery region can also host development and test workloads that can be sacrificed when a disaster recovery is started or tested.
   - Multiple ESXi bare metal servers forming a cluster hosting virtual machines.
2. **Veeam Backup and Replication Server:**
   - Responsible for managing the replication jobs.
   - Deployed onto a Microsoft Windows operating system.
   - Deployed within the VMware recovery environment as a virtual machine.
   - Veeam is deployed by using the [Simple Deployment](https://helpcenter.veeam.com/docs/backup/vsphere/simple.html?ver=120){: external} scenario that's also known as all-in-one.
   - The virtual machine deployment was selected to use the benefits from vSphere HA.
   - The recovery site was selected so that the server is available to quickly restore protected virtual machines.
3. **Veeam Repository:**
   - The backup repository is responsible for storing replication metadata of the Veeam VMware Replication proxies.
   - The backup repository stores replica metadata that contains information on the read data blocks. For more information, see [Backup Repository](https://helpcenter.veeam.com/docs/backup/vsphere/replication_components.html?ver=120#backup-repository){: external}.
   - Only the backup repository at the protected site is required.
   - In this pattern, the backup repository is installed on a Linux virtual machine.
   - Backup repositories can be hosted on Microsoft Windows or Linux operating systems.
   - The repository has a single network interface, one on an {{site.data.keyword.Bluemix_notm}} portable subnet that is used for proxies and on the {{site.data.keyword.Bluemix_notm}} Private VLAN - Primary (Management) to enable efficient traffic flow from the Veeam VMware Backup proxy to the backup replication.
   - For more information, see [VMware Backup Proxies](https://helpcenter.veeam.com/docs/backup/vsphere/backup_proxy.html?ver=120){: external}.
4. **Veeam Backup Proxy:**
   - The backup proxy is responsible for replication of virtual machines.
   - A minimum of one backup proxy per site is required, however, multiple backup proxies should be deployed for availability and scaling.
   - In this pattern backup proxies are installed on Linux virtual machines.
   - Backup proxies can be hosted on Microsoft Windows or Linux operating systems.
   - The proxies have two network interfaces; one on an {{site.data.keyword.Bluemix_notm}} portable subnet used for proxies and on the {{site.data.keyword.Bluemix_notm}} Private VLAN - Primary (Management) and a second on an {{site.data.keyword.Bluemix_notm}} portable subnet on the {{site.data.keyword.Bluemix_notm}} Private VLAN - Secondary (Storage/vMotion). This is to enable efficient traffic flow from the ESXi hosts' vmk0 interfaces to the proxies and the from the proxies to the remote proxies bypassing the firewalls.
   - For more information, see [VMware Backup Proxies](https://helpcenter.veeam.com/docs/backup/vsphere/backup_proxy.html?ver=120){: external}.
5. **Veeam VMware CDP Proxy:**
   - The VMware CDP backup proxy is responsible for replication of virtual machines using CDP.
   - VMware CDP backup proxies are only needed if RPO in seconds is needed.
   - A minimum of one VMware CDP proxy per site is required, however, multiple VMware CDP proxies should be deployed for availability and scaling.
   - In this pattern VMware CDP proxies are installed on Linux virtual machines.
   - VMware CDP proxies can be hosted on Microsoft Windows or Linux operating systems.
   - The proxies have two network interfaces; one on an {{site.data.keyword.Bluemix_notm}} portable subnet used for proxies and on the {{site.data.keyword.Bluemix_notm}} Private VLAN - Primary (Management) and a second on an {{site.data.keyword.Bluemix_notm}} portable subnet on the {{site.data.keyword.Bluemix_notm}} Private VLAN - Secondary (Storage/vMotion). This is to enable efficient traffic flow from the ESXi hosts' vmk0 interfaces to the proxies and the from the proxies to the remote proxies bypassing the firewalls.
   - For more information, see [VMware CDP Proxies](https://helpcenter.veeam.com/docs/backup/vsphere/cdp_proxy.html?ver=120){: external}
6. **I/O filter:**
   - You must install the I/O filter on each cluster to be able to use CDP.
   - The I/O filter reads and processes I/O operations in transit between the protected virtual machines and their underlying datastore and sends/receives data to/from the VMware CDP proxies.
   - The I/O filter leverages vSphere API for I/O filtering (VAIO).
   - For more information, see [I/O Filter](https://helpcenter.veeam.com/docs/backup/vsphere/cdp_io_filter_install.html?ver=120){: external}
7. **Veeam Backup Console:**
   - Console for configuring and monitoring replication jobs.
   - Installed by default on the Veeam Backup and Replication Server.
   - It is recommended that it is uninstalled from the Veeam Backup and Replication Server and installed on DevOps consoles.
   - For more information, see [Installing Veeam Backup and Replication Console](https://helpcenter.veeam.com/docs/backup/vsphere/install_console.html?ver=120){: external}
8. **Veeam ONE:**
   - Veeam ONE, part of the Veeam Availability Suite, provides visibility into Veeam-protected workloads.
   - Veeam ONE provides; monitoring, reporting, alerting, diagnostics with automated resolutions and infrastructure utilization and capacity planning.

## Design Scope
{: #design-scope}

The VMware Disaster Recovery solution using Veeam architecture covers [design considerations](/docs/vmware-cross-region-dr?topic=vmware-cross-region-dr-compute-design) and [architecture decisions](/docs/vmware-cross-region-dr?topic=vmware-cross-region-dr-compute-decisions) for the following aspects and domains (as defined in the [Architecture Framework](/docs/architecture-framework?topic=architecture-framework-intro)):

- **Compute:** Virtual Servers
- **Storage:** Primary Storage, Backup Storage
- **Networking:** Enterprise Connectivity, Segmentation and Isolation
- **Security:** Data Security, Identity and Access Management
- **Resiliency:** High Availability, Backup and Restore, Disaster recovery
- **Service Management:** Monitoring, Logging, Auditing/tracking, Alerting, Event management

![Architecture framework for Veeam deployment](image/heat-map-veeam.svg){: caption="Figure 2 Architecture framework for Veeam deployment on VMware {{site.data.keyword.Bluemix_notm}} IBM Cloud" caption-side="bottom"}

The Architecture Framework provides a consistent approach to design cloud solutions by addressing requirements across a set of "aspects" and "domains", which are technology-agnostic architectural areas that need to be considered for any enterprise solution. For more information, see [Introduction to the Architecture Framework](/docs/architecture-framework?topic=architecture-framework-intro) for more details.

### Design considerations
{: #arch-frame-design}

Consider the following when reviewing the pattern:

- Network connectivity from on-premises to the {{site.data.keyword.Bluemix_notm}} environments is considered as out of scope for this pattern.
- The Veeam solution available from {{site.data.keyword.Bluemix_notm}} VMware Solutions Catalog leverages Veeam Availability Suite 12 which consists of Veeam Backup and Replication and Veeam ONE.
- While the {{site.data.keyword.Bluemix_notm}} automation deploys the optional add-on Veeam service, it deploys it as an-all-in-one deployment scenario. While applicable for some use-cases it only provides the base for this disaster recovery pattern. Therefore, additional post deployment tasks are required, including; ordering additional private portable subnets, deploying virtual machines, deploying Veeam services onto ESXi hosts and virtual machines.
- The configuration of the Veeam Backup and Replication server should be configured for a scheduled backup. This pattern recommends using an {{site.data.keyword.Bluemix_notm}} Object Storage bucket.
- For more information on the Veeam components, review the following:
  - [Requirements and limitations for VMware backup proxies](https://helpcenter.veeam.com/docs/backup/vsphere/backup_proxy_requirements.html?ver=120).
  - [Transport Modes](https://helpcenter.veeam.com/docs/backup/vsphere/transport_modes.html?ver=120).
  - [VMware backup proxy](https://helpcenter.veeam.com/docs/backup/vsphere/backup_proxy.html?ver=120).
  - [VMware CDP proxy](https://helpcenter.veeam.com/docs/backup/vsphere/cdp_proxy.html?ver=120).
  - [Object storage repositories](https://helpcenter.veeam.com/docs/backup/vsphere/object_storage_repository.html?ver=120).
- For CDP, an I/O filter needs to be installed on every VMware cluster where protected or recovered virtual machines will be hosted. For more information, see [Installing I/O Filter](https://helpcenter.veeam.com/docs/backup/vsphere/cdp_io_filter_install.html?ver=120){: external}.
- Veeam recommends having at least two Veeam VMware backup or CDP proxies on each site to provide some level of redundancy. Replication performance will increase when additional proxies are added as the replication jobs then get distributed across the proxies. This pattern only shows the minimum components needed for a functional replication between the 2 regions, the exact number and types of Veeam proxies needed depend on each customer’s environment and requirements.
- While any Windows or Linux bare metal server, virtual server instance or virtual machine can be leveraged for a backup or CDP proxy, this pattern assumes a Linux virtual machine are used. If your requirements are different consider a different operating system or hardware environment.

## Requirements
{: #requirements}

Using the {{site.data.keyword.IBM_notm}} architecture framework, the following table describes the requirements for the disaster recovery pattern:

| **Aspect**   | **Requirement**                                                                                                                                                                                                          |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Compute            | Disaster Recovery for VMware workloads                                                                                                                                                                                         |
| Storage            | Storage to support Veeam components and to replicate the protected virtual machines                                                                                                                                            |
| Security           | Provide data encryption at rest and in transit                                                                                                                                                                                 |
| Resiliency         | Replicate VMware workloads from a protected site to a recovery site in a different region for failover of workloads in the event of failure in the protected site. Failover that meets the required RTO/RPO of the application |
| Service Management | Monitor the usage and performance of the Veeam components. Enable logging and alerting to DevOps tooling                                                                                                                       |
| Networking         | Provide private connectivity between workloads across protected and recovery sites                                                                                                                                             |
|                    |                                                                                                                                                                                                                                |
{: caption="Table 1. Veeam disaster recovery pattern for VMware Workloads on {{site.data.keyword.Bluemix_notm}} Classic (VCS) requirements " caption-side="bottom"}

## Components

{: #components}

Using the {{site.data.keyword.IBM_notm}} architecture framework, the following table describes how the pattern's components deliver against the requirements for the disaster recovery pattern:

| Aspect  | Component                                                               | How the component is used                                                                                                                                                                                                                                                                                                                            |
| ------------------ | --------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Data               | PostgreSQL, MS SQL Express                                                        | Veeam Backup and Replication uses a PostgreSQL database to store its configuration. Veeam ONE uses MS SQL Express for its database                                                                                                                                                                                                                               |
| Compute            | VMware virtual machine                                                            | The virtual machine option was selected for this pattern so that the Veeam Backup and replication application, VMware Backup and CDP proxies could leverage vSphere HA and be restarted due to any hardware issues                                                                                                                                               |
| Storage            | vSAN or {{site.data.keyword.Bluemix_notm}} File Storage, IBM Cloud Object Storage | vSAN or {{site.data.keyword.Bluemix_notm}} File Storage is used for the VMWare datastores in both the protected and recovery sites. For the Veeam Backup and replication configuration backup, {{site.data.keyword.Bluemix_notm}} Object Storage is used                                                                                                         |
| Networking         | {{site.data.keyword.Bluemix_notm}} backbone                                       | The {{site.data.keyword.Bluemix_notm}} private network is used for replicate traffic between the proxies in the regions. Control traffic between the Veeam Backup and Replication server and the data-plane Veeam components also traverses this network                                                                                                         |
| Resiliency         | Veeam replication and CDP                                                         | Veeam replication and CDP provide the resiliency of the VMware virtual machines to be protected and recovered. The resiliency of the Veeam data-plane components is accomplished by deploying multiple proxies. For the Veeam Backup and Replication server, vSphere HA and backups of the configuration database enables resiliency of the Veeam control-plane. |
| Service Management | Veeam ONE                                                                         | Veeam ONE, part of the Veeam Availability Suite, provides visibility into Veeam-protected workloads and provides; monitoring, reporting, alerting, diagnostics with automated resolutions and infrastructure utilization and capacity planning.                                                                                                                |

{: caption="Table 2. Veeam Disaster Recovery solution for VMware Workloads on {{site.data.keyword.Bluemix_notm}} Classic (VCS) components" caption-side="bottom"}
