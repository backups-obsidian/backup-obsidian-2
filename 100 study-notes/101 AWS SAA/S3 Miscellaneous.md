---
created: 2022-04-23 15:21
updated: 2023-03-01 19:26
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
- The **target listeners** (**only 3**) to these events can be 
	- *SQS* 
	- *SNS* 
	- *Lambda functions*.

- Generally the notifications are delivered in a second but sometimes it can take minutes

> [!tip]- For best performance and making sure you get the event *enable versioning*. Why?
> Your team-mate has configured an Amazon S3 event notification for an S3 bucket that holds sensitive audit data of a firm. As the Team Lead, you are *receiving the SNS notifications for every event in this bucket*. After validating the event data, you realised that *few events are missing*. What could be the reason for this behaviour and how to avoid this in the future?
>
> If two writes are made to a single non-versioned object at the same time, it is possible that only a single event notification will be sent. Solution is to enable versioning.

> [!caution] Only normal SQS is allowed. **SQS FIFO is NOT allowed**.

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
- Use case: *analytics*, *BI*, *analyse logs*(VPC flow logs, CloudTrail trails)
- It is much **cheaper** than Redshift and EMR.

> [!tip] Go for Athena if you see keywords like *cost effective*, *easiest*, *quickly*, *serverless* (minimise operational overhead), *adhoc*

## Glacier Vault Lock
- Adopt a **WORM (write once read many)** model.
- It is helpful for **compliance** and at **data retention**
- An object would go in the glacier and you will add a glacier vault lock policy saying that the **object cannot be deleted** and the **policy** itself **cannot be deleted**.

> [!note] A vault is a container for *storing archives on Glacier*. Vault Lock is *only for Glacier and not for S3*.

## S3 Object Lock
- To use this we **must** **enable versioning**
- It **blocks object version deletion** for a specified amount of time.

> [!caution] In both the locks you won't be able to upload modified versions of the object. So read the question carefully. Unless and until *compliance* is mentioned don't go for locks.

- **Object Retention Period**:
	- *Retention Period*: specifies a fixed period
	- *Legal Hold*: same protection, *no expiry date*

- **Retention Modes**:
	- *Governance mode*: users can't overwrite or delete an object version or alter its lock settings *unless they have special permissions*.
	- *Compliance mode*: a protected object version can't be overwritten or deleted by any user, *including the root user* in your AWS account. When an object is locked in compliance mode, its *retention mode can't be changed*, and its *retention period can't be shortened*.

> [!tip] Compliance mode > Governance mode.

- When you apply a *retention period to an object version explicitly*, you specify a `Retain Until Date` for the object version.
- *Different versions* of a single object *can have different retention modes and periods*.
- You can place a retention period on an object version either *explicitly* **or** *through a bucket default setting*. Explicit settings override the default bucket settings.