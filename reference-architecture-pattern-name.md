---
copyright:
years: 2023
lastupdated: "2023-12-26"
subcollection: vmware-cross-region-dr
---
# VMware Disaster Recovery on IBM Cloud using Veeam.

Reference architecture using Veeam for a DR solution for VMware workloads at source and target locations.

- Supported by delivery and orderable via Cloud Catalog with near real-time RPO, RTO.
- Protected workloads in DR zone must support Data Encryption. GPDR and other regulated markets requires PI/SPI data to be protected according to the laws.

## Architecture Diagram

{: \#architecture-diagram}

Here is the Architecture overview

Key Components of Veeam on IBM Cloud

1. **IBM Cloud Infrastructure:**

* VMware-based virtualized environment hosted on IBM Cloud.
* Multiple ESXi hosts forming a cluster for running virtual machines.

2. **Veeam Backup & Replication Server:**
   * Deployed within the VMware environment as a virtual machine.
   * Responsible for managing backup and replication jobs.
3. **Veeam Backup Repository:**
   * A dedicated storage location for storing backup files.
   * Can be implemented as an IBM Cloud Object Storage or a high-performance block storage solution.
4. **Veeam Backup Proxy:**
   * Installed on a separate virtual machine.
   * Facilitates data transfer between VMware infrastructure and the Veeam Backup & Replication server.
5. **Veeam Backup Console:**
   * Web-based console for configuring and monitoring backup jobs.
   * Accessible from administrators' workstations.
6. **Continuous Data Protection (CDP) Server:**
   * Deployed as a virtual appliance.
   * Ensures real-time replication of VMs for near-zero RPO (Recovery Point Objective).

![A screenshot of a computer diagram Description automatically generated](image/41a6308e99bcbe0425b6f93666405785.png)

Figure 1. Veeam Disaster Recovery solution for VMware Workloads on IBM Cloud Classic (VCS) architecture

Short overview of architecture here

![Backup Infrastructure for Replication](image/a8ebce1fe013d185fed25ff52394ffdd.png)

To adapt to IBM Cloud/recreate (remove the wan accelerators) – “standard” replication architecture.

![How Replication Works](image/43a4ce9bda14c2ec5f8f496fab2754a1.png)

To adapt to IBM Cloud/recreate – “standard” replication architecture

![Backup Infrastructure for CDP](image/581b404316a0c8da7f850aee4ffed86f.png)

To adapt/recreate – CDP architecture.

The Veeam solution available from IBM Cloud VMware Solutions catalog is based on Veeam Backup and Replication 12 and Veeam Availability Suite 12.

It enables the backup of VMware workloads as well as their replication between different ESXi hosts/clusters/environments.

List of things needed components on primary/secondary

## Design scope

{: \#design-scope}

Following the [Architecture Framework](https://test.cloud.ibm.com/docs-draft/architecture-framework?topic=architecture-framework-taxonomy), pattern-name covers design considerations and architecture decisions for the following aspects and domains:

- **Compute:** Bare Metal and Virtual infrastructure
- **Storage:** Primary, Backup, Archive and Migration
- **Networking:** Enterprise Connectivity, Edge Gateways, Segmentation and Isolation, Cloud Native Connectivity and Load Balancing
- **Security:** Data, Identity and Access Management, Infrastructure and Endpoint, Threat Detection and Response
- **Resiliency:** Backup and Restore, Disaster Recovery, High Availability
- **Service Management:** Monitoring, Logging, Alerting, Management/Orchestration

The Architecture Framework, described in [Introduction to the Architecture Framework](https://cloud.ibm.com/docs/architecture-framework?topic=architecture-framework-intro), provides a consistent approach to design cloud solutions by addressing requirements across a pre-defined set of aspects and domains, which are technology-agnostic architectural areas that need to be considered for any enterprise solution. It can be used as a guide to make the necessary design and component choices to ensure you have considered applicable requirements for each aspect and domain. After you have identified the applicable requirements and domains that are in scope, you can evaluate and select the best fit for purpose components for your enterprise cloud solution.

The Figure 3 shows the domains that are covered in this solution.

![A diagram of a computer network Description automatically generated](./image3.png)

Figure 3 design scope

## Requirements

{: \#requirements}

The following represents a baseline set of requirements which we believe are applicable to most clients and critical to successful SAP deployment.

| Aspect                                                                                                            | Requirement                                                                                                                                                                                                                                                                                                                       |
| ----------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Network                                                                                                           | Enterprise connectivity to customer data center(s) to provide access to applications from on-prem                                                                                                                                                                                                                                 |
|                                                                                                                   | Map and convert existing customer SAP Network functionality into IBM Cloud and VPC networking services                                                                                                                                                                                                                            |
|                                                                                                                   | Migrate/Redeploy customer IP addressing scheme within the IBM Cloud environment                                                                                                                                                                                                                                                   |
|                                                                                                                   | Provide network isolation with the ability to segregate applications based on attributes such as data classification, public vs internal apps and function                                                                                                                                                                        |
| Security                                                                                                          | Provide data encryption in transit and at rest                                                                                                                                                                                                                                                                                    |
|                                                                                                                   | Migrate customer IDS/IAM Services to target IBM Cloud environment                                                                                                                                                                                                                                                                 |
|                                                                                                                   | Retain the same firewall rulesets across existing DCs                                                                                                                                                                                                                                                                             |
|                                                                                                                   | Firewalls must be restrictively configured to provide advanced security features and prevent all traffic, both inbound and outbound, except that which is specifically required, documented, and approved, and include IPS/IDS services                                                                                           |
| Resiliency                                                                                                        | Multi-site capability to support a disaster recovery strategy and solution leveraging IBM Cloud infrastructure DR capabilities                                                                                                                                                                                                    |
|                                                                                                                   | Provide backups for data retention                                                                                                                                                                                                                                                                                                |
|                                                                                                                   | RTO/RPO = 4 hours/15 minutes; Rollback to original environments should occur no later than specified RTOs                                                                                                                                                                                                                         |
|                                                                                                                   | 99.95 Availability                                                                                                                                                                                                                                                                                                                |
|                                                                                                                   | Backups                                                                                                                                                                                                                                                                                                                           |
|                                                                                                                   | - Prod: Daily Full, logs per SAP product standard, 30 days retention time\\n - Non-Prod: Weekly full, logs per SAP product standard, 14 days retention time                                                                                                                                                                       |
| Service management                                                                                                | Provide Health and System Monitoring with ability to monitor and correlate performance metrics and events and provide alerting across applications and infrastructure                                                                                                                                                             |
|                                                                                                                   | Ability to diagnose issues and exceptions and identify error sources                                                                                                                                                                                                                                                              |
|                                                                                                                   | Automate management processes to keep applications and infrastructure secure, up to date, and available                                                                                                                                                                                                                           |
| Other                                                                                                             | Migrate SAP workloads from existing data center to IBM Cloud VPC                                                                                                                                                                                                                                                                  |
|                                                                                                                   | Customer's SAP systems and applications run on NetWeaver (application) & HANA (DB), AnyDB or S/4 HANA                                                                                                                                                                                                                             |
|                                                                                                                   | Provide an Image Replication migration solution that will minimize disruption during cut-over                                                                                                                                                                                                                                     |
|                                                                                                                   | Cloud infrastructure for the proposed IAAS solution must be SAP Certified                                                                                                                                                                                                                                                         |
|                                                                                                                   | IBM Cloud IaaS will be deployed to support SAP and surrounding non-SAP workloads                                                                                                                                                                                                                                                  |
|                                                                                                                   | Customer does not want to adopt[RISE](https://www.ibm.com/consulting/rise-with-sap?utm_content=SRCWW&p1=Search&p4=43700077624079785&p5=e&gclid=EAIaIQobChMIr9bRlt7LgQMVJdHCBB0cewwcEAAYASAAEgIVgfD_BwE&gclsrc=aw.ds) at this time but wants to consider Cloud deployment solution that would facilitate a future RISE transformation |
| {: caption="Table 1. Pattern requirements" caption-side="bottom"}\<!-- each table MUST have a caption attribute\> |                                                                                                                                                                                                                                                                                                                                   |
