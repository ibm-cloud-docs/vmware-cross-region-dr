---
copyright:
  years: 2023
lastupdated: "2024-01-31"

subcollection: vmware-cross-region-dr

keywords:
---

# Service management design
{: #service-design}

This pattern expands on the service management aspect for the Veeam for disaster recovery for VMware workloads.

Monitoring the usage and performance of the Veeam components and enabling the logging and alerting to DevOps tools are key aspects of the service management design. Monitoring a Veeam Replication and Continuous Data Protection (CDP) environment is crucial to ensure the health, performance, and reliability of the replication processes.

## Veeam Backup and Replication console
{: console-replication}

The Veeam Backup and Replication console are the primary user interface for managing Veeam Backup and Replication. By default, it's installed on the Veeam Backup and Replication server but you should consider installing it on your DevOps consoles.

From the DevOps console you can regularly check the Veeam Backup and Replication console for real-time information on replication jobs, and overall infrastructure health.
You can also monitor job sessions, completion status, and any warnings or errors reported by Veeam.

## Veeam ONE
{: #veeam-one}

Veeam ONE and its associated database are installed during the "all-in-one" deployment. This tool can be used to monitor Veeam Backup and Replication, VMware vSphere, and other environments. Veeam ONE provides visibility of the virtual and backup infrastructure:

* Manage, view, and interact with alarms and monitoring data.
* Analyze the performance of virtual and backup infrastructure objects.
* Track the efficiency of data protection in the virtual environment.
* Generate reports and administer monitoring settings.
* Speed up troubleshooting and quickly isolate root causes of performance issues before they become problems.

### Data protection monitoring
{: #data-monitoring}

Veeam ONE integrates with Veeam Backup and Replication to collect statistics from backup servers. You can track the latest status of data protection operations in the managed environment, receive immediate alarms whenever a potential problem can cause data loss, monitor performance of backup infrastructure components to optimize workloads, and plan the capacity of backup infrastructure resources. For more information, see [Veeam Backup &amp; Replication Monitoring](https://helpcenter.veeam.com/docs/one/monitor/backup_monitoring.html?ver=120){: external}.

### Virtual infrastructure monitoring
{: #virtual-infra-monitoring}

Veeam ONE discovers the virtual infrastructure and provides visibility of its health status and performance. Using predefined and custom alarms, performance charts, dashboards, reports, and an extensive knowledge base, you can always stay aware of the important events and eliminate potential problems in the virtual environment. For more information, see [VMware vSphere Monitoring](https://helpcenter.veeam.com/docs/one/monitor/vsphere_monitoring.html?ver=120){: external}.

## Service management considerations
{: #service-management-consider}

1. **Infrastructure monitoring:**
   * Monitor the underlying infrastructure components, including virtualization hosts, storage, and network devices to identify potential bottlenecks. Consider the use of the {{site.data.keyword.Bluemix_notm}} console. For more information, see [{{site.data.keyword.monitoringlong}}](/docs/cloud-infrastructure?topic=cloud-infrastructure-monitoring-iaas) or IBM Cloud services such as {{site.data.keyword.monitoringlong_notm}}. For more information, see [Monitoring for VMware vCenter Server deployments](/docs/monitoring?topic=monitoring-vmware-vcenter).
   * Use performance monitoring tools specific to your virtualization platform to track resource utilization. Review the use of Veeam ONE as your performance monitoring tool [VMware vSphere Monitoring](https://helpcenter.veeam.com/docs/one/monitor/vsphere_monitoring.html?ver=120){: external} or consider the optional add-on service [VMware Aria Operations and VMware Aria Operations for Logs on IBM Cloud overview](/docs/vmwaresolutions?topic=vmwaresolutions-vrops_overview)
2. **IBM Cloud Logs:**
   * Regularly review Veeam log files for any warning or error messages. Logs can provide detailed information about the health and performance of various components. For more information, see [Performing Veeam Log Analysis](https://helpcenter.veeam.com/docs/one/monitor/vbr_log_analysis.html?ver=120){: external}
   * Configure log retention and archiving to make sure that historical logs are available for troubleshooting and analysis.
   * Consider the use of IBM Cloud Logs. For more information, see [Getting started with IBM Cloud Logs](/docs/cloud-logs) and [Logging for VMware vCenter Server deployments](/docs/log-analysis?topic=log-analysis-vmware-vcenter).
   * Consider the optional add-on service [VMware Aria Operations and VMware Aria Operations for Logs on IBM Cloud overview](/docs/vmwaresolutions?topic=vmwaresolutions-vrops_overview).
3. **Event Logs and Syslog Integration:**
   * Integrate Veeam with centralized logging systems or syslog servers to consolidate and analyze logs across the entire CDP environment. For more information, see [Syslog Integration](https://helpcenter.veeam.com/docs/one/monitor/syslog_settings.html?ver=120){: external}.
   * Monitor Windows Event Logs for events related to Veeam and address any issues promptly. This is performed by using IBM Log analysis. For more information, see [Veeam ONE Server Settings](https://helpcenter.veeam.com/docs/one/monitor/server_settings.html?ver=120){: external} on how Veeam ONE can be integrated into your service management tools.
   * By default, Veeam stores its logs locally on the host where it is installed. For more information, see [Managing Logs](https://helpcenter.veeam.com/docs/backup/vsphere/logging.html?ver=120){: external} for a detailed list of logs location.
   * It is possible to configure an external syslog server where the Veeam events are sent.
   * With Veeam Backup and Replication, email notifications or SNMP traps can also be used for alerting or job monitoring. For more information, see [Specifying Email Notification Settings](https://helpcenter.veeam.com/docs/backup/vsphere/email_notification_settings.html?ver=120){: external} and [Specifying SNMP Settings](https://helpcenter.veeam.com/docs/backup/vsphere/snmp_settings.html?ver=120){: external}.
4. **Backup Repository Monitoring:**
   * Monitor the health and capacity of backup repositories. Make sure that there is sufficient space for storing backups and that repository performance meets expectations. For more information, see [Veeam Backup and Replication Performance Charts](https://helpcenter.veeam.com/docs/one/monitor/backup_charts.html?ver=120){: external}.
5. **Network Monitoring:**
   * Monitor network traffic between the primary and secondary sites to make sure that replication traffic is not impeded. For more information, see [Network Performance Chart](https://helpcenter.veeam.com/docs/one/monitor/backup_network.html?ver=120){: external}
   * Set up alerts for unusual network behavior or performance issues.
6. **Regular Audits and Reviews:**
   * Conduct regular audits of your Veeam configuration and performance to identify areas for optimization and enhancement.
   * Review reports and alerts to ensure that any issues are addressed in a timely manner. For more information, see [Veeam Backup &amp; Replication Summary Dashboards](https://helpcenter.veeam.com/docs/one/monitor/backup_dashboards.html?ver=120){: external}.
7. **User Activity Monitoring:**
   * Monitor user activities within the Veeam environment, such as changes to job configurations or backup policies to verify security and compliance. For more information, see [Audit Log](https://helpcenter.veeam.com/docs/one/monitor/audit_log_settings.html?ver=120){: external}.
