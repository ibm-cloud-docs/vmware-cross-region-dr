---
copyright:
  years: 2024
lastupdated: "2024-02-29"

subcollection: vmware-cross-region-dr

keywords:
---
{{site.data.keyword.attribute-definition-list}}

# Security design
{: #security-design}

The following table summarizes the nsecurity design architecture decisions for the Veeam pattern. Securing Veeam Backup and Continuous Data Protection (CDP) on VMware within the {{site.data.keyword.Bluemix_notm}} environment requires careful consideration of various aspects to ensure the confidentiality, integrity, and availability of data.

## Requirements
{: #requirements-security-design}

The requirements for the security aspect for the Veeam for disaster recovery for VMware workloads pattern focuses on the following:

- Providing encryption at rest for the replicas at the recovery site.
- Providing encryption or privacy for the replication between the {{site.data.keyword.Bluemix_notm}} regions.

## Security design considerations
{: #considerations-security-design}

| Security areas                       | Description                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Network security**           | **Isolation:** Ensure proper network isolation for the Veeam infrastructure components, such as the backup server, repository, and CDP proxy from unauthorized access.  \n **Firewall rules:** Implement strict firewall rules to control traffic between Veeam components and other systems within the IBM Cloud environment.                                                                           |
| **Access control**             | **Role-Based Access Control (RBAC):** Implement RBAC to restrict access to Veeam components based on job responsibilities, ensuring that only authorized personnel can configure, manage, and monitor the CDP solution.  \n **Authentication:** Enforce strong authentication mechanisms, such as multi-factor authentication (MFA), for accessing the Veeam management console and associated interfaces |
| **Data encryption**            | In-Transit Encryption: Enable encryption for data in transit between Veeam components and VMware infrastructure to prevent interception and tampering. Use secure communication protocols like TLS.  \n At-Rest Encryption: Implement encryption for data at rest on storage repositories to protect against unauthorized access to backup and CDP data.                                                              |
| **Backup repository security** | **Access controls:** Restrict access to the backup repository to only authorized personnel and systems. \n **Audit trails:** Enable audit logging to track access and modifications to the backup repository, providing a trail of actions for security monitoring.                                                                                                                                      |
| **Vulnerability management**   | **Regular updates:** Keep Veeam software and underlying systems up-to-date with the latest security patches and updates to address potential vulnerabilities.  \n **Scanning and testing:** Regularly perform vulnerability assessments and penetration testing on the Veeam infrastructure to identify and remediate security weaknesses.                                                                |
| **Compliance**                 | **Regulatory compliance:** Ensure that the Veeam CDP deployment within the IBM Cloud adheres to relevant industry regulations and compliance standards, such as GDPR, HIPAA, or any other applicable requirements.                                                                                                                                                                                          |
| **Data residency and privacy** | **Data residency policies:** Understand and comply with data residency requirements by configuring Veeam CDP to align with IBM Cloud's data residency policies. \n **Privacy considerations:** Address privacy concerns by implementing anonymization or pseudonymization of sensitive data within backups                                                                                               |
{: caption="Table 1. Architecture decisions for security design" caption-side="bottom"}