---
copyright:
  years: 2023, 2024, 2025
lastupdated: "2025-10-27"

subcollection: vmware-cross-region-dr

keywords:
---
{{site.data.keyword.attribute-definition-list}}

# Storage design
{: #storage-design}

The following are storage design requirements and considerations for the VMware Disaster Recovery with Veeam pattern.

## Requirements
{: #requirements-storage-design}

The requirements for this pattern focuses on the data store at the recovery site for the replicas. Veeam Backup and Replication includes features for both backup and replication. While this pattern focuses on replication, some considerations for backup repositories are also documented.

For replication, particularly if vSAN is being used, make sure to properly size the disaster recovery (DR) site VMware deployment data stores so that they can accommodate the replicas.
{: note}

## Considerations for backup repositories
{: #backup-considerations}

The [Veeam Backup Capacity Calculator](https://www.veeam.com/calculators/simple/vbr/machines/){: external} can calculate the storage requirements that are needed for defined backup policies.

The bare metal all-in-one Veeam deployment option on {{site.data.keyword.Bluemix_notm}} comes with the following directly attached storage:

- 2x 1 TB database configuration drive in RAID1
- 2x 1 TB system drive in RAID1
- 8 drives in RAID6 providing configurable backup storage with a usable capacity that ranges from 12 to 72 TB

The VM and VSI deployment options come with the following network iSCSI storage:

- 100 GB system drive
- 2 to 12 TB backup storage

The bare metal Linux hardened repository comes with the following directly attached storage:

- 2 x 1 TB system drive in RAID1
- 10 drives in RAID6 providing configurable backup storage with a usable capacity that ranges from 16 to 96 TB

Veeam can use {{site.data.keyword.cos_full_notm}} buckets as target repositories in the following ways:

- As target repositories for backup jobs and backup copy jobs.
- As target repositories for backups of virtual and physical machines that are created by Veeam Agent for Microsoft Windows or Veeam Agent for Linux.
- As target repositories for backups of configuration database that is created by Veeam Backup and Replication.
- As a part of Performance Tier in a scale-out backup repository. Performance tier allows quick access to the stored data.
- As a part of Capacity Tier in a scale-out backup repository. Capacity tier allows the offloading of existing backup data directly to cloud-based object storage.

For more information, see [Object Storage Repositories](https://helpcenter.veeam.com/docs/backup/vsphere/object_storage_repository.html?ver=120){: external}.
