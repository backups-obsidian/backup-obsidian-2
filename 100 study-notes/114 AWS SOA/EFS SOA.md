---
created: 2023-03-03 16:15
updated: 2023-03-03 16:42
---
---
**Links**: [[114 AWS SOA Index]]

---
## EFS Access Points
- Easily manage applications access to NFS environments.
- **Enforce a POSIX user and group** to use when accessing the file system.
- **Restrict access to a directory within the file system** and *optionally specify a different root directory*.
	- ![[attachments/Pasted image 20230303162257.png]]
- Can restrict access from NFS clients using *IAM policies*.

## EFS Operations
- Some operations that can be done *in place*:
	- *Lifecycle Policy* (enable IA or change IA settings)
	- Throughput Mode and Provisioned Throughput Numbers
	- EFS Access Points
- *Operations* that require a **migration using DataSync** (replicates all file attributes and metadata)
	- **Migration to encrypted EFS**
	- **Performance Mode** (e.g. *Max I.O.*)

> [!note] So to *encrypt an unencrypted EFS* we have to perform *migration*.

## CloudWatch Metrics
- **`PercentIOLimit`**
	- How close the file system reaching the I/O limit (General Purpose)
	- *If at 100%, move to Max I/O* (**migration**)
- `BurstCreditBalance`
	- The number of burst credits the file system can use to achieve higher throughput levels
- `StorageBytes`
	- *File system's size* in bytes (15 minutes interval)
	- *Dimensions*: Standard, IA, Total (Standard + IA)

## Miscellaneous
> [!important]- Different ways of *securing access to files/data* stored on an *EFS* file system? 
> - AWS *IAM* & *Security Groups*
> - *NFS-Level Permissions* (users, groups)
> - EFS Access Points