---
copyright:
  years: 2023
lastupdated: "2023-12-18"

subcollection: pattern-vpc-vsi-multizone-resiliency

keywords:
---
# Service Management Design considerations

## Service management for Veeam Monitoring

The deployment of the Bare Metal server with the-all in-one Veeam solution installed on it is automated, however the initial configuration of the cross-region replication (mainly adding the necessary proxies, connecting the DR site vCenter/cluster and installing the I/O filter on the ESXi hosts) and the management of all the backup and replication operations are the responsibility of the customer or of its chosen service provider.

To access the Veeam console, the customer needs to connect using RDP to the bare metal server where it is deployed. This suppose an existing access to the IBM Cloud account via either VPN or Direct Link (on premises connectivity to the IBM Cloud environment is not in scope for this pattern).

## General Veeam Monitoring Considerations

Monitoring a Veeam Continuous Data Protection (CDP) environment is crucial to ensure the health, performance, and reliability of the backup and replication processes. Here are some guidance and best practices for monitoring a Veeam CDP environment:

1. **Veeam Backup & Replication Console:**

   * Regularly check the Veeam Backup & Replication console for real-time information on backup jobs, replication status, and overall infrastructure health.
   * Monitor job sessions, completion status, and any warnings or errors reported by Veeam.
2. **Infrastructure Performance Monitoring:**

   * Monitor the underlying infrastructure components, including virtualization hosts, storage arrays, and network devices, to identify potential bottlenecks.
   * Use performance monitoring tools specific to your virtualization platform to track resource utilization.
   * **This  is available via IBM Cloud monitoring console.**
3. **Log Analysis:**

   * Regularly review Veeam log files for any warning or error messages. Logs can provide detailed information about the health and performance of various components.
   * Configure log retention and archiving to ensure that historical logs are available for troubleshooting and analysis.
   * This is performed using IBM Log analysis
4. **Event Logs and Syslog Integration:**

   * Integrate Veeam with centralized logging systems or syslog servers to consolidate and analyze logs across the entire CDP environment.
   * Monitor Windows Event Logs for events related to Veeam and address any issues promptly.
   * This is performed using IBM Log analysis
5. **Backup Repository Monitoring:**

   * Monitor the health and capacity of backup repositories. Ensure that there is sufficient space for storing backups and that repository performance meets expectations.
6. **Network Monitoring:**

   * Monitor network traffic between the primary and secondary sites to ensure that replication traffic is not impeded.
   * Set up alerts for unusual network behavior or performance issues.
7. **Regular Audits and Reviews:**

   * Conduct regular audits of your Veeam CDP configuration and performance to identify areas for optimization and enhancement.
   * Review reports and alerts to ensure that any issues are addressed in a timely manner.
8. **User Activity Monitoring:**

   * Monitor user activities within the Veeam environment, such as changes to job configurations or backup policies, to ensure security and compliance.


## **Monitoring**

Basic monitoring of the Veeam all-in-one bare metal server and of the VSIs used as Veeam backup/CDP proxies is strongly recommended. This can be done manually using the IBM Cloud console or IBM Cloud native services such as IBM Cloud Monitoring (see https://cloud.ibm.com/docs/cloud-infrastructure?topic=cloud-infrastructure-monitoring-iaas#basic-monitoring).

Inside Veeam, email notifications or SNMP traps can also be used for alerting or job monitoting (see https://helpcenter.veeam.com/docs/backup/vsphere/email_notification_settings.html?ver=120 and https://helpcenter.veeam.com/docs/backup/vsphere/snmp_settings.html?ver=120).** **

## **Logging**

By default Veeam store its logs locally on the host where it is installed (see https://helpcenter.veeam.com/docs/backup/vsphere/logging.html?ver=120 for a detailed list of logs location).** **

It is possible to configure an external syslog server where the Veeam events will be sent (see https://helpcenter.veeam.com/docs/backup/vsphere/syslog_servers.html?ver=120).
