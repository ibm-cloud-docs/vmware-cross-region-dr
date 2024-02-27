---
copyright:
  years: 2023
lastupdated: "2023-12-26"

subcollection: <vmware-cross-region-dr>

keywords:
---
{{site.data.keyword.attribute-definition-list}}

# Storage design

{: #storage-design}

This pattern expands on the storage aspect of the IBM Architecture framework in respect of the Veeam for disaster recovery for VMware workloads pattern.

## Requirements

The requirements for the storage aspect for the Veeam for disaster recovery for VMware workloads pattern focuses on the datastore required at the recovery site for the replicas.

Veeam Backup & Replication has features for both backup and replication. While this pattern focuses on replication, some considerations for backup repositories are also documented below.

## Recovery site datastore

Note: For replication, an important consideration, particularly if vSAN is being used, is to properly size the DR site VMware deployment datastores so that they can accommodate the replicas.

## Considerations for backup repositories

The [Veeam Backup Capacity Calculator](https://calculator.veeam.com/vbr/){: external} can calculate the storage requirements needed for defined backup policies.

The bare metal all-in-one Veeam deployment option on IBM Cloud comes with the following directly attached storage:

- 2x 1TB database config drives in RAID1
- 2x 1TB system drives in RAID1
- 8 drives in RAID6 providing configurable backup storage with a useable capacity ranging from 12 to 72 TB

The VM and VSI deployment options come with the following network iSCSI storage:

- 100 GB system drive
- 2 to 12 TB backup storage

The bare metal Linux hardened repository comes with the following directly attached storage:

- 2 x 1TB system drives in RAID1
- 10 drives in RAID6 providing configurable backup storage with a useable capacity ranging from 16 to 96 TB

Veeam can use IBM Cloud Object Storage buckets as target repositories in the following ways:

- As target repositories for backup jobs and backup copy jobs.
- As target repositories for backups of virtual and physical machines created by Veeam Agent for Microsoft Windows or Veeam Agent for Linux.
- As target repositories for backups of configuration database created by Veeam Backup & Replication.
- As a part of Performance Tier in a scale-out backup repository. Performance tier allows to quickly access the stored data.
- As a part of Capacity Tier in a scale-out backup repository. Capacity tier allows the offloading of existing backup data directly to cloud-based object storage.

See [Object Storage Repositories](https://helpcenter.veeam.com/docs/backup/vsphere/object_storage_repository.html?ver=120){: external}.
