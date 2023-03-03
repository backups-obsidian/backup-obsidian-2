---
created: 2022-04-19 10:04
updated: 2023-03-03 15:52
---
---
**Links**: [[101 AWS SAA Index]]

---
## IOPS, Throughput and Latency
-  **IOPS is for random read and writes**, **throughput is for sequential read and writes**.
-   Throughput is measured in **MB/s**.
-   **IOPS is just measured in count**, no of successful random read and writes.
-   Latency is how much time it takes for the response of each request (like read, write, delete).

## EBS
- It is a type of **block storage**.
- [[EFS]] and EBS are **network attached** storage options. Since they are network attached storage options there is a bit of latency.
- [[Instance store]] is **hardware attached** storage.

> [!caution]+ EBS volume is bound to a **specific AZ** just like an ENI. The EC2 instance and the EBS must be in the same AZ.
> To use the EBS volume in a different AZ we have to use **snapshots**.

- We can create EBS volumes and leave them unattached.
- We can *only attach an EBS volume to a single instance* **unless** they are from the `io1/io2` family class.
- By default on termination *root EBS volumes are destroyed* unless **DeleteOnTermination** is unchecked. This can be done in 2 ways:
	- *Unchecking* it at the *time of launching the instance*.
	- Set the `DeleteOnTermination` attribute to False using the **command line**.
		- This can be done even while the instance is running and when it is stopped or terminated the EBS volume won't be terminated. 
- Non root volumes are never destroyed

- EBS is *automatically replicated in an AZ* so if the primary one goes down it can be replaced with secondary, without you knowing.
- You have to mount a non root volume EBS volume to an EC2 instance.
- EBS volumes **support live configuration changes**. So this means in production you can modify the volume type, volume size, IOPS without service interruptions.
- EBS volumes attached to stopped EC2 instances incur costs.

### EBS Encryption
- There is no direct way to change the state of encryption.
- Encryption is **managed by AWS** and has minimum impact on latency.

> [!caution] All EBS types and all *instance families* support encryption but *not all instance types* support encryption.

> [!info]+  When you create an **encrypted EBS volume**, you get the following
> -   *Data at rest is encrypted inside the volume*
> -   All the *data in transit, moving between the instance and the volume is encrypted*
> -   All *snapshots are encrypted* since snapshots of encrypted volumes are encrypted and snapshots of unencrypted volumes are unencrypted.
> -   All *volumes created from the snapshot are encrypted*

- You can have encrypted an unencrypted EBS volumes attached to an instance at the same time.

> [!important]+ How to encrypt and unencrypted snapshot?
> *Copy of an unencrypted snapshot creates an unencrypted snapshot that allows for encryption*. This feature comes in handy when you want to encrypt and unencrypted EBS volume
> ---
> - Take the unencrypted volume and create a snapshot.
> - Take the snapshot and create a copy snapshot which can be encrypted.
> - Create an encrypted volume from the encrypted snapshot.

- Volumes can be encrypted using AWS *managed keys by AWS KMS* or using your *own keys in AWS KMS*.
- If you have encrypted a volume using a custom key then to transfer the snapshots between accounts you need to share the custom key also so unencrypt the snapshots.
- Note that you **cannot share encrypted volumes created using a default CMK key** and you cannot change the CMK key that is used to encrypt a volume.

### EBS Snapshots
- We can create snapshots of our EBS volume at any point of time.
- It is *advised* to detach the EBS volume while making a snapshot but it is not a hard requirement. *EBS volumes can be used while snapshots are being taken*.
- Snapshot creation is asynchronous.

> [!info] We can **copy snapshots across AZs and regions** and then create volumes from these snapshots in that particular region or AZ. This is how we can transfer volumes from one AZ or region to another.

- **Snapshots of Amazon EBS volumes are stored on S3** by design so you only need to take a snapshot and it will *automatically be stored on Amazon S3*.
- To prevent manual snapshots we can use **Amazon Data Lifecycle Manager** (Amazon DLM) to *automate the creation, retention, and deletion of snapshots*.
- EBS **snapshots are incremental**. This means only the blocks that have been changed since the last snapshot are saved in the new snapshot.
- Although EBS volumes are incremental you can *delete previous EBS snapshots* to clear up space and *AWS will make sure that no data is lost*.
- *EBS snapshot archive*:
	- Move a snapshot to **archive tier** that is 75% cheaper.
	- *Takes 24 to 72 hours* to restore from the archive.
- By default when we delete snapshots they are gone but we can setup a *recycle bin*.
	- Recycle bin will have all the delete snapshots.
	- Setup rules to retain deleted snapshots so you can recover them after an accidental deletion
	- We can specify retention period after which they will be deleted (from 1 day to 1 year)

#### Amazon DLM
- Amazon Data Lifecycle Manager (**Amazon DLM**) automates the **creation**, **retention**, and **deletion of snapshots** taken to back up your Amazon EBS volumes.
- If you see keywords like **automated**, **fast**, **efficient**, **cost effective**, **backup** with a question in EBS then go for DLM.

### Volume Types
- They are of **6** types
- EBS volumes are characterised using **size**, **throughput** and **IOPS**.
- Only the *SSD volumes* (gp and io series) can be **used as boot volumes**.
- Both the HDD volumes do not provide any SLA for IOPS.

> [!tip]- For *small random* IO operations go with *SSD* volumes. For *large sequential* IO operations use *HDD* volumes.
> An example of *small random* IO operations will be *database operations*. Example of *large sequential* IO operations would be big *data, data warehousing, log processing*.

- Depending on the **raid type** we can either increase **redundancy (RAID 1)** or **IOPS (RAID 0)** ^e75d97
	- **RAID 0**: *striping data* is written across multiple disks and *increases performance but no redundancy*.
	- **RAID 1**: *data mirroring, creates 2 copies of the data* but *does not increase performance, only redundancy*.

| Class | Size (GiB) - (TiB) | IOPS                   | Maximum Throughput                     |
| ----- | ------------------ | ---------------------- | -------------------------------------- |
| gp3   | 1 - 16             | 3000 - 16000           | 125 MiB/s - 1000 MiB/s (independently) |
| gp2   | 5334 GB            | 3000 - 16000           |                                        |
| io1   | 4 - 16             | 32000 - 64000 (Nitro)  |                                        |
| io2   | 4 - 64             | 256000 (Block Express) |                                        |
| st1   | 500 - 16           | 500                    | 500 MiB/s (baseline 40 MiB/s)          |
| sc1   | 500 - 16           | 250                    | 250 MiB/s (baseline 12 MiB/s)          |

#### gp2/gp3
- General purpose SSD volume that balances price and performance.
- gp3 is the newer version of gp2.
- For **gp3 IOPS and throughput can be scaled independently of each other**.
- For **gp2 IOPS and volume are linked**. **3 IOPS per GB** and we can have a maximum of 16000 IOPS so **5334 GB** (**5.3 TiB**).
- Whenever you see terms like **cost effective storage** in questions go for the gp family.
- It uses a bucket and credit model for calculating IOPS.

#### io1/io2
- High performance SSD volume used for *low latency high throughput tasks*.
- When you need sustained IOPS performance or **IOPS greater than 16000**.
- io2 gives more durability and more IOPS per GB at the price of io1. **So always go with io2**.
- We get *higher IOPS with EC2 Nitro instances*.
- **50 IOPS per GB till 64000 IOPS**. So if you are given a volume of size `x` then maximum IOPS of that volume will be `50x`. 
- Can go upto **256000 IOPS with block express volume**. This is equivalent to *1000 IOPS per GB*.
- If you need **IOPS > 256000** then you have to use **instance store**.
- If you see the terms like **databases** in question then go for io family.
- Supports **multi attach** (attaching single EBS volume to multiple EC2 instances). Best option is to use [[EFS]].
	- For multi attach you have to use a different file system (cluster aware).
	- All the *instances must be in the same AZ*.
- In questions try to maintain *low queue length* for best performance.

> [!info] In `gp` family IO *increases as the size of volume increases*. In `io` family IO can increase **independently**.

##### EC2 Nitro
-  **If you want higher EBS IOPS then you need to use EC2 nitro**.
-   It is *not available for all instance types*.
-   EC2 nitro is a **new virtualisation technology** for the next generation of EC2 instances.

#### st1
- **Low cost HDD** volume. Designed for *frequently accessed throughput intensive workloads*.
- If you see terms like **frequent, sequential/large IO, high throughput** operations then go for st1.

#### sc1
- **Lowest cost HDD** volume. Designed for *infrequently accessed workloads*.
- If you see terms like **infrequent, sequential/large IO** operations then go for sc1.


## Miscellaneous
- New EBS volumes are raw block devices and *do not contain any partition or file system*. 
	- You need to login to the instance and then format the EBS volume with a file system, and then mount the volume for it to be usable.