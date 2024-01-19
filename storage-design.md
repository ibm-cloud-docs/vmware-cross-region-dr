---
copyright:
  years: 2023
lastupdated: "2023-12-26"

subcollection: vmware-cross-region-dr

keywords:
---
{{site.data.keyword.attribute-definition-list}}

# Storage design

{: \#storage-design}

The Veeam solution provides both backup and replication capability. Storage capacity is naturally especially important for the backup aspect.

The Veeam calculator ([https://calculator.veeam.com/vbr/](https://calculator.veeam.com/vbr/)) can guide in properly sizing the storage for backup.

The bare metal all-in-one Veeam deployment option on IBM Cloud comes with the following directly attached storage:

- 2x 1TB database config drives in RAID1
- 2x 1TB system drives in RAID1
- 8 drives in RAID6 providing configurable backup storage with a useable capacity ranging from 12 to 72 TB

The VM and VSI deployment options come with the following network iSCSI storage:

- 100 GB system drive
- 2 to 12 TB backup storage

**Scale-out backup repository**

For long term retention of backups (offload backups) or redundancy (copying backups), it is possible to use IBM Cloud Object Storage through Veeam's scale-out backup repository functions (see [https://cloud.ibm.com/docs/vmwaresolutions?topic=vmwaresolutions-icos_ordering](https://cloud.ibm.com/docs/vmwaresolutions?topic=vmwaresolutions-icos_ordering)).


**Replication**

Note: For replication, an important consideration, particularly if vSAN is being used, is to properly size the DR site VMware deployment datastores so that they can accommodate the replicas.
