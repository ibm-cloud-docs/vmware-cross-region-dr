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

Once the RDP session has been established, the customer can connect to the local Veeam console (see [https://helpcenter.veeam.com/docs/backup/vsphere/logon_to_console.html?ver=120)](https://helpcenter.veeam.com/docs/backup/vsphere/logon_to_console.html?ver=120))

## Logs

Please add Logs that are used for Veeam

## Event Management
