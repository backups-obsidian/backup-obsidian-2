---
created: 2022-04-19 16:22
updated: 2023-02-18 08:42
---
---
**Links**: [[101 AWS SAA Index]]

---
## Introduction
- It is a **fully managed**, **highly available** with **replication across multi AZs** database.
- It is a **NoSQL** database. Some features of NoSQL database:
	- A SQL database has a rigid schema. Go for NoSQL databases if you need a flexible schema.
	- NoSQL databases like MongoD, DynamoDB are easy to **scale horizontally since they are distributed**.
	- NoSQ databases *do not support query joins* (or just limited support)
	- *All the data that is needed for a query is present in one row*
	- NoSQL databases don't perform aggregations such as "SUM", "AVG", etc.
- It *scales to massive workloads*, distributed database.
- **Integrated with IAM for security**, authentication and authorisation.
	- *Access fully controlled* by IAM
- **Low cost** and **scales automatically**. 
	- By default, Auto Scaling is not enabled in a DynamoDB table which is created using the *AWS CLI*.
	- Autoscaling is *free of cost*. 
	- We have *standard* & *IA table class*.

> [!question]- The main differentiator will be keywords like "*scaling*" in questions. In *RDS, you still have to manually scale up your resources and create Read Replicas to improve scalability* while in DynamoDB, this is automatically done.
> Also you can only vertically scale the RDS. It is possible to horizontally scale the reads by having read replicas but the master that takes write can only be scaled vertically.

- DynamoDB is **made up of tables.** We **directly create tables** instead of creating databases.
- *Each table must* have a **Primary Key** which must be decided at creation time. **Combination** of the **partition key and the sort key** constitutes the primary key.
- Each table can have **infinite number of rows (items)**.
- Each row can have **columns (attributes)** which can be null. 
	- The attributes can be nested.
	- The attributes can be added over time without affecting your previous data.
- **Maximum size** of an **item (row)** is **400KB**.
	- Store *objects larger than 400KB in S3 and use pointers* in DynamoDB
- **Data types** supported are:
	- *Scalar Types*: String, Number, Binary, Boolean, Null
	- *Document Types*: List, Map
	- *Set Types*: String Set, Number Set, Binary Set
- It allows us to write to *2 tables at the same time or to None* as part of one specific write.
- Store more frequently and less frequently accessed data in *separate tables*.
- **All DynamoDB tables are encrypted**. 
	- There is *no option to enable or disable encryption* for new or existing tables. 
	- By *default*, all tables are encrypted *under an AWS owned customer master key (CMK)* in the DynamoDB service account. 
	- However, you *can select* an option to encrypt some or all of your tables under a *customer-managed CMK* or the *AWS managed CMK* for DynamoDB in your account.
- DynamoDB has *two built-in backup methods* (On-demand, Point-in-time recovery) that **write to Amazon S3** 
	- We do not have access to the S3 buckets that are used for these backups.

## Read and Write Capacity Modes
- *Control* how you manage your **table’s capacity** (*read/write throughput*)
- For understanding calculation of read & write capacity and how data is stored refer [[../102 AWS DVA/DynamoDB Capacity| DynamoDB Capacity]]
- We can easily switch between provisioned mode and on demand mode.

### Provisioned Mode
- **Default** 
- You specify the *number of reads and writes per second*. This means capacity has to be planned before hand.
- RCU and WCU can be increased independent of each other.
- You pay for *provisioned Read Capacity Units (RCU)* & *Write Capacity Units (WCU)*
- *Cheaper* as compared to on demand mode
- We can also *choose autoscaling* for RCU and WCU and specify a minimum & maximum capacity.
	- We can autoscale RCU and WCU independent of each other. Like it is possible to autoscale only RCU but not WCU.
	- ![[attachments/Pasted image 20220525111059.png]]
- If we disable auto scaling we only get the option to specify the read and write capacities.
- Throughput can be exceeded *temporarily* using **Burst Capacity**
- If Burst Capacity has been consumed, you'll get `ProvisionedThroughputExceededException`
	- It's then advised to do an *exponential backoff retry*

### On Demand Mode
- Read/writes *automatically scale up/down* with your workloads
- *No capacity planning needed*
	- Unlimited WCU & RCU, *no throttle*
- You're charged for reads/writes that you use *in terms of RRU and WRU*
	- Read Request Units (*RRU*) throughput for reads (*same as RCU*)
	- Write Request Units (*WRU*) throughput for writes (*same as WCU*)
- Useful for **unpredictable** workloads 
- It is **2x -3x more expensive** than the provisioned mode.

## DynamoDB Accelerator (DAX)
- **Fully-managed**, **highly available**,  **in memory cache** for DynamoDB. Helps solve read congestion by caching.
	- Solves the *hot key problem* (too many reads).
- **Doesn't** require **application logic modification.** Compatible with existing DynamoDB APIs.
- Data stored in DAX have a **TTL** of **5 minutes** (**default**).
- DAX cluster is *made up of nodes* 
	- We have to select the *node type* while creating the cluster
	- *Multi-AZ* (*3 nodes minimum* recommended for *production*)
-  Can deliver up to 10 times performance improvement—**from milliseconds to *microseconds***—even at millions of requests per second.
- Secure: *Encryption at rest* with KMS, VPC, IAM, CloudTrail,

> [!question]- When to go for ElastiCache over DAX?
>- ElastiCache is useful in a scenario where the application is performing some computation after retrieving it from DynamoDB. The aggregation result is stored in ElastiCache. *If there is no compute then it is always advisable to use DAX*.
>- Also using Elasticache with DynamoDB is much more involved than DAX.
> ---
> ![[attachments/Pasted image 20220525151014.png]]

## DynamoDB Streams
- *Event driven programming* using **dynamo db streams**. 
	- Example sending a welcome mail when users are added: Enable DynamoDB Stream and create an AWS Lambda trigger, as well as the IAM role which contains all of the permissions that the Lambda function will need at runtime. The data from the stream record will be processed by the Lambda function which will then publish a message to SNS Topic that will notify the subscribers via email.

- Ordered stream of **item-level modifications** (create/update/delete) in a table
- DynamoDB streams is **not enabled by default**.
- Stream records can be sent to (**3**) : **Lambda**, **Kinesis Data Streams** and **Kinesis Client Library applications**.
- *Data retention* for up to **24 hours**.
	- All data in DynamoDB Streams is subject to a *24 hour lifetime*. 
	- You can retrieve and analyse the last 24 hours of activity for any given table; however, data older than 24 hours is susceptible to trimming (removal) at any moment.

> [!question]- You are designing an online medical appointment system that allows patients to book an appointment with their preferred doctor at medical centers all over the country. The DynamoDB Streams feature is enabled in your DynamoDB database, which allows you to capture information about every modification to data items in your table. *A Lambda function integrated with CloudWatch Events is used to process the data stream every 36 hours then store the results in an S3 bucket*. However, you noticed that there are a lot of *updated data in DynamoDB which are not sent to your S3 bucket* even though there are no errors in the logs.
> **Decrease the interval of running your function to 24 hours** because in DynamoDB Streams, **data older than 24 hours** is susceptible to trimming (removal) at any moment.

- *Use cases*:
	- React to changes in real-time (welcome email to users)
	- Analytics
	- Insert into derivative tables
	- Insert into ElasticSearch
	- Implement *cross-region replication*

## DynamoDB Global Tables
- Tables are present in **different regions**.
- There is **two way replication** between the tables.
- This will make DynamoDB tables accessible with **low latency** in **multiple regions**.
- It is an **Active Active** replication which means **data can be READ and WRITTEN to the table in any region**.
- **DynamoDB streams must be enabled** to use this feature.

> [!note]+ Here **active active** configuration is the key. 
> - You will be presented with questions in which you will have to choose between DynamoDB global tables and [[Aurora#Global Aurora|Global Aurora]] for a database that spans *multiple regions*.
> - If the question mentions *active active* then go for DynamoDB tables since in Global Aurora you cannot have active active config. There is *only one master and read replicas* in other regions for low latency. The read replicas don't take writes.
> - We do have [[Aurora#Aurora Multi Master|Aurora Multi Master]] in which all the databases take writes but it is not multi region.
> - So we can say **DynamoDB global tables** are **multi master**, **multi region** 
> - Whereas **Aurora Global** is **single master**, **multi region**.

## DynamoDB Indexes
- There are **two** types of indexes **Global Secondary Indexes (GSI)** & **Local Secondary Indexes (LSI)**.
- These indexes allow us to query items on attributes other than the primary key.
- The optimal usage of a table's provisioned throughput depends not only on the workload patterns of individual items, but also on the *partition-key design*.
	- More the *distinct partition key values* that your workload accesses, the more those requests will be spread across the partitioned space. 
	- Use *partition keys with high-cardinality attributes*, which have a **large number of distinct values** for each item.

## Cross Account Access
- DynamoDB **DOESN'T** have resource policies.

> [!question]- The development team at a retail organisation wants to allow a *Lambda* function in its AWS Account *A* to access a *DynamoDB* table in another AWS Account *B*.
> - You need to create an *execution role in Account A* that gives the Lambda function permission to do its work. 
> - Then you need to create a *role in account B that the Lambda function in account A assumes to gain access to the cross-account DynamoDB table*. 
> - Make sure that you *modify the trust policy of the role in Account B* to allow the execution role of Lambda to assume this role. 
> - Finally, *update the Lambda function code to add the AssumeRole API call*.