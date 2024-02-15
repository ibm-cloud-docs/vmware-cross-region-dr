---
copyright:
  years: 2024
lastupdated: "2024-02-06"

subcollection: vmware-cross-region-dr

keywords:
---

# Architecture decisions for service management

{: \#service}

The following are service management architecture decisions for the VMware Disaster Recovery using Veeam.

## Architecture decisions for monitoring

{: \#monitoring}

| **Architecture decision**                                   | **Requirement**                                                                                          | **Option**                                                      | **Decision**                          | **Rationale**                                                                                                                                                                                                                                             |
|-------------------------------------------------------------|----------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Operational monitoring of cloud infrastructure and services | Monitor system health to detect issues that might impact the availability of the system and application. | Veeam ONE, IBM Cloud Monitoring, 3rd party monitoring tool      | Veeam ONE                             | A Veeam ONE license is included in the service and the application is installed on the Veeam Backup & Replication server during the automated deployment. Veeam ONE, is able to monitor the Veeam backup infrastructure as well as the VMware environment |
| Operational monitoring of applications                      | Monitor app health to detect issues that might impact the availability of the app.                       | IBM Cloud Monitoring, Instana (SaaS), 3rd party monitoring tool | IBM Cloud Monitoring + Instana (SaaS) | Instana is used along with IBM Cloud Monitoring to get more application performance metrics and automate application performance management. Instana provides data and actionable insights to monitor the applications and automate root-cause analysis.  |

{: caption="Table 1. Architecture decisions for monitoring" caption-side="bottom"}

## Architecture decisions for logging

{: \#logging}

| **Architecture decision**                           | **Requirement**                                                                                               | **Option**                                                          | **Decision**                                 | **Rationale**                                                                                                                                               |
|-----------------------------------------------------|---------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Log monitoring of cloud infrastructure and services | Monitor operational logs to detect issues that might impact the availability of the system and application.   | IBM Cloud Logging, VMware Aria, 3rd party logging tool              | IBM Cloud Logging                            | IBM Cloud Logging collects operational logs from applications, platform resources, and infrastructure and provides interfaces to view and analyze all logs. |
| Log monitoring of applications                      | Monitor application operational logs to detect issues that might impact the availability of the applications. | IBM Cloud Logging, application logging tool, 3rd party logging tool | IBM Cloud Logging + application logging tool | Use the application logging tool to send application logs to IBM Cloud Logging and aggregate application-specific log details.                              |
| Log monitoring of databases                         | Monitor database logs to detect issues that might impact the availability of the database.                    | IBM Cloud Logging, database logging tools, 3rd party logging tool   | IBM Cloud Logging + database logging tool    | Use the database logging tools along with IBM Cloud Logging to get more database specific log information.                                                  |

{: caption="Table 2. Architecture decisions for logging" caption-side="bottom"}

## Architecture decisions for alerting

{: \#alerting}

| **Architecture decision** | **Requirement**                                                                                                                       | **Option**                                                        | **Decision**                                                      | **Rationale**                                                                                                                                                                                                                                                         |
|---------------------------|---------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------|-------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Operational alerts        | Provide a mechanism to identify and send notifications about operational issues that are found across application and infrastructure. | IBM Cloud Monitoring + IBM Cloud Logging + Event Notifications    | IBM Cloud Monitoring + IBM Cloud Logging + Event Notifications    | IBM Cloud Monitoring and IBM Cloud Logging support the configuration of alerts to detect operational issues and send notifications to targeted channels. Event Notifications are used to route the alert events to service destinations to automate response actions. |
| Audit alerts              | Provide a mechanism to identify and send notifications about issues that are found in audit logs.                                     | IBM Cloud Activity Tracker + IBM Monitoring + Event Notifications | IBM Cloud Activity Tracker + IBM Monitoring + Event Notifications | IBM Activity Tracker supports the configuration of alerts to detect audit issues and send notifications to targeted channels. Event Notifications are used to route the alert events to service destinations to automate response actions.                            |

{: caption="Table 3. Architecture decisions for alerting" caption-side="bottom"}
