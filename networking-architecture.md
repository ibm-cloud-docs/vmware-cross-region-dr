---
copyright:
  years: 2023
lastupdated: "2023-12-18"

subcollection: vmware-cross-region-dr

keywords:
---

{{site.data.keyword.attribute-definition-list}}

# Architecture decisions for networking

{: \#networking-architecture}

The following tables summarize the networking architecture decisions for the Veeam pattern.

| **Architecture decision**                        | **Requirement**                                                                 | **Option**                                               | **Decision**              | **Rationale**                                                                                                                                                                                                                   |
|--------------------------------------------------|---------------------------------------------------------------------------------|----------------------------------------------------------|---------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cross-region connectivity                        | Provide connectivity between the two IBM regions for the replication component  | IBM Cloud backbone + VRF  Gateway appliances + GRE over  | IBM Cloud backbone + VRF  | Dependent on the type of deployment for the Veeam appliance in the production site and for the Veeam proxy in the DR site                                                                                                       |
| Connectivity to the scale-out backup repository  | Provide connectivity to COS in case a scale-out backup repository is needed     | HTTP proxy  IBM Cloud Service endpoint                   |                           | IBM cloud documentation mentions the need for an HTTP proxy (to validate the TLS certificate)..is it really needed?  Ideally use only a service endpoint to keep the connection private and optimize costs (no egress traffic)  |
