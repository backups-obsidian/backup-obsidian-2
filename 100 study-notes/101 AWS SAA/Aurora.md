---
created: 2022-04-19 19:08
updated: 2022-06-17 21:03
---
---
**Links**: [[101 AWS SAA Index]]

---
## Introduction
- Amazon aurora is *also managed by Amazon RDS*. 
- Aurora is a **distributed**, **fault-tolerant**, **self-healing** storage system that auto-scales up to 128TB per database instance.
- Aurora supports **Postgres** and **MySQL**.
- Aurora is *cloud optimised* which means you can gain *5x* performance over *MySQL* on RDS and *3x* performance over *Postgres* on RDS. But more expensive (*20%*) than RDS.
- Aurora storage automatically grows from **10GB to 128TB**.
- We can have upto **15 read replicas**. With RDS MySQL or Postgres we can only have 5.
- **Instantaneous automatic failover** (30s).
- **Automatic backups** are stored for a **maximum of 35 days** just like RDS.
- Aurora can handle highly transactional **OLTP** workloads. The database is also **ACID** compliant.
- It has the same security measures as [[RDS Security#Network Security|RDS]] because it uses the same engines Postgres and MySQL. It also *supports IAM based authentication*.
	- In Aurora also you have to *configure security groups*.

## High Availability and Read Scaling
- *High availability is built in Aurora*. For RDS HA you have to use Multi AZ.
- For HA it stores multiple copies (**6**) of you data across AZs.
- Only one Aurora which is the *master takes write*.

### DB cluster
> [!important]+ What is a **DB cluster**?
> If you have *read replicas* then it is known as a **DB cluster**. 
> - In a DB cluster we have *one master* which supports read and writes
> - We can have several *read replicas*.
> - In the event of disaster any of these *read replicas* can be *automatically promoted to the new master*. 

> ---
> In RDS you have to use multi AZ for automatic failover. Also the standby instances don't take read requests in aurora they do.

- Aurora DB cluster uses a *cluster volume* that manages the data for those DB instances. It is a *virtual database storage volume that spans multiple Availability Zones*, with each Availability Zone having a copy of the DB cluster data. 
- This is also known as a **provisioned DB cluster** since you can *choose your DB instance class size* and create Aurora Replicas to increase read throughput. If your workload changes, you can *modify the DB instance class size* and change the number of Aurora Replicas. This model *works well* when the **database workload is predictable**, because **you can adjust capacity manually** based on the expected workload.

> [!question]- How can read replicas be promoted to the main DB in case of failure without any replication?
> *Aurora Replica connects to the same storage cluster volume* as the primary DB instance and supports only read operations.
> ---
> - So read replicas help in multi AZ setup.
> - ![[attachments/Pasted image 20220421144808.png]]

### Failover
> [!question]+ What happens incase of a failover.
> **DB Cluster** - Read Replica present
>  If you have an Amazon Aurora Replica in the same or a different Availability Zone, when failing over, *Amazon Aurora flips the canonical name record (CNAME)* for your DB Instance to point at the healthy replica, which in turn is promoted to become the new primary. Start-to-finish, *failover typically completes within 30 seconds*.
> 
> **Aurora Serverless**
>  If you are running Aurora Serverless and the DB instance or AZ become unavailable, Aurora *will automatically recreate the DB instance in a different AZ*.
> 
> **Single Instance** - No DB cluster since no read replica.
> If you do not have an Amazon Aurora Replica (i.e. single instance) and are not running Aurora Serverless, Aurora *will attempt to create a new DB Instance in the same Availability Zone as the original instance*. If unable to do so, Aurora will attempt to create a new DB Instance in a different Availability Zone and not the other way around.

### Read Replicas
- *Read replicas are best when you have dynamic reads*. In memory caching services are best when reads are not dynamic.
- Just like RDS Aurora also **supports cross region** read replica.
- Replication time of Aurora Read Replicas is **< 1s**. For RDS it is greater than 1s.
- We have a **writer(cluster)** and a **reader** endpoint.
- **Read replicas are auto scaling**. Reader endpoint takes care of adding the new instances. In short *Reader endpoint* helps in connection *load balancing*.
	- ![[attachments/Pasted image 20220421145652.png]]

> [!info]- Read replica promotion priority
> - Each Read Replica is associated with a **priority tier (0-15)**. 
> - In the event of a failover, Amazon Aurora will *promote the Read Replica that has the highest priority* (**the lowest numbered tier**). 
> - If two or more Aurora Replicas *share the same priority*, then Amazon RDS **promotes the replica that is largest in size**.
> - If two or more Aurora Replicas share the same priority and size, then Amazon Aurora promotes an **arbitrary replica** in the same promotion tier.

### Custom Endpoints
- Define a *subset of Aurora read replicas* with custom end points.
	- ![[attachments/Pasted image 20220421190739.png]]
- *Reader endpoint is generally not used with custom end points*.
- Example use case would be **running analytical queries on some specific replicas**.

> [!note]- Scenario where to optimise your database workloads in your cluster where you have to direct the write operations of the production traffic to your high-capacity instances and point the reporting queries sent by your internal staff to the low-capacity instances.
> *Create a custom endpoint* in Aurora based on the specified criteria for the production traffic and another custom endpoint to handle the reporting queries.

## Aurora Serverless
- Great for **infrequent**, **intermittent**, **sporadic** or **unpredictable** workloads. Provisioned DB cluster is not suitable for these kinds of workload.

> [!tip] Whenever you see the word **unpredictable** or **minimising** costs then go for Aurora Serverless.

-  Automated database instantiation and auto scaling based on actual usage.
- **No capacity planning**.
- *Pay per second* can be more **cost effective**. 

> [!question]- How is Aurora serverless more cost effective than normal aurora?
> Aurora serverless need not be running when its not being used but the *normal aurora (provisioned DB cluster) must always be running*. This is how aurora serverless is more cost effective.

- Also known as a serverless DB cluster.

## Aurora Multi Master
- **High HA** and **immediate fail over**.

> [!question]+ How is Aurora multi master different from normal aurora?
> **Each node does R/W** instead of promoting a Read replica to master. Since there is no need of promotion fail over is instantaneous.

- In a multi-master cluster, all DB instances can perform write operations. There isn't any failover when a writer DB instance becomes unavailable, because another writer DB instance is immediately available to take over the work of the failed instance.

- It has a *very specialised use case*. Like in applications where you *can't afford even brief downtime for database write operations*. In *most cases you should opt for Global Aurora*.

## Global Aurora 
- **1 primary region** (R/W)
- Upto **5 secondary read only regions**. With **16 read replicas per secondary region**.
- Helps in **decreasing latency**
- If you need **good RPO (1s) and RTO(1m)** go for global aurora.
- Can promote another region for disaster recovery. *Mitigate multi region failure*.

> [!note] *Keywords* for identifying *questions* related to aurora: another **region**,  **global** , decreasing inter region **latency**, **RPO/RTO**

## Aurora ML
-   Add ML based predictions to your applications **via SQL**
-   *Integration* between Aurora and AWS ML services
    -   *Amazon SageMaker* (use with any ML model)
    -   *Amazon comprehend* (for *sentiment analysis*)
- Some use cases are **fraud detection**, **ads targeting**, **sentiment analysis** etc.

## Miscellaneous
- You *can invoke an AWS Lambda function from an Amazon Aurora MySQL* compatible Edition DB cluster with a *native function or a stored procedure*.
