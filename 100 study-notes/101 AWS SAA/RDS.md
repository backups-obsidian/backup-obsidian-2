---
created: 2022-04-21 11:18
updated: 2023-03-06 08:35
---
---
**Links**: [[101 AWS SAA Index]]

---

- **Managed** database service SQL databases.
- List of database engines (**6 = 5 + 1**) which are managed by AWS (**Postgres**, **Maria**, **Oracle**, **MySQL**, **Ms SQL server**) and the last is [[Aurora]] which is AWS proprietary.

## Why use RDS instead of manually provisioning databases on EC2
- **Automated provisioning** and **OS patching**
- [[RDS Read Replica & Multi AZ#RDS Read Replica | RDS Read Replica]] to improve read performance.
- [[RDS Read Replica & Multi AZ#RDS Multi AZ | RDS Multi AZ]] setup for **disaster recovery**.
- To handle a higher load in your database, **you can vertically scale up your master database** with a simple push of a button.
- In addition to scaling your master database vertically, you can also improve the performance of a read-heavy database by using **read replicas to horizontally scale your database**.
- You can restore a DB instance to a specific point in time, creating a new DB instance.

> [!caution] You cannot SSH into RDS instances.

### Automated Backups
- We get **automated backups** in RDS. Features of Automated backups.
	- *Daily full backup* of the database (during the maintenance window)
	- *Transaction logs* are backed-up by RDS *every 5 minutes*.
	-  Ability to *restore to any point in time* (from oldest backup to **5 minutes ago**)
		- When you do a point in time restore the *default SG is applied to the new database*.
	- *7 days retention* (can be increased to *35* days)

-   Backups which are **manually triggered** by the user are known as **snapshots**.
-   **Snapshots** **can be retained as long as we want**. In automated backups its **7** days by default and maximum of **35** days.

> [!important]- Backups are stored in **S3**.
> Just for reference, snapshots of EBS volumes were also stored in S3.

> [!question]- Your RDS backups are impacting your production database when they run. What can you do to *improve the performance of your production database when backups are taken*?
> Enable Multi-AZ.

### Autoscaling
- RDS will detect when you are running out of space and will **scale automatically** with zero downtime. 
	- This means we don’t have to scale our database manually. 
- We set a **Maximum Storage Threshold** (since we don't want to scale our DB infinitely) and auto scaling takes care of the rest. 
- It is very useful for applications with **unpredictable workloads**.
- Automatically modify storage if:
	- *Free storage is less than 10%* of allocated storage 
	- Low-storage lasts at least 5 minutes
	- **6 hours** have passed since last modification

> [!caution]+ **Autoscaling** will only work if it is enabled. It is **disabled by default**.
> If you are running into storage issues make sure that autoscaling is enabled for a simple and easy fix.

- There is *no additional charge for autoscaling*.
- It is supported by all database engines.

> [!tip]- Whenever you see RDS instance is running out of storage always go for auto scaling for increasing the disk space instead of manually increasing the disk space in instance settings.
> Keywords like **minimum** development and database administration maybe mentioned.

> [!question]- You have an application that stores its data in an RDS database. You're expecting your application to receive a large number of writes in the next 24 hours. You have enabled Storage Auto Scaling for your RDS database so your RDS database storage can handle a large number of writes. *Your RDS database has increased storage at 1 P.M, but when it tries to scale again at 3 P.M it fails*. What do you expect the reason for this?
> You can **only scale up your RDS storage once within 6 hours**.

## RDS Monitoring
- CloudWatch metrics gathered from the *hypervisor*:
	- DatabaseConnections
	- SwapUsage
	- ReadIOPS / WriteIOPS
	- ReadLatency / WriteLatency
	- ReadThroughPut / WriteThroughPut
	- DiskQueueDepth
	- FreeStorageSpace

- **Enhanced Monitoring** (gathered from an **agent on the DB instance**)
	- Useful when you need to see how different **processes or threads** use the CPU

- Example: 
	- ![[attachments/Pasted image 20230305222457.png]]

> [!question]+ How does CloudWatch metrics and Enhanced Monitoring differ from each other in case of RDS monitoring?
> -   **CloudWatch gathers metrics about CPU utilisation from the hypervisor for a DB instance**,
> -   Enhanced Monitoring **gathers its metrics from an agent on the instance.**
> ---
> -   As a result, you might find **differences between the measurements**, because the **hypervisor layer performs a small amount of work**. The differences can be greater if your DB instances use smaller instance classes, because then there are likely more virtual machines (VMs) that are managed by the hypervisor layer on a single physical instance.
> - Data provided by *CloudWatch is not as detailed* as RDS Enhanced Monitoring.
> - Since we do not have access to the EC2 instances running our database we *cannot install CloudWatch agents or custom scripts* for specific parameters. We are dependent on Enhanced monitoring.

-   **Enhanced Monitoring metrics** are useful when you want to see how **different processes** or **threads on a DB instance use the CPU**.
-   We can **view the following** in enhanced monitoring: **RDS child processes**, **RDS processes**, **OS processes**.

> [!tip]- Whenever you see *processes* and *threads* in question go for RDS Enhanced monitoring.
> A company has an application hosted in an ECS Cluster which is heavily using an RDS database. A developer needs to closely monitor how the different processes on a DB instance use the *CPU* such as the percentage of the CPU bandwidth or the *total memory* consumed by **each process** to ensure application performance. Which of the following is the MOST suitable solution that the developer should implement?
> Use *Enhanced Monitoring in RDS*.
