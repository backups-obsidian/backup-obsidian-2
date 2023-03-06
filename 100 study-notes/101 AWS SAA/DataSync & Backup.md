---
created: 2022-04-19 16:22
updated: 2023-03-06 10:53
---
---
**Links**: [[101 AWS SAA Index]]

---
## DataSync
- **Move large amount of data** to and from
	- *On-premises* / *other cloud* to AWS (NFS, SMB, HDFS, S3 API...) - **needs agent**
	- *AWS to AWS* (different storage services) - *NO agent needed*
- Example use case:
	- You have on-premises sensitive files and documents that you want to *continuously synchronise* to AWS to keep *another copy*.
- Can synchronise to: 
	- **S3** (*any storage classes including Glacier*). Data **can be directly moved to Glacier**.
	- **EFS** 
	- **FSx for Windows**

- Move data from your NAS or file system via **NFS or SMB**
	- **File permissions and metadata are preserved** (NFS POSIX, SMB, etc.)
- Replication tasks can be **scheduled** hourly, daily, weekly. 
	- It is **NOT continuous**.
- It is **automated** and **managed**.
- Can be used to *synchronise 2 EFS across regions*.

> [!important] Generally used *when you no longer need to store data on premises*.

> [!note] Keywords: *data replication*, *automated*, *moving on premise data*.

- Sometimes the question will mention that *there is not enough bandwidth capacity*.
	- In such cases we have to use *AWS Snowcone*.
	- It comes with DataSync agent already installed.
	- We can just run snowcone on premises, pull the data and then shipped back to AWS.

- Diagram (AWS DataSync NFS / SMB to AWS): 
	- ![[attachments/Pasted image 20230306104554.png]]

> [!tip]- In questions whenever you see **preserve metadata**, go for DataSync.

## Backup
- **Fully managed service**
- **Centrally manage and automate backups across AWS services**
- No need to create custom scripts and manual processes
- Supported services: *Everything you can think of*.
    - Amazon *FSx*, Amazon *EFS*, Amazon DynamoDB, Amazon EC2, Amazon *EBS*, Amazon *RDS* (AIl DBs engines), Amazon *Aurora*, AWS *Storage Gateway* (Volume Gateway)

- Supports **cross-region** and **cross-account** backups.
- Supports **PITR** (Point in time recovery) for supported services
- On-Demand and **Scheduled** backups

- You create backup policies known as **Backup Plans:**
    -   Backup *frequency* (every 12 hours, daily, weekly, monthly, cron expression)
    -   Backup *window*
    -   *Transition* to Cold Storage (Never, Days, Weeks, Months, Years)
    -   *Retention Period* (Always, Days, Weeks, Months, Years)

> [!caution] We need backup for databases because the automated backups only have a duration from 7 to 35 days. If we want to store them for longer duration we need to create *snapshots*. *Backup automates the process of **snapshot** creation*.

> [!note] You create and *backup plan* for an AWS service and then backup stores its *snapshot in S3*.

### Backup Vault Lock
- Enforce a **WORM** (Write Once Read Many) state for all the backups that you store in your AWS Backup Vault.
	- *Backups CAN'T be deleted*.
- *Additional layer of defense* to protect your backups against:
	- Inadvertent or malicious delete operations
	- Updates that shorten or alter retention periods
- Even the **root user CANNOT delete backups** when enabled.