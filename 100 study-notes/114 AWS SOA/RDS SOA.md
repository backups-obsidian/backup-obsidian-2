---
created: 2023-03-05 20:07
updated: 2023-03-11 09:53
---
---
**Links**: [[114 AWS SOA Index]]

---
## Parameter Groups
- You can configure the DB engine using Parameter Groups
- *Dynamic parameters* are applied **immediately**.
	- ![[attachments/Pasted image 20230305201227.png]]
- *Static parameters* are applied after instance **reboot**.
- You can modify parameter group associated with a DB (*must reboot*)

> [!tip]+ Forcing *SSL* on different databases.
> - **PostgreSQL/SQL Server**: `rds.force_ssl=1`. Using parameters to force SSL.
> - **MYSQL/MariaDB**: `GRANT SELECT ON mydatabase.* TO 'myuser'@'%' IDENTIFIED BY '...' REQUIRE SSL;`. Using SQL statements to force SSL.

- We set **`rds.force_ssl=1` in the DB parameter group**.

## Backups vs Snapshots
> [!note] **Manual backup** is known as **snapshot**.

|                              Backups                               |                                     Snapshots                                     |
|:------------------------------------------------------------------:|:---------------------------------------------------------------------------------:|
| Backups are *continuous* and allow point in time recovery (*PITR*) | Snapshots takes IO operations and *can stop the database* from seconds to minutes |
|            Backups happen during *maintenance windows*             |    Snapshots taken on *Multi AZ DB don’t impact the master* – just the standby    |
| When you delete a DB instance, you *can retain automated backups*  |       Snapshots are *incremental* after the first snapshot (which is full)        |
|  Backups have a retention period you set between *0 and 35 days*   |                         *Manual Snapshots don’t expire*.                          |
|          To disable backups, set retention *period to 0*           |              You can take a *final snapshot when you delete your DB*              |
|                      We CANNOT share backups                       |                       You can copy & *share DB Snapshots*.                        |

> [!caution] Restoring from Automated Backups or DB Snapshots **creates a new DB Instance**.

- We **CANNOT share backups** but we can *share snapshots*.

### Snapshot Sharing
- It is same as sharing EBS snapshots.
- Manual snapshots can be shared with other AWS accounts.
- **Automated snapshots CANT** be shared *without copying* it first.
- You can only share:
	- *Unencrypted* snapshots 
	- *Snapshots encrypted with a customer managed key*.
		- If you *share an encrypted snapshots*, you *must also share any customer managed keys used to encrypt them*.

## Events & Event Subscriptions
- RDS keeps record of events related to:
	- DB instances
	- Snapshots
	- Parameter groups, security groups.
- Example: DB state changed from pending to running
- RDS Event Subscriptions
	- Subscribe to *events to be notified* when an event occurs using **SNS**.
	- Specify the *Event Source* (instances, SGs, etc) and the *Event Category* (creation, failover, etc)
- RDS delivers events to **EventBridge**.

## RDS Logs
- We can send RDS logs to CloudWatch logs, create a metric and then create an alarm form that metric.
	- ![[attachments/Pasted image 20230305221740.png]]
- *Audit logs are lost after a certain period* so if we want to save them then we should send them to CloudWatch logs.

## RDS Performance Insights
- Visualise your *database performance* and **analyse any issues** that affect it.
- With the Performance Insights dashboard, you can *visualise the database load* and filter the load:
	- *Waits*: find the resource that is the *bottleneck* (CPU, IO, lock, etc)
	- *SQL statements*: find the SQL statement that is the problem
	- *Hosts*: find the server that is using the most our DB
	- *Users*: find the user that is using the most our DB
- **DBLoad**: the number of **active sessions** for the DB engine.