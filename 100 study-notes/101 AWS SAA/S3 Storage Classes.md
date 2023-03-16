---
created: 2022-04-19 16:22
updated: 2023-03-16 08:20
---
---
**Links**: [[101 AWS SAA Index]]

---
- All the S3 storage classes are highly durable with **11 9's**.
- Except for One Zone IA in all the storage classes data is resilient in the event of the AZ goes down.
- Except for S3 Standard and S3 intelligent tiering there are **retrieval charges** for all storage classes.
- **Summary**
	- ![[attachments/Pasted image 20220423120550.png]]

> [!caution] **S3 Glacier** is the only storage class that *supports encryption by default* for both data at rest as well as in-transit.

## S3 Standard - general purpose
- For general purpose use
- There is **no minimum storage duration**. If you have to store something for less than 24hrs then S3 Standard is the most cost effective option.
- Best for *short term storage*.
- If you *don't know the size of objects*, go for S3 standard.

## S3 Standard IA
- Suitable for *less frequently accessed data* but requires **rapid access when needed**.
- **Low cost** as compared to *general purpose*.
- Minimum storage duration of **30 days**.
- There is a minimum size of the object.
- **Use cases** → data store for **backups** or **disaster recovery**
- Availability is less than S3 standard.

> [!question]- A Company stores a large volume of non-critical log files in an Amazon S3 bucket. An Amazon EC2 instance processes files from the bucket on a *daily basis*. Which storage option will be the MOST cost-effective for this scenario?
> S3 Standard.
> ---
> S3 Standard-Infrequent Access won't be the correct choice since the data is being frequently accessed and **there would be additional retrieval costs**.

## S3 One Zone IA
- **Same as IA** but the data is stored in a **single AZ**.
- **Lower cost (20%)** as compared to *standard IA*.
- Minimum storage duration of **30 days**.
- There is a minimum size of the object.
- **Use Cases**: 
	- storing *secondary backup copies* 
	- any type of *data which we can recreate*.

> [!tip]- If you need something which is *highly available* and less frequently accessed then go for S3 IA over S3 IA One Zone *if cost has not been mentioned*.
> *S3 IA is more available than S3 IA one zone*.

> [!tip] When asked for the *most cost effective* option then go for IA One Zone over Standard IA.

> [!tip] If question asks for *HA* then *don't go for One Zone*.

## S3 Intelligent Tiering
-   Small *monthly monitoring* and *auto tiering* **fee**.
-   It will **automatically move objects** between access tiers based on changing access patterns.
- Different access tiers:
	- *Frequent Access tier (automatic)*: **default** tier
	- *Infrequent Access tier (automatic)*: objects not accessed for 30 days
	- *Archive Instant Access tier (automatic)*: objects not accessed for 90 days
	- *Archive Access tier (optional)*: configurable from 90 days to 700+ days
	- *Deep Archive Access tier (optional)*: config. from 180 days to 700+ days

> [!note] In general intelligent tiering won't be the ideal solution in any question because a fee is attached to it. It is only *useful if you have changing access patterns*.

## S3 Glacier
- **Low cost** storage meant for **archiving** and **backup**.
- Data is **retained** for **longer periods**, 10s of years. Alternative to on premise magnetic tapes.
- We **pay for storage** as well as the **retrieval cost**
- **Each item** in glacier is called an “**archive**” and it can be **upto 40TB**.
- Archives are in **vaults** not buckets.
- It has its own UI.
- *Vault Operations*:
	- Create & Delete - delete only when there's no archives in it
	- Retrieving Metadata - creation date, number of archives, total size of all archives, ...
	- Download Inventory - list of archives in the vault (archive ID, creation date, size, ...)
- *Glacier Operations*:
	- Upload - single operation or by parts (MultiPart upload) for larger archives
	- *Download* - *first initiate a retrieval job for the archive*, Glacier then prepares it for download. 
		- **User then has a limited time to download the data before the restore link expires**. 
		- Once the link expires we have to request another one.
	- Delete - use Glacier Rest API or AWS SDKs by specifying archive ID

### Vault policies and Vault lock
- Each Vault has:
	- **ONE vault access policy**
	- **ONE vault lock policy**
- *Vault Access Policies* are written in JSON and are *similar to a bucket policy* (restrict user / account permissions)
- *Vault Lock Policy* is a policy you lock, for **regulatory and compliance requirements**.
	- The **policy is immutable**, it can never be changed
	- Example 1: forbid deleting an archive if less than 1 year old
	- Example 2: implement WORM policy (write once read many)

### Notifications for Restore Object
- **Vault Notification Configuration**
	- Configure a vault so that when a *job completes*, a *message is sent to SNS*
		- ![[attachments/Pasted image 20230304090828.png]]
	- Optionally, specify an SNS topic when you initiate a job
- **S3 Event Notifications**
	- S3 supports the restoration of objects archived to S3 Glacier storage classes
	- `s3:ObjectRestore:Post`: notify when *object restoration initiated*
	- `s3:ObjectRestore:Completed`: notify when *object restoration completed*

### Three storage classes in glacier
#### S3 Glacier Instant Retrieval
- **Millisecond** retrieval, great for *data accessed once a quarter*.
- Minimum storage duration of **90 days**.

#### S3 Glacier Flexible Retrieval
- Amazon Glacier *3 retrieval options*:
    -   **Expedited** (1 to 5 minutes): *Most expensive* out of the 3.
    -   **Standard (3 to 5 hours)**
    -   Bulk (5 to 12 hours): *Least expensive* out of the 3.
- Minimum storage duration of **90 days**.

- **Provisioned capacity** ensures that your *retrieval capacity for expedited retrievals is available when you need it*. Each unit of capacity provides that at least three expedited retrievals can be performed every five minutes and provides up to 150 MB/s of retrieval throughput. *You should purchase provisioned retrieval capacity if your workload requires highly reliable and predictable access to a subset of your data in minutes*. Without provisioned capacity Expedited retrievals are accepted, except for rare situations of unusually high demand. However, if you require access to Expedited retrievals under all circumstances, you must purchase provisioned retrieval capacity.
	 ^78779e
- Minimum storage duration of **90 days**.

#### S3 Glacier Deep Archive
- Amazon Glacier Deep Archive for *long term storage* cheaper:
    -   Standard (12 hours)
    -   Bulk (48 hours)
- Minimum storage duration of **180 days**