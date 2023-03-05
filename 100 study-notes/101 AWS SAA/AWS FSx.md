---
created: 2022-04-19 16:22
updated: 2023-03-05 13:40
---
---
**Links**: [[101 AWS SAA Index]]

---
## AWS FSx
- Launch **3rd party high performance file systems** on AWS
- Fully managed service
- All *FSx and EFS are file storage solutions*. 
	- For example EBS and instance are block storage solutions.

## FSx for windows file server
-   EFS is only available for linux systems. 
	- So if you want to **share data between windows systems** you have to use *FSx for windows*. 
- It is a **fully managed** *windows file share*.
- Supports **SMB** and **Windows NTFS sharing formats**
- Shared storage for windows EC2 instances.
- Supports **Microsoft Active Directory integration**, ACLs, user quotas
- Supports **Microsoft's Distributed File System (DFS) Namespaces** (group files across multiple FS)
- Storage Options:
	- SSD - *latency sensitive* workloads (databases, media processing, data analytics, etc)
	- HDD - *broad spectrum* of workloads (home directory, CMS, etc)
- **Can be accessed from your on-premise infrastructure (VPN or Direct Connect)**
	- ![[attachments/Pasted image 20220424204638.png]]
- Can be configured to be **Multi-AZ**.
	- Always choose Multi-AZ for high HA.
- **Data is backed-up daily to S3**
- Windows instances cannot be connected to EFS but Linux instances can be connected to windows file share.
- We can use the 

> [!note] Although from the name it may look like that FSx windows file server is for windows, it **can be mounted on Linux EC2 instances**.

## FSx for Lustre
- It is a type of **parallel distributed file system** for **large scale computing**.
- **Lustre** is derived from **Linux** and **Cluster**.
- It is **used** for **ML** and **HPC** (high performance computing)
- It has *seamless integration* with **S3** for **Cold data**. 
	- Hot data is stored in lustre.
- It is *POSIX compliant*.
- Storage Options:
	- *SSD* - *low-latency*, *IOPS* intensive workloads, small & random file operations
	- *HDD* - *throughput-intensive workloads*, large & *sequential* file operations
- There are *2 types of file systems* in lustre
- Can be **used from on-premises servers (VPN or Direct Connect)**.

### Scratch File System
- **Temporary storage**
- Data is *not replicated* (doesn't persist if file server fails)
- **High burst** (6x faster, 200MBps per TiB)
- **Usage**: *short-term processing*, *optimise costs*

### Persistent File System
- **Long-term** storage
- *Data is replicated* within **same AZ**
- **Usage**: long-term processing, *sensitive data*

> [!note] In any FSx for Lustre data will only live within one AZ.

## Amazon FSx for NetApp ONTAP
- *Managed* NetApp ONTAP on AWS
- File System compatible with **NFS**, **SMB**, **iSCSI** protocol
- Move workloads running on ONTAP or NAS to AWS
- It has *very broad compatibility*.
- **Storage shrinks or grows automatically**.
- Snapshots, replication, low-cost, compression and **data de-duplication**
- **Point-in-time instantaneous cloning** (helpful for *testing* new workloads)

## Amazon FSx for OpenZFS
- Managed OpenZFS file system on AlVS
- File System compatible with **NFS**.
- Move workloads running on ZFS to AWS
- It has *very broad compatibility*.
- Up to *1,000,000 lOPS with < 0.5ms latency*
- Snapshots, compression and low-cost 
- **Point-in-time instantaneous cloning** (helpful for testing new workloads)