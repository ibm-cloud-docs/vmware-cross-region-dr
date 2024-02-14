---
copyright:
  years: 2023
lastupdated: "2024-01-31"

subcollection: vmware-cross-region-dr

keywords:
---
# Service management design

This document expands on the service management aspect of the IBM Architecture framework in respect of the Veeam for disaster recovery for VMware workloads pattern.

The requirements for the compute aspect for the Veeam for disaster recovery for VMware workloads pattern focuses on the following:

- Monitor the usage and performance of the Veeam components.
- Enable logging and alerting to DevOps tooling.

Monitoring a Veeam Replication and Continuous Data Protection (CDP) environment is crucial to ensure the health, performance, and reliability of the replication processes. Here are some guidance and best practices for monitoring a Veeam CDP environment.

## Veeam Backup & Replication Console

The Veeam Backup & Replication console is the primary user interface for managing Veeam Veeam Backup & Replication. By default it is installed on the Veeam Backup & Replication server but you should consider installing it on your DevOps consoles. Via the console:

* Regularly check the Veeam Backup & Replication console for real-time information on replication jobs, and overall infrastructure health.
* Monitor job sessions, completion status, and any warnings or errors reported by Veeam.

## Veeam ONE

Veeam ONE and its associated database is installed during the "all-in-one" deployment. This tool can be used to monitoring Veeam Backup & Replication, VMware vSphere and other environments. Veeam ONE provides  visibility of the virtual and backup infrastructure and allows you to:

* Manage, view and interact with alarms and monitoring data.
* Analyze performance of virtual and backup infrastructure objects.
* Track the efficiency of data protection in the virtual environment
* Generate reports and administer monitoring settings
* Speed up troubleshooting and quickly isolate root causes of performance issues before they become problems.

Data protection monitoring - Veeam ONE integrates with Veeam Backup & Replication to collect real-time statistics from backup servers. You can track the latest status of data protection operations in the managed environment, receive immediate alarms whenever a potential problem can cause data loss, monitor performance of backup infrastructure components to optimize workloads, and plan capacity of backup infrastructure resources. See [Veeam Backup &amp; Replication Monitoring](https://helpcenter.veeam.com/docs/one/monitor/backup_monitoring.html?ver=120){: external}.

Virtual infrastructure monitoring - Veeam ONE discovers the virtual infrastructure and provides visibility of its health status and performance. Using predefined and custom alarms, performance charts, dashboards, reports, and an extensive knowledge base, you can always stay aware of the important events and eliminate potential problems in the virtual environment. See [VMware vSphere Monitoring](https://helpcenter.veeam.com/docs/one/monitor/vsphere_monitoring.html?ver=120){: external}.

## Service management considerations

Some key considerations for service management are listed below.

1. **Infrastructure monitoring:**

   * Monitor the underlying infrastructure components, including virtualization hosts, storage, and network devices, to identify potential bottlenecks. Consider the use of the IBM Cloud console see [IBM Cloud Monitoring](/docs/cloud-infrastructure?topic=cloud-infrastructure-monitoring-iaas) or IBM Cloud services such as IBM Cloud Monitoring, see [Monitoring for VMware vCenter Server deployments](/docs/monitoring?topic=monitoring-vmware-vcenter).
   * Use performance monitoring tools specific to your virtualization platform to track resource utilization. Review the use of Veeam ONE as your performance monitoring tool, see [VMware vSphere Monitoring](https://helpcenter.veeam.com/docs/one/monitor/vsphere_monitoring.html?ver=120){: external} or cnsider the optional add-on service [VMware Aria Operations and VMware Aria Operations for Logs on IBM Cloud overview](/docs/vmwaresolutions?topic=vmwaresolutions-vrops_overview)
2. **Log Analysis:**

   * Regularly review Veeam log files for any warning or error messages. Logs can provide detailed information about the health and performance of various components. See [Performing Log Analysis](https://helpcenter.veeam.com/docs/one/monitor/vbr_log_analysis.html?ver=120){: external}
   * Configure log retention and archiving to ensure that historical logs are available for troubleshooting and analysis.
   * Consider the use of IBM Log analysis. See [Getting started with IBM Log Analysis](/docs/log-analysis?topic=log-analysis-getting-started). See [Logging for VMware vCenter Server deployments](/docs/log-analysis?topic=log-analysis-vmware-vcenter).
   * Consider the optional add-on service [VMware Aria Operations and VMware Aria Operations for Logs on IBM Cloud overview](/docs/vmwaresolutions?topic=vmwaresolutions-vrops_overview).
3. **Event Logs and Syslog Integration:**

   * Integrate Veeam with centralized logging systems or syslog servers to consolidate and analyze logs across the entire CDP environment. See [Syslog Integration](https://helpcenter.veeam.com/docs/one/monitor/syslog_settings.html?ver=120){: external}.
   * Monitor Windows Event Logs for events related to Veeam and address any issues promptly.
   * This is performed using IBM Log analysis.
   * See [Veeam ONE Server Settings](https://helpcenter.veeam.com/docs/one/monitor/server_settings.html?ver=120){: external} on how Veeam ONE can be integrated into your service management tooling.
   * By default Veeam stores its logs locally on the host where it is installed. See [Managing Logs](https://helpcenter.veeam.com/docs/backup/vsphere/logging.html?ver=120){: external} for a detailed list of logs location.
   * It is possible to configure an external syslog server where the Veeam events will be sent (see https://helpcenter.veeam.com/docs/backup/vsphere/syslog_servers.html?ver=120).
   * With Veeam Backup & Replication, email notifications or SNMP traps can also be used for alerting or job monitoring. See [Specifying Email Notification Settings](https://helpcenter.veeam.com/docs/backup/vsphere/email_notification_settings.html?ver=120){: external} and [Specifying SNMP Settings](https://helpcenter.veeam.com/docs/backup/vsphere/snmp_settings.html?ver=120){: external}.
4. **Backup Repository Monitoring:**

   * Monitor the health and capacity of backup repositories. Ensure that there is sufficient space for storing backups and that repository performance meets expectations. See [Veeam Backup &amp; Replication Performance Charts](https://helpcenter.veeam.com/docs/one/monitor/backup_charts.html?ver=120){: external}.
5. **Network Monitoring:**

   * Monitor network traffic between the primary and secondary sites to ensure that replication traffic is not impeded. See [Network Performance Chart](https://helpcenter.veeam.com/docs/one/monitor/backup_network.html?ver=120){: external}
   * Set up alerts for unusual network behavior or performance issues.
6. **Regular Audits and Reviews:**

   * Conduct regular audits of your Veeam configuration and performance to identify areas for optimization and enhancement.
   * Review reports and alerts to ensure that any issues are addressed in a timely manner. See [Veeam Backup &amp; Replication Summary Dashboards](https://helpcenter.veeam.com/docs/one/monitor/backup_dashboards.html?ver=120){: external}.
7. **User Activity Monitoring:**

   * Monitor user activities within the Veeam environment, such as changes to job configurations or backup policies, to ensure security and compliance. See [Audit Log](https://helpcenter.veeam.com/docs/one/monitor/audit_log_settings.html?ver=120){: external}.
