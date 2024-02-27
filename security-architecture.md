---
copyright:
  years: 2023
lastupdated: "2023-12-18"

subcollection: 

keywords:

---

# Architecture decisions for security

{: #security-architecture}

The following are security architecture decisions for the VMware Disaster Recovery using Veeam.

| **Architecture decision**          | **Requirement**                                                                                 | **Option**                                                                                                                         | **Decision**    | **Rationale**                                                                                                                                                                                                                                                                                                                                                                                              |
|------------------------------------|-------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Data encryption/privacy in transit | Encrypt/protect the VMs data in transit as it is being replicated between the IBM Cloud regions | Use of Veeam Network profiles - replication of vSphere encryption protected VMs. No encryption as connection considered as private | Encrypted       | By default, Veeam Backup & Replication only encrypts network traffic transferred between public networks. Veeam network rules also allow the encryption of data transfer connections between Veeam data movers in private networks. Network traffic encryption is provided by TLS connection and configured as the part of global network traffic rules that are set for backup infrastructure components. |
| Key Management Server              | Manage the Keys used for encrypting the protected VMs storage                                   | IBM Key Protect IBM Hyper Protect Crypto Services IBM Guardium                                                                     | IBM Key Protect | As a service cost effective offering using a shared (multi-tenant) FIPS 140-2 Level 3 certified hardware security modules (HSMs).                                                                                                                                                                                                                                                                          |

{: caption="Table 1. Architecture decisions for security" caption-side="bottom"}
