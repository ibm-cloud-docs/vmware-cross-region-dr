---
copyright:
  years: 2023
lastupdated: "2023-12-18"

subcollection: 

keywords:

---

# Architecture decisions for security

{: \#security-architecture}

The following are security architecture decisions for the VMware Disaster Recovery using Veeam.

| **Architecture decision**                 | **Requirement**                                                                                            | **Option**                                                                                                                                 | **Decision**                                       | **Rationale**                                                                                                                                                                                                                                                        |
|-------------------------------------------|------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Encryption at rest – backup and replicas  | Encrypt all protected VMs data (backup and replicas).                                                      | - Veeam encryption  - vSphere encryption  - vSAN encryption                                                                                | vSphere encryption                                 | Does not require vSAN, standard VMware vSphere capability                                                                                                                                                                                                            |
| Data encryption/privacy in transit        | Encrypt/protect the protected VMs data in transit as it it being replicated between the IBM Cloud regions  |   - Use of Veeam Network profiles  - replication of vSphere encryption protected VMs  - No encryption as connection considered as private  | No encryption as connection considered as private  | The replication between the IBM Cloud regions is done over IBM cloud backbone and account specific VRF and therefore considered as private  VMs already protected by vSphere encryption remain encrypted during replication  Avoids putting extra load on the ESXis  |
| Key Management Server                     | Manage the Keys used for encrypting the protected VMs storage                                              | IBM Key Protect  IBM Hyper Protect Crypto Services  IBM Guardium                                                                           | IBM Key Protect                                    | As a service cost effective offering using a shared (multi-tenant) FIPS 140-2 Level 3 certified hardware security modules (HSMs).                                                                                                                                    |
