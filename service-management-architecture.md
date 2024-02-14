---
copyright:
  years: 2023
lastupdated: "2023-12-18"

subcollection: vmware-cross-region-dr

keywords:
---
# Architecture decisions for service management

{: \#service}

This will be updated later

## Architecture decisions for monitoring

{: \#monitoring}

| Architecture decision                                       | Requirement                                                                                              | Option                                                          | Decision                              | Rationale                                                                                                                                                                                                                                                 |
| ----------------------------------------------------------- | -------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------- | ------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Operational monitoring of cloud infrastructure and services | Monitor system health to detect issues that might impact the availability of the system and application. | Veeam ONE, IBM Cloud Monitoring, 3rd party monitoring tool      | Veeam ONE                             | A Veeam ONE license is included in the service and the application is installed on the Veeam Backup & Replication server during the automated deployment. Veeam ONE, is able to monitor the Veeam backup infrastructure as well as the VMware environment |
| Operational monitoring of applications                      | Monitor app health to detect issues that might impact the availability of the app.                       | IBM Cloud Monitoring, Instana (SaaS), 3rd party monitoring tool | IBM Cloud Monitoring + Instana (SaaS) | Instana is used along with IBM Cloud Monitoring to get more application performance metrics and automate application performance management. Instana provides data and actionable insights to monitor the applications and automate root-cause analysis.  |


|  |  |  |  |  |
| - | - | - | - | - |
|  |  |  |  |  |
|  |  |  |  |  |
|  |  |  |  |  |



|  |  |  |  |  |
| - | - | - | - | - |
|  |  |  |  |  |
|  |  |  |  |  |
