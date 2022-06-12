---
created: 2022-04-19 16:22
updated: 2022-04-24 21:22
---
---
**Links**: [[101 AWS SAA Index]]

---
- Launch **3rd party high performance file systems** on AWS
- Fully managed service
- All *FSx and EFS are file storage solutions*. For example EBS and instance are block storage solutions.

## FSx for windows file server
-   EFS is only available for linux systems. So if you want to **share data between windows systems** you have to use FSx for windows 
- It is a **fully managed** *windows file share*.
- Supports **SMB** and **Windows NTFS sharing formats**
- Shared storage for windows EC2 instances.
- Supports **Microsoft Active Directory integration**, ACLs, user quotas
- Built on SSD, scale up to 10s of GB/s, millions of IOPS, 100s PB of data
- **Can be accessed from your on-premise infrastructure**
	- ![[attachments/Pasted image 20220424204638.png]]
- Can be configured to be Multi-AZ (high availability)
- **Data is backed-up daily to S3**
- Windows instances cannot be connected to EFS but Linux instances can be connected to windows file share.

## FSx for Lustre
- It is a type of **parallel distributed file system** for **large scale computing**.
- **Lustre** is derived from **Linux** and **Cluster**.
- It is **used** for **ML** and **HPC** (high performance computing)
- It has *seamless integration* with **S3** for **Cold data**. Hot data is stored in lustre.
- It is *POSIX compliant*.
- Scales up to 100s GB/s, *millions of IOPS*, sub-ms latencies
- There are *2 types of file systems* in lustre

### Scratch File System
- **Temporary storage**
- Data is *not replicated* (doesn't persist if file server fails)
- **High burst** (6x faster, 200MBps per TiB)
- **Usage**: *short-term processing*, *optimise costs*

### Persistent File System
- **Long-term** storage
- *Data is replicated* within same AZ
- **Usage**: long-term processing, *sensitive data*