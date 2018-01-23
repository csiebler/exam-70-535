# Azure Storage Overview

Available in all regions

* Blob
  * REST-based object storage
  * Types: Block Blobs (sequential I/O), Page Blobs (random R/W), Append Blobs

* Tables
  * NoSQL store

* Queues
  * Queues for application decoupling

* Files
  * File Shares
  * SMB and RESTful Access
  * Lift & Shift

* Disks
  * Persistent disks for Azure (Standard, Premium)

Storage Accounts

General Purpose
* Tables, Queues, Files, Blob, and Disks
* Standard performance tier: for all services, uses HDDs in backend
* Premium performance tier: only for disks, uses SSDs in backend
* Cannot auto-migrate from one tier to the other

Blob
* For unstructured data, hot or cool

Storage Replication Options
* LRS - Locally redundant storage, 3 copies within single facility
* ZRS - Zone redudant storage, 3 copies within 2-3 facilities (either single or two regions)
* GRS - Geo redundant storage, 6 copies (3 in primany region, and 3 in secondary region, 100's of miles away)
* RA-GRS - Read access geo redundant storage, 6 copies (same as GRS, but allows read access from secondary region)
* Can be changed afterwards, except for ZRS

Planning Storage on Azure

For IaaS
* Disks
  * Standard, Premium
  * Managed (auto-replicated) or unmanaged
* Files (SMB, RESTful, Lift & Shift)

For PaaS
* Blobs (Block, Page, Append Blobs)
  * Often used for logging by other Azure services
* Tables (auto-scaling NoSQL, PB, fast key/value lookups)
* Queues (reliable and scalable, message visibility timeout)

Billing
* Pay for data going out, transactions, storage used (standard is what you use, premium is what you provision)
* Free to put data in
* Data transfer between service and storage is free within region
* Storage analytics costs too

Storage Account
* Globally unique Namespace for storing and accessing data
* Attributes: kind (general purpose or blob), performance (standard or premium), replication, encrpytion
* Billed as a group, limit per account 500TB, isolation, performance
* Can be limited to certain virtual networks

```
$ az storage account create --location westeurope --resource-group learning --sku Standard_LRS --name csteststorage

```

`azcopy` can be used to copy data from/to/between storage accounts.
```
$ azcopy
------------------------------------------------------------------------------
azcopy 6.0.0-netcorepreview Copyright (c) 2017 Microsoft Corp. All Rights Reserved.
------------------------------------------------------------------------------
# azcopy is designed for high-performance uploading, downloading, and copying
data to and from Microsoft Azure Blob, and File storage.

# Command Line Usage:
    azcopy --source <source> --destination <destination> [options]

# Options:
    [--source-key] [--dest-key] [--source-sas] [--dest-sas] [--verbose] [--resume]
    [--config-file] [--quiet] [--parallel-level] [--source-type] [--dest-type]
    [--recursive] [--include] [--check-md5] [--dry-run] [--preserve-last-modified-time]
    [--exclude-newer] [--exclude-older] [--sync-copy] [--set-content-type] [--blob-type]
    [--delimiter] [--include-snapshot]

------------------------------------------------------------------------------
For azcopy command-line help, type one of the following commands:
# Detailed command-line help for azcopy      ---   azcopy --help
# Detailed help for any azcopy option        ---   azcopy --help source-key
# Command line samples                       ---   azcopy --help sample
You can learn more about azcopy at http://aka.ms/azcopy.
------------------------------------------------------------------------------
```

## Virtual Machine Storage

Each VM has automatically has
* 2GB OS disk
* Temporary disk (page file)

Additional data disks are user defined
* Max 4GB in size, number depends on VM instance type (via iSCSI)
* Are stored in a BLOB in Storage Account
* Mapped via iSCSI as SCSI disk to VM
* Existing VHDs can be attached to VMs

Standard Storage
* 50k IOPS per Storage Account
* 100 disks with max IOPS per Storage Account
* No limits on total disk capacity, number of disks with max limit capacity, snapshot capacity, bandwidth, number of VMs with max bandwidth 
* Unmanaged 1GB to 4095GB
* Managed 32GB to 4095GB (S4 to S50)
* Max 60MB/s per disk, 500 IOPS max per disk

Premium Storage
* No Limit on IOPS per Storage Account
* No limit on max number of disks with max IOPS
* Max 35TB capacity per account, max 35 disks with max capacity, 10TB snapshot capacity in total, 50 Gbps max bandwidth per account, max number of VMs with max bandwidht depends
* Disk size between 32GB and 4096GB, max 250 MB/s throughput per disk, 7500 IOPS per disk (P10 to P50)
* 32GB and 64GB are managed only (P4, P6)
* Only availble for DS-Series, GS, Ls, and Fs
* Single VM can have up to 64TB of Premium Storage

VMs either use managed or unmanaged disks.

Unmanaged disks
* most config on Storage Account level, replication LRS, GRS, RA-GRS, encryption ADE/SSE, priced per GB used (Standard) or provisioned (Premium), visible in storage account
* Are storage in Azure Blob
* Can be migrated to managed disks (offline, VM needs to be shut down) via `ConvertTo-AzureRmVMManagedDisk`

Managed disks
* Bypasses limits of Storage Account
* most config on disk level, LRS replication only, SEE on by default, priced per disk size, automatic placement, no visible in storage account, one time SAS for export to VM

### File Shares

* Can be mounted via Storage account name and Storage account access key
* Can upload files to share via PS Set-AzureStorageFileContent
* Quota can be changed dynamically to increase share size
* Maximum size is currently 5TB

### Azure Storage File Sync Service

Azure Storage Sync agent on-prem
Requires powershell cmdlets for azure on server

### Storage Spaces

* Disks are grouped together to Storage Pools
* Storage Pools are grouped together to Storage Spaces
* Storage Space allows to provision and allocate, mirror, etc. storage to hosts
* Looks like this is Windows only

Benefits of Storage Spaces
* Continuous availablity (auto failover)
* Multi-tenancy
* Simplicity - managed via Server Manager or PowerShell
* Data integrity checks in background
* Storage tiers - SSD and HDD

Implementing Storage Spaces
1. Identify physical disks
1. Create Storage Pools
1. Create virtual disks

1. Deploy Windows Server VM (bigger instances allow to attach more disks to VM)
1. Add managed data disks (Storage Spaces needs at least 3 disks) and update VM definition (to make disks appear in VM)
1. Create new Storage Pool from disks
1. Create Volume and format it, then available in VM

## Unstructured Storage

* Core of Azure Storage Engine and used by nearly all services
* VHD leverage Blob in backend
* Blob = Binary large object
* Especially used for Big Data workloads on Azure
* Azure Blob = Azure's Object Storage service

Use cases: App and web scale data, backups and archive, big data, IoT, Genomics, video, etc.

3 types of blobs:
* Block Blobs
* Append Blob - multi writer append only scenarios
* Page Blobs - page alinged random reads and writes (for example for vhds, database, etc.)

Why?
* Limitless scale
* Globally accessible
* Cost efficient
* Active or deep archive modes

Notion of
* Container - an account can have an unlimited number of containers, unlimted amount of blobs in it
* Blob - an object (Block vs Append vs Page)

VHD Blobs can be accessed via:
* Add-AzureRmVHD
* Save-AzureRmVHD

Azure import and export service
* Ship harddrives to Azure DC
* Get Blob content shipped to you via mail

## Structured Storage

* Files
* Tables
* Queues

### Tables

* Non-relational NoSQL database for structured data
* Stored as Key-Value pairs
* Storage Account has Tables which have Entities
* Clustered index for fast querying
* Scales automatically as demand increases
* Scales to billions of entries per table

Entities can have:
* up to 255 entities
* 3 system properties: PartitionKey, RowKey, Timestamp

### Queues

* Storage Account has one or more queues
* Single queues message is up to 64KB and has a maximum lifetime of 7 days
* Queue can have millions of messages
* HTTP or HTTPS

Usage
* New-AzureStorageTable
* Get-AzureStorageTable

### Azure Files

* Lift and shift, SMB 2.1/3.0 and REST, Encryption at rest, secure SMB (port 445)
* Example: HA FTP server behind Azure files

### Azure File Sync

* Sync files to Azure File Share, does local caching for hot/cold data
* Syncs upon file access (other than in OneDrive)
* Can replicate Fileshares to other regions and make them available in different locations

## Storage Security

* Control access to storage account via Azure Active Directory
* Secure data access via storage account keys or Shared Access Signatures (for granting access to specific data objects / time)
* Encrypt data in transit using https
* Encrypt data at rest
* Azure Disk Encryption using BitLocker / DM-Crypt under Linux, integrated with Azure Key Vault for managing encrpytion keys

Access Keys
* kind of like a password
* Can be stored in Azure Key Vault
* Always two for easier rotation

Encryption key
* customer provided key is coming soon (might be here by now)

* Storage Firewall can protect Storage Account by IP / VNET
* RBAC (Read, write, delete) permission through Azure AD
* Storage Metrics are logged into Azure Monitor, can be fed into PowerBI, EventHub, etc.
* Storage Keys = password for storage account = root password (2x 512 bit symmetric keys)

### Share Access Signature
* Similar to presigned URLs in AWS
* Works for Blob, File, Queue and Table
* Works on full service, container or object
* Permissions: read, write, delete, list, add, create, update, process
* Start and stop time
* Allowed IPs and protocols

Ad Hoc Signatures - single use URL (use short time)
Policy-based signatures - on container level, holds for specificed time

### Azure Backup

1. Create a recovery services vault
1. Download files
1. Install and register backup agent
1. Backup files and folders

* Azure backup agent - Azure and onprem
* System Center DPM (Data Protection Manager) - Azure and onprem
* Azure Backup Server - Azure and on-prem
* Azure Backup (VM extension) - Azure only

Backup and Site Recovery (OMS) --> Create Backup vault --> Create Backup --> select what you want to do

### Azure Site Recovery

* DR solution for Hyper-V, VMware, bare metal
* Protect SQL Server, Sharepoint, SAP, Oracle (application aware, consistent backup)
* Can be used to migrate VMware to Azure
* Also works from site-to-site without Azure (Azure only monitors it)

2 modes:
* Orchestration only
* Orchestration, replication, failover, failback

### Storage Analytics

* Metrics available for blobs, files, tables, queues
* Loggin only for blob, tables, queues
* For Standard Storage Accounts: Aggregate metrics, per api metrics, logs
* Not for Premium
* Storage Alerts


