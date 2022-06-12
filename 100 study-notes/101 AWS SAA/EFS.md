---
created: 2022-04-19 16:22
updated: 2022-05-05 16:26
---
---
**Links**: [[101 AWS SAA Index]]

---
- It is a **managed** and **shared NFS (network file system)** 
- It can be **mounted on many EC2 across AZs** (shared component). EBS was locked in a single AZ. This means **EFS is a regional service**.
- It is quite expensive as compared to EBS volumes. It is *3 times the cost of a gp2 drive*.

>[!important]+ You **pay for only what you use**. In EBS volumes you pay for the whole volume even though you are not using it.
> - With *Amazon EFS*, you pay only for the resources that you use. The EFS Standard Storage pricing is **$0.30 per GB per month**. Therefore the cost for storing the test file (1GB) on EFS is $0.30 for the month.
> - For *EBS General Purpose SSD (gp2)* volumes, the charges are **$0.10 per GB-month** of provisioned storage. Therefore, for a provisioned storage of 100GB for this use case, the monthly cost on EBS is *$0.10\*100 = $10*. This **cost is irrespective of how much storage is actually consumed by the test file**(1 GB).
> - For *S3 Standard storage*, the pricing is **$0.023 per GB per month**. Therefore, the monthly storage cost on S3 for the test file is $0.023.

- It is scalable, highly available **POSIX compliant** file system.
-  Whenever you see terms like *POSIX*, *concurrent connection from multiple EC2 instances*, low latency *file operations* in questions go for EFS.

>[!tip]- If you *don't require concurrent accessible storage* then always go for EBS volumes.
> EBS offers less latency than EFS.

- Don't modify permissions of root directory, create a subdirectory for each user.

>[!caution] It is **not block storage** like EBS and Instance stores. It is a **file based storage system**.

- **Only works with linux based AMIs**.
- We use **security groups to control access to EFS**. If you want more security POSIX permissions can be used to restrict host from hosts based on users and groups.
- Use an *lAM policy* to control access for clients who can mount your file system with the required permissions.
- Data is *encrypted in transit and at rest*.
- You can connect to Amazon EFS file systems from EC2 instances in other AWS regions using an inter-region VPC peering connection, and from on-premises servers using an AWS VPN connection

- **Storage tiers**: If the file has not been accessed for **N days** (generally 7) then it will be moved from standard to infrequent access
	- *Standard*: For frequently accessed file.
	- Infrequent access (*EFS - IA*): cost to retrieve, lower cost to store.

- EFS can scale to *petabyte storage automatically* and support 1000 NFS clients concurrently.

- **Performance mode**: Set at the time of EFS creation. **Defaults to General Purpose**.
	- *General purpose*: **latency sensitive** use cases. *Web servers*, *CMS*.
	- *Max IO*: -   **High latency**. 
		- Max I/O performance mode is used to scale to higher levels of **aggregate throughput** and operations per second. This *scaling is done with a tradeoff of slightly higher latencies* for file metadata operations. **Highly parallelised applications** and workloads, such as **big data analysis, media processing**, and genomic analysis, can benefit from this mode. 
		- Suitable for *high frequency read and write operations*.

- **Throughput mode**: **Defaults to bursting**.
	- **Bursting**: *File-based workloads* are typically *spiky*, driving high levels of throughput for short periods of time, and low levels of throughput the rest of the time
	- **Provisioned**: *high frequency reading and writing*, better performance than bursting.

> [!note]+ Bursting vs Provisioned
> By default, AWS recommends that you run your application in the Bursting Throughput mode. But, if you're *planning to migrate large amounts of data into your file system*, consider switching to **Provisioned Throughput** mode.