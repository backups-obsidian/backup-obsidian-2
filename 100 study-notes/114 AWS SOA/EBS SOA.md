---
created: 2023-03-03 15:30
updated: 2023-03-11 09:09
---
---
**Links**: [[114 AWS SOA Index]]

---
## Volume Resizing
- We can **only increase** an EBS volume's **size** or **IOPS**.
	- We can also change the volume type like from gp2 to gp3.
- After resizing an EBS volume, **we need to repartition your drive**.
- After increasing the size, it's *possible for the volume to be in the  "optimisation" phase for a long time*. The volume is still usable.
	- There are 3 stages: *modifying, optimising, completed*.

> [!note]- We *CANNOT decrease* the size of an EBS volume.
> We can create a smaller volume and then *migrate* the data.

> [!important]+ **Resizing of an instance is only possible if the root device for your instance is an EBS volume**.
> - If the root device for your instance is an EBS volume, you can change the size of the instance simply by changing its instance type, which is known as resizing it. 
> - *If the root device for your instance is an instance store volume*, you **must migrate your application to a new instance (by creating an AMI)** with the instance type that you need.

## Fast Snapshot Restore (FSR)
- EBS Snapshots stored in S3
- By *default*, there's a *latency of I/O operations the first time* each block is accessed since the **block must be pulled from S3**.
- *Solution*: 
	- Force the initialisation of the entire volume (using the dd or fio command), or 
	- **Enable FSR**
- FSR helps you to create a volume from a *snapshot that is fully initialised at creation* (no I/O latency).
- Enabled for a snapshot in a particular AZ (billed per minute - **very expensive**)
- Can be enabled on snapshots created by Data Lifecycle Manager.

## Questions
> [!question]- An e-commerce company runs its web application on Amazon EC2 instances backed by Amazon Elastic Block Store (Amazon EBS) volumes. An Amazon S3 bucket is used for storing sharable data. *A developer has attached an Amazon EBS to an Amazon EC2 instance, but it’s still in the "attaching" state after 10-15 minutes*. As a SysOps Administrator, what solution will you suggest to fix this issue with the EBS volume?
> **Check that the device name you specified when you attempted to attach the EBS volume isn't already in use**. Attempt to attach the volume to the instance, again, but use a different device name.