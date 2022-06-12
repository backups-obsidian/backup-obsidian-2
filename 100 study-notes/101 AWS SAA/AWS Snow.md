---
created: 2022-04-24 20:22
updated: 2022-05-06 09:27
---
---
**Links**: [[101 AWS SAA Index]]

---
## Introduction
- Snow devices serve two main purpose:
	- **Migrate** data into and out of AWS.
		- ![[attachments/Pasted image 20220424202243.png]]
	- Collect and **process data at edge**.

- AWS Snow family are *offline devices to perform data migrations*.

> [!question]- Why data migrations with AWS Snow
> Sometimes we want to get our data to AWS fast but there are several **challenges** like
> - Limited Connectivity
> - *Limited Bandwidth*
> - Higher network cost
> - Connection stability

> [!important] If it **takes over a week to transfer data** to AWS over the network then use AWS Snowball devices.

- AWS will send you a device via post office, load the data into it and then send it back to AWS.

## Data Transfers
### Snowcone
-   It is a **small** portable rugged computing which can **withstand harsh environments**.
-   The amount of **usable data** that can be stored in it is **8TBs**.
-   It should be *used where Snowball Edge cannot be used due to space constraints*.
-   We can send it back to **AWS offline** or **connect** it to the internet to send data via **AWS DataSync**.

> [!important] Snowcone is the only offering in which data can be *transferred online* using DataSync agent. In both Snowball Edge and Snowmobile data is transferred offline.

### Snowball Edge - Storage Optimised
- Storage optimised version has **80 TB** of space.
- Used for transferring **TBs** and **PBs** (few PBs *like 5*). Although capacity is 80 TBs you can *order multiple*.
- 40 vCPUs and 80 GiB of RAM.
- Provides **block storage** and **S3 compatible object storage**.
- **Use Cases**: *large cloud migrations*, disaster recovery

> [!caution]- Snowball **cannot import to Glacier directly**. You must use Amazon S3 first, in combination with an S3 lifecycle policy.

> [!note] Both snowball edge compute and storage optimised allow for **storage clustering**.

### Snowmobile
- It is used for **transferring EBs** of data
- It is literally a *truck*.
- Each truck can transfer upto **100PBs** of data.
- The truck is highly secure, temperature controlled, GPS etc.
- If you are transferring **more the 10 PBs** then you should consider **AWS Snowmobile**.

## Edge Computing
- *Process data while it is being created at an Edge Location*. Edge location is anything that is offline and doesn’t have access to the internet.
- **Use Cases**: *Preprocess data*, *Machine learning at Edge*, Transcoding media streams
- Long term *discounted price for 1 and 3 years*.
- *Can run EC2 Instances* & *AWS Lambda functions* (using AWS loT Greengrass)

### Snowcone 
- 2 CPUs, 4 GB of memory, wired or wireless access
- USB-C power using a cord or the optional battery

### Snowball Edge - Compute Optimised
- 52 vCPUs, 208 GiB of RAM
- *Optional GPU* (useful for video processing or machine learning)
- *42 TB usable storage*

## AWS OpsHub
- *Historically* to deal with the snow family of devices *one had to use CLI*.
- **AWS OpsHub** is something you can download on your desktop to **manage** the **snow devices**.