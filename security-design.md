---
copyright:
  years: 2023
lastupdated: "2023-12-26"

subcollection: pattern-sap-on-vpc

keywords:
---

{{site.data.keyword.attribute-definition-list}}

# Security design

{: \#security-design}

Securing Veeam Backup and Continuous Data Protection (CDP) on VMware within the IBM Cloud environment requires careful consideration of various aspects to ensure the confidentiality, integrity, and availability of data. Here are key security requirements and best practices:

1.  **Network Security:**
    1.  **Isolation:** Ensure proper network isolation for the Veeam infrastructure components, such as the backup server, repository, and CDP proxy, from unauthorized access.
    2.  **Firewall Rules:** Implement strict firewall rules to control traffic between Veeam components and other systems within the IBM Cloud environment.
2.  **Access Control:**
    1.  **Role-Based Access Control (RBAC):** Implement RBAC to restrict access to Veeam components based on job responsibilities, ensuring that only authorized personnel can configure, manage, and monitor the CDP solution.
    2.  **Authentication:** Enforce strong authentication mechanisms, such as multi-factor authentication (MFA), for accessing the Veeam management console and associated interfaces.
3.  **Data Encryption:**
    1.  **In-Transit Encryption:** Enable encryption for data in transit between Veeam components and VMware infrastructure to prevent interception and tampering. Use secure communication protocols like TLS.
    2.  **At-Rest Encryption:** Implement encryption for data at rest on storage repositories to protect against unauthorized access to backup and CDP data.
4.  **Backup Repository Security:**
    1.  **Access Controls:** Restrict access to the backup repository to only authorized personnel and systems.
    2.  **Audit Trails:** Enable audit logging to track access and modifications to the backup repository, providing a trail of actions for security monitoring.
5.  **Vulnerability Management:**
    1.  **Regular Updates:** Keep Veeam software and underlying systems up-to-date with the latest security patches and updates to address potential vulnerabilities.
    2.  **Scanning and Testing:** Regularly perform vulnerability assessments and penetration testing on the Veeam infrastructure to identify and remediate security weaknesses.
6.  **Compliance:**
    1.  **Regulatory Compliance:** Ensure that the Veeam CDP deployment within the IBM Cloud adheres to relevant industry regulations and compliance standards, such as GDPR, HIPAA, or any other applicable requirements.
7.  **Data Residency and Privacy:**
    1.  **Data Residency Policies:** Understand and comply with data residency requirements by configuring Veeam CDP to align with IBM Cloud's data residency policies.
    2.  **Privacy Considerations:** Address privacy concerns by implementing anonymization or pseudonymization of sensitive data within backups.

By addressing these security requirements, organizations can establish a robust and resilient Veeam CDP deployment on VMware within the IBM Cloud environment, safeguarding critical data and ensuring the integrity of their backup and recovery processes.

**Requirements**

-   Provide encryption at rest for both backup and replication
-   Provide encryption or privacy for the replication between the IBM Cloud regions

**Encryption at rest for backup/replicas**

Several options are available for ensuring the workloads backups and replicas are encrypted.

**Native Veeam encryption**

Veeam supports two types of encryption for backup operations:

-   job level encryption (applies only to a specific backup job)
-   Capacity tier encryption (“data uploaded to object storage”), specific to scale out backup repository

The two types of encryption can be combined. Encryption is not retroactive, and if Veeam Backup Enterprise Backup Manager is not used, it will not be possible to restore data from encrypted backups without the password.

**vSphere encryption**

Veeam supports backing up virtual machines encrypted with vSphere encryption, the only requirement for this is to have a Key Management Server already configured (which is a pre-requisite for vSphere encryption).

**KMS**

It is possible (and strongly recommended) to add a KMS (Key Management Server) server to the Veeam solution. Note that a KMS is needed for encrypted VM replication, without a KMS configured, the encrypted VMs will still be replicated, but the replica will be unencrypted.

Once a KMS has been added, Veeam requests the KMS server to generate an asymmetric KMS key for the required job or repository.

The generated public key is stored in the Veeam Backup & Replication configuration database and used for data encryption while the private key is stored on the KMS server and used for data decryption.

KMS server options on IBM Cloud:

-   IBM Cloud Key Protect (as a service shared HSM)
-   IBM Cloud Hyper Protect Crypto Service (as a service dedicated hardware HSM)
-   IBM Security Guardium Key Lifecycle Manager (GKLM) 4.1.1.0 and later (Keep?)

![](image/7d177cdc205c05335550eb30d891d64a.png)

**To replicate/replace**

**Encryption in transit for replication**

Veeam supports encryption during replication using network traffic rules.

In this pattern, replication happens between two IBM Cloud regions leveraging IBM Cloud backbone and VRF. As a result the traffic is considered as being private and isolated and we do not consider encryption as being necessary.

In cases where encryption in transit is a mandatory requirement, the use of Veeam network traffic rules or the replication of vSphere encryption protected VMs would be the options.
