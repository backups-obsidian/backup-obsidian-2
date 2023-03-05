---
created: 2022-04-19 10:02
updated: 2023-03-05 16:38
---
---
**Links**: [[101 AWS SAA Index]]

---
## Introduction
-   It is **bridge** between your **S3 and on premise data**.
-   It is *not possible to put everything on AWS due to compliance* and other reasons so companies are moving to Hybrid cloud options.
-   **Use cases**: recovery, backup and restore
- Different types of *storage options*
	- ![[attachments/Pasted image 20220424213119.png]]
-   There are **3** types of **Storage Gateways**: **File**, **Volume** and **Tape**.

## File Gateway
- *Works with both* windows and linux file shares.

> [!tip] Go for file gateway if see keywords like *SMB/NFS*, *cache*, *AD authentication*, *file storage*, *POSIX compliant*.

### S3 file gateway
- **Configured S3 buckets** are **accessible** using the **SMB** (*FSx windows*) and **NFS**(*EFS*) protocol.
- Can be **Integrated with Active Directory** for User authentication.
- Can be used with **windows file servers**.
- Most **recently used files** are **cached**.
- Can send to **S3**, **S3 IA**, **S3 one zone IA** and **S3 intelligent tiering**. 
	- *For glacier you will need lifecycle rules* just like Snow devices.
	- ![[attachments/Pasted image 20230305134449.png]] 
- No EBS backup like in volume gateway.

> [!note] *S3 Glacier* and *S3 Glacier Deep Archive* are **NOT** covered by S3 file gateway.

### FSx file gateway
- Main point is **Local cache for frequently accessed data**.
- *Everything is same as s3 file gateway* expect for the fact that it has **native access to Amazon FSx for Windows File Server**.
	- ![[attachments/Pasted image 20230305163153.png]]
- Windows native compatibility (**SMB**, **NTFS**, Active Directory, etc)

## Volume Gateway
- **Block storage** using **iSCSI protocol**. 
- They are stored in S3, *backed by EBS snapshots* since EBS is the only block storage option apart from instance store.
	- ![[attachments/Pasted image 20220424213257.png]]

- It is of two types **stored** and **cached volumes**.
	- **Cached volumes**: *low latency access* to most recent data
	- **Stored volumes**: entire dataset is on premise, *scheduled backups to S3*

## Tape Gateway
- Can directly send to **S3 Glacier and S3 Deep Glacier**.
- Useful for companies that have backups using tapes.
- **Virtual Tape Library (VTL) backed by Amazon S3 and Glacier**
	- ![[attachments/Pasted image 20220424213624.png]]
- Back up data using **existing tape-based processes** (and **iSCSI interface**)

> [!tip] If question mentions backed by *S3 Glacier*, *tape* then always go for tape gateway.

> [!note] Tape Gateway is the *only way of transferring data stored in tapes to AWS*. You *cannot use DataSync* since it uses SMB or NFS file types.

## Summary
![[attachments/Pasted image 20230305163643.png]]

## Required hardware
> [!caution] **Gateway** has to run on **corporate data centres**.

- But sometimes it is not possible so we *can leverage storage gateway hardware appliance from AWS*.

- Works with File Gateway, Volume Gateway, Tape Gateway
- Has the required CPU, memory, network, SSD cache resources