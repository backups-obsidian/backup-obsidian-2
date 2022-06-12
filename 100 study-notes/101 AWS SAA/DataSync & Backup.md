---
created: 2022-04-19 16:22
updated: 2022-05-06 22:20
---
---
**Links**: [[101 AWS SAA Index]]

---
## DataSync
- **Move large amount of data from on-premise to AWS**. 
	- You have on-premises sensitive files and documents that you want to *continuously synchronise* to AWS to keep **another copy**.

- Can synchronise to: 
	- **S3** (*any storage classes including Glacier*). Data **can be directly moved to Glacier**.
	- **EFS** 
	- **FSx for Windows**

- Move data from your NAS or file system via **NFS or SMB**
- Replication tasks can be **scheduled** hourly, daily, weekly. It is not continuous.
- It is **automated** and **managed**.
- Leverage the *DataSync agent to connect to your systems*
- Can be used to *synchronise 2 EFS across regions*.

> [!important] Generally used *when you no longer need to store data on premises*.

> [!note] Keywords: *data replication*, *automated*, *moving on premise data*.

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