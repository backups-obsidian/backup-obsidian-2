---
created: 2022-04-21 11:18
updated: 2022-05-05 19:47
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

### Autoscaling
- RDS will detect when you are running out of space and will **scale automatically** with zero downtime. This means we don’t have to scale our database manually. 
- We set a *desired maximum*(since we don't want to scale our DB infinitely) and auto scaling takes care of the rest. 

> [!caution]+ **Autoscaling** will only work if it is enabled. It is **disabled by default**.
> If you are running into storage issues make sure that autoscaling is enabled for a simple and easy fix.

- There is *no additional charge for autoscaling*.
- It is supported by all database engines.

> [!tip]- Whenever you see RDS instance is running out of storage always go for auto scaling for increasing the disk space instead of manually increasing the disk space in instance settings.
> Keywords like **minimum** development and database administration maybe mentioned.

## RDS Monitoring
-   Amazon RDS provides **metrics in real time** for the **operating system (OS) that your DB instance runs on.** This is known as **enhanced monitoring**.
-   You can **view the metrics for your DB instance using the console**(I think CloudWatch console), or **consume** the **Enhanced Monitoring** JSON output from **CloudWatch Logs** in a monitoring system of your choice

> [!question]+ How does CloudWatch metrics and Enhanced Monitoring differ from each other in case of RDS monitoring?
> -   **CloudWatch gathers metrics about CPU utilisation from the hypervisor for a DB instance**,
> -   Enhanced Monitoring **gathers its metrics from an agent on the instance.**
> ---
> -   As a result, you might find **differences between the measurements**, because the **hypervisor layer performs a small amount of work**. The differences can be greater if your DB instances use smaller instance classes, because then there are likely more virtual machines (VMs) that are managed by the hypervisor layer on a single physical instance.
> - Data provided by *CloudWatch is not as detailed* as RDS Enhanced Monitoring.
> - Since we do not have access to the EC2 instances running our database we *cannot install CloudWatch agents or custom scripts* for specific parameters. We are dependent on Enhanced monitoring.

-   **Enhanced Monitoring metrics** are useful when you want to see how **different processes** or **threads on a DB instance use the CPU**.
-   We can **view the following** in enhanced monitoring: **RDS child processes**, **RDS processes**, **OS processes**.

> [!tip] Whenever you see *processes* and *threads* in question go for RDS Enhanced monitoring.
