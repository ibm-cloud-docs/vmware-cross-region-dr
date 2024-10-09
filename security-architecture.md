---
copyright:
  years: 2023, 2024
lastupdated: "2024-03-01"

subcollection: 

keywords:

---

# Architecture decisions for security
{: #security-architecture}

The following are security architecture decisions for the VMware Disaster Recovery using Veeam.

| Architecture decision         | Requirement                                                                              | Option                                                                                                                         | Decision    | Rationale                                                                                                                                                                                                                                                                                                                                                                                              |
|------------------------------------|-------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Data encryption and privacy in transit | Encrypt and protect the VMs data in transit as it is replicated between the IBM Cloud regions | Use of Veeam Network profiles: replication of vSphere encryption protected VMs. No encryption as connection is considered as private | Encrypted       | By default, Veeam Backup and Replication only encrypt network traffic that is transferred between public networks. Veeam network rules also allow the encryption of data transfer connections between Veeam data movers in private networks. Network traffic encryption is provided by TLS connection and configured as the part of global network traffic rules that are set for backup infrastructure components. |
| Key Management Server              | Manage the keys that are used for encrypting the protected VMs storage                                   | * IBM Key Protect \n * IBM Hyper Protect Crypto Services \n * IBM Guardium                                                                     | IBM Key Protect | As a service, cost-effective offering by using a shared (multi-tenant) FIPS 140-2 Level 3 certified hardware security modules (HSMs).                                                                                                                                                                                                                                                                          |

{: caption="Architecture decisions for security" caption-side="bottom"}
