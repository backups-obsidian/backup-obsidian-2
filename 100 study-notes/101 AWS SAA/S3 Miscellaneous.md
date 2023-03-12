---
created: 2022-04-23 15:21
updated: 2023-03-11 09:52
---
---
**Links**: [[101 AWS SAA Index]]

---
## S3 and Glacier Select
- **Performing queries on data to get filtered data** instead of getting all the data to your application and then doing it.
- This helps in **saving both the bandwidth and the processing time** 
	- Less data is coming out of S3 so low costs
	- Processing is taking place on AWS side so you are saving costs on your CPU and increasing speed of the application.
- We can filter using **simple SQL statements**
- To perform an S3 select we need the *bucket's name* and the *object key* (full path).

## Event Notifications
- Whenever we perform some activity on S3 event notifications are generated if enabled. Like object removed, object added etc
- We can also filter these events for a particular file type.
- An example **use case** is generating *thumbnails from images when they are uploaded to S3*.
- The **target listeners** (**only 4**) to these events can be 
	- *SQS* 
	- *SNS* 
	- *Lambda functions*
	- **Event Bridge**

- Generally the notifications are delivered in a second but sometimes it can take minutes

> [!tip]- For best performance and making sure you get the event *enable versioning*. Why?
> Your team-mate has configured an Amazon S3 event notification for an S3 bucket that holds sensitive audit data of a firm. As the Team Lead, you are *receiving the SNS notifications for every event in this bucket*. After validating the event data, you realised that *few events are missing*. What could be the reason for this behaviour and how to avoid this in the future?
>
> If two writes are made to a single non-versioned object at the same time, it is possible that only a single event notification will be sent. Solution is to enable versioning.

> [!caution] Only normal SQS is allowed. **SQS FIFO is NOT allowed**.

## S3 Inventory
- **List objects and their corresponding metadata** 
	- Alternative to S3 List API operation
- Usage examples: 
	- **Audit and report on the replication and encryption status of your objects**
	- Get the **number of objects in an S3 bucket**
	- Identify the total storage of previous object versions
- Output files: CSV, ORC, or Apache Parquet
- We can generate *daily or weekly reports*.
- You can query all the data using Amazon Athena, Redshift, Presto, Hive, Spark, etc.
- You can *filter generated report using S3 Select*
- Use cases: *Business*, *Compliance*, Regulatory needs,

## S3 Batch Operations
- **Perform bulk operations on existing S3 objects** with a *single request*, example:
	- Modify object metadata & properties
	- *Copy objects between S3 buckets*
	- **Encrypt un-encrypted objects**
	- Modify ACLs, tags
	- Restore objects from S3 Glacier
	- Invoke Lambda function to perform custom action on each object

- A job consists of a list of objects, the *action* to perform, and optional *parameters*.
- *Why use S3 Batch Operations vs scripting it ourselves*? 
	- S3 Batch Operations *manages retries*, *tracks progress*, sends completion notifications, generate reports, etc.
- We use **S3 Inventory to generate a list of objects** and use **S3 Select to filter the objects**.
	- ![[attachments/Pasted image 20230304084358.png]]

## Requester Pays
- In **general** S3 *bucket owners pay for the storage and data transfer costs*.
- In **requester pays** the *requester will pay for the data transfer* instead of the owner. Storage costs will still be paid by the owner.
	- ![[attachments/Pasted image 20220423152753.png]]
- The **requester** must be an **authenticated AWS account** so that AWS can bill them.

## Athena
- Athena is a **serverless** query service to perform **analytics against S3 objects**.
- We use **SQL** to query these files.
- Athena is built on the **Presto engine**.
- *Reporting* can be done using *Amazon QuickSight*.
- Use case: *analytics*, *BI*, **analyse logs**(VPC flow logs, CloudTrail trails)
- It is much **cheaper** than Redshift and EMR.
	- We **pay per TB of data scanned**.

> [!tip] Go for Athena if you see keywords like *cost effective*, *easiest*, *quickly*, *serverless* (minimise operational overhead), *adhoc*

### Athena Performance Improvement
- Since we pay for per TB of data scanned, we *need to scan less data*.
	- We use a **columnar data** for cost savings (less scan)
	- **Apache Parquet** and **ORC** are the *recommended formats*.
- We can use **glue** to *convert the data* in Apache Parquet or the ORC format.
- **Compress the data** for smaller retrievals.
- **Partition datasets** in S3 for easy querying on virtual columns
	- ![[attachments/Pasted image 20230304094905.png]]
- **Use larger files** (> 128 MB) to minimise overhead.

## Glacier Vault Lock
- Adopt a **WORM (write once read many)** model.
- It is helpful for **compliance** and at **data retention**
- An object would go in the glacier and you will add a glacier vault lock policy saying that the **object CANNOT be deleted** and the **policy** itself **CANNOT be deleted**.
- More information on [[S3 Storage Classes#Vault policies and Vault lock | Vault Lock]]

> [!note] A vault is a container for *storing archives on Glacier*. Vault Lock is *only for Glacier and not for S3*.

## S3 Object Lock
- To use this we **must** **enable versioning**
- It **blocks object version deletion** for a specified amount of time.
- It is not a lock for the whole bucket, we can *adapt it for each and every object*.

> [!caution] In both the locks you won't be able to upload modified versions of the object. So read the question carefully. Unless and until *compliance* is mentioned don't go for locks.

- **Retention Modes**:
	- *Governance mode*: users can't overwrite or delete an object version or alter its lock settings *unless they have special permissions*.
	- *Compliance mode*: a protected object version can't be overwritten or deleted by any user, *including the root user* in your AWS account. When an object is locked in compliance mode, its *retention mode can't be changed*, and its *retention period can't be shortened*.

> [!tip] Compliance mode > Governance mode.

- **Object Retention Period**:
	- *Retention Period*: protect the object for a *fixed period*, it can be extended
	- **Legal Hold**: **protect the object indefinitely**, independent from retention period or the retention mode.
		- Can be *freely placed and removed* using the `s3:PutObjectLegalHold` IAM permission

- When you apply a *retention period to an object version explicitly*, you specify a `Retain Until Date` for the object version.
- *Different versions* of a single object *can have different retention modes and periods*.
- You can place a retention period on an object version either *explicitly* **or** *through a bucket default setting*. Explicit settings override the default bucket settings.

> [!question]- A startup uses Amazon S3 buckets for storing their customer data. The company has defined different retention periods for different objects present in their Amazon S3 buckets, based on the compliance requirements. But, the retention rules do not seem to work as expected. Which of the following points are important to remember when configuring retention periods for objects in Amazon S3 buckets? 
> - When you *apply a retention period* to an *object version explicitly*, you specify a `Retain Until Date` for the object version.
> - Different versions of a single object can have different retention modes and periods
> ---
> - You can place a retention period on an object version either explicitly or through a bucket default setting.
> - When you use bucket default settings, you don't specify a Retain Until Date.
> - If your *request to place an object version in a bucket contains an explicit retention mode and period, those settings **override** any bucket default settings for that object version*.