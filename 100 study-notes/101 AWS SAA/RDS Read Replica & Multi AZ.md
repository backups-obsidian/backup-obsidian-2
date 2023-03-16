---
created: 2022-04-19 16:22
updated: 2023-03-16 08:26
---
---
**Links**: [[101 AWS SAA Index]]

---

## RDS Read Replica
- Read replicas help in scaling our reads.
- With RDS non [[Aurora]] engines we can create upto **5** read replicas.

> [!important]- Read replicas can be in the *same AZ*, *different AZ* or in a *different region*.
> RDS Multi AZ is for only one region. So if you want your database backed up to a different region then create cross region read replicas. In the case of a disaster you will have to *manually promote the read replica in another region to become the master*.

- Replication is **async** so the reads are **eventually consistent**.

> [!caution]- Read replicas can be **manually** promoted to their own DB i.e. allowing it to take writes. But by **default they only read**.
> That is by default read replicas are only used for **SELECT** statements.

- To take advantage of the read replicas the *application must update the connection string*. You don't have to update the connection string in Aurora.

> [!tip]- Classic use case of Read Replicas
> **Reporting application** wants to read you data. You create a read replica for it since reading from the main database will effect the performance of production application.

- In AWS there is a *cost when data goes from one AZ to another*. For *RDS read replicas* if its in the *same region then we don’t have to pay this fee* since it is a *managed service*.
	- ![[attachments/Pasted image 20220421121900.png]]
	- Read Replicas are *billed as a standard DB Instance and at the same rates*. You are not charged for the data transfer incurred in replicating data between your source DB instance and read replica within the same AWS Region.
	- Remember inter AZ transfer does cost money but its less when you are using private IPs. In case of RDS Read Replica its nice you don't have to pay for the inter AZ transfer.

> [!question]+ How to create a *high availability read replica*?
> You can create a **read replica as a Multi-AZ DB instance**. Amazon RDS creates a standby of your replica in another Availability Zone for failover support for the replica. *Creating your read replica as a Multi-AZ DB instance is **independent** of whether the source database is a Multi-AZ DB instance*.

## RDS Multi AZ
- Spans at least 2 AZ. As the name suggests the *standby instance* is in a **different AZ in the same region**.
- Replication is **sync**.
- We have a **single DNS name** which **switches automatically**(**Automatic failover**) to the standby database in case the primary database becomes unavailable. 
	- We can say the *CAME record* will be updated to point to the standby DB.
- **Automatic failover takes places in case of**:
	- *Loss* of AZ
	- Storage *failure* on primary 
	- *Loss* of network
	- **OS is undergoing software patching**
	- Modified (e.g., **DB instance type changed**)
	- Busy and unresponsive

> [!note]- Automatic failure takes in case of loss, failure, DB instance type change, OS patching.

- We can *initiate a manual failover* of the DB instance using **Reboot with failover**.

> [!important]- The standby database in Multi AZ is *not used for scaling*. **No one can read to it or write to it**. It is just here as a failover.
> Remember *high-availability feature is not a scaling solution for read-only scenarios*; you cannot use a standby replica to serve read traffic. To service read-only traffic, you should use a Read Replica.

- With Multi AZ we get increased database availability in case of system upgrades like OS/security patching or DB instance scaling. If you are using a *non Multi AZ* instance then *security/OS patching will cause a downtime*.

> [!question]- Why not use Read Replica for disaster recovery?
> - Read replicas can be setup in a different AZ for disaster recovery. Although you can promote a read replica, its *asynchronous replication might not provide you the latest version of your database*. **Always go for multi AZ for disaster recovery**.
> - Also you have to *manually promote read replicas*, in case of multi AZ it is automatic.

> [!tip]- If you see key words like *high availability* and *automatic failover* then always go for RDS multi AZ. 
> Although you can have read replicas in different regions they don't offer automatic failover. You have to manually promote it.

> [!question]- How does *OS patching* take place in multi AZ?
> - Perform maintenance on the standby.
> - Promote the standby to primary.
> - Perform maintenance on the old primary, which becomes the new standby.
> 
> This **ensures HA**. 

> [!question]- How does *database engine update* takes place?
> When you modify the database engine for your DB instance in a Multi-AZ deployment, then Amazon RDS upgrades **both the primary and secondary DB instances at the same time**. In this case, the database engine for the entire Multi-AZ deployment is shut down during the upgrade.
> There is **some down time**.

- We **can easily configure a RDS database to be multi AZ by enabling it in settings**. 
	- This will *not cause any downtime*.
- What happens when you **convert a single AZ database to multi AZ**?
	- AWS automatically takes a *snapshot of our DB*, 
	- Creates a *new DB from the snapshot* 
	- Establishes *sync replication*