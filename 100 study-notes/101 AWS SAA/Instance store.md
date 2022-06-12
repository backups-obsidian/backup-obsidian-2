---
created: 2022-04-19 19:08
updated: 2022-04-20 08:57
---
---
**Links**: [[101 AWS SAA Index]]

---
- It is a type of **block storage**.
- This **storage is located on disks** that are **physically attached to the host computer**. 
- Instance store is ideal for the **temporary storage of information** that changes frequently such as *buffers*, *caches*
- For high performance we use EC2 instance store. *Can give IOPS > 256000* (limit of EBS volumes)

> [!info] You can specify the instance store volumes for your instance only when you launch an instance. You *can’t attach instance store volumes to an instance after you’ve launched it*.

> [!caution] Once you **stop** the instance you **lose all the storage**. On termination also you lose everything. 

- Instance store volumes can also be configured in **RAID 0 (max performance)** and **RAID 1 (redundancy)**. [[EC2 EBS#^e75d97|More information about RAID]].
- With instance stores backup and replication is our responsibility.

> [!tip]- If data is replicated then we can use instance store for applications that require permanent storage.
> Like a company plans to migrate a NoSQL database to an EC2 instance. The database is *configured to replicate the data automatically to keep multiple copies of data for redundancy*. The Solutions Architect needs to launch an instance that has a high IOPS and sequential read/write access.
> ---
> In this case we can go for instance stores since data is replicated automatically so stopping an EC2 instance won't cause any loss of data.

