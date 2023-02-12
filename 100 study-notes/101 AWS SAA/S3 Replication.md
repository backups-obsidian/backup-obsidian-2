---
created: 2022-04-19 19:08
updated: 2023-02-11 18:08
---
---
**Links**: [[101 AWS SAA Index]]

---

- There are two kinds of replication **CRR** (Cross Region Replication) and **SRR** (Same Region Replication).
	- Same-Region Replication (SRR) and Cross-Region Replication (CRR) can be configured at the *S3 bucket level*, a *shared prefix level*, or an *object level using S3 object tags*.
- For replication **both the buckets must have versioning enabled**.
- **Buckets can be in different account**.
- **S3 lifecycle actions are NOT replicated with S3 replication**
- Copying is **asynchronous**
- Use cases: 
	- CRR: *compliance*, *lower latency access*, replication across accounts
	- SRR: *log aggregation*, live replication between production and test accounts

- After activation **only new objects are replicated**. 
	- We can replicate existing objects using **S3 Batch Replication**.

> [!question]+ If you want to copy the data from one bucket to another then you should probably use `aws s3 sync` command. Why?
> As the data already exists in the source bucket, so you *cannot use cross-Region replication* because, by default, replication *only supports copying new Amazon S3 objects* after it is enabled. Replication enables automatic, asynchronous copying of objects across Amazon S3 buckets. 

- For DELETE operations:
	- *Can replicate delete markers* from source to target (optional setting)
	- **Deletions with a version ID are not replicated** (*to avoid malicious deletes*)

- There is **no chaining** of replication
	- If bucket 1 has replication into bucket 2, which has replication into bucket 3 then objects created in bucket are not replicated to bucket 3.

- The version ID of the replicated object will be same.

> [!tip] Go for replication incase the question asks for *HA* or *Disaster Recovery*.