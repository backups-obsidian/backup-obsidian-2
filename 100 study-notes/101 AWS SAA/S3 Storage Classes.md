---
created: 2022-04-19 16:22
updated: 2022-05-06 08:28
---
---
**Links**: [[101 AWS SAA Index]]

---
- All the S3 storage classes are highly durable with **11 9's**.
- Except for One Zone IA in all the storage classes data is resilient in the event of the AZ goes down.
- Except for S3 Standard and S3 intelligent tiering there are **retrieval charges** for all storage classes.
- **Summary**
	- ![[attachments/Pasted image 20220423120550.png]]

> [!caution] **S3 Glacier** is the only storage class that *supports encryption by default* for both data at rest as well as in-transit

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
-   It will **automatically move objects** **between two (S3 general purpose and S3 IA)** access tiers based on changing access patterns.

> [!note] In general intelligent tiering won't be the ideal solution in any question because a fee is attached to it. It is only *useful if you have changing access patterns*.

## S3 Glacier
- **Low cost** storage meant for **archiving** and **backup**.
- Data is **retained** for **longer periods**, 10s of years. Alternative to on premise magnetic tapes.
- Cost per storage is really low ($ 0.004/GB) + **retrieval cost**
- **Each item** in glacier is called an “**archive**” and it can be **upto 40TB**.
- Archives are in **vaults** not buckets.
- Amazon Glacier *3 retrieval options*:
    -   **Expedited** (1 to 5 minutes)
    -   Standard (3 to 5 hours)
    -   Bulk (5 to 12 hours)

- **Provisioned capacity** ensures that your *retrieval capacity for expedited retrievals is available when you need it*. Each unit of capacity provides that at least three expedited retrievals can be performed every five minutes and provides up to 150 MB/s of retrieval throughput. *You should purchase provisioned retrieval capacity if your workload requires highly reliable and predictable access to a subset of your data in minutes*. Without provisioned capacity Expedited retrievals are accepted, except for rare situations of unusually high demand. However, if you require access to Expedited retrievals under all circumstances, you must purchase provisioned retrieval capacity.
	 ^78779e
- Minimum storage duration of **90 days**.

## S3 Glacier Deep Archive
- Amazon Glacier Deep Archive for long term storage cheaper:
    -   Standard (12 hours)
    -   Bulk (48 hours)
- Minimum storage duration of **180 days**

> [!caution]+ Main difference between Glacier and Deep Archive
> - No expedited retrieval option in deep archive 
> - Storage duration.
