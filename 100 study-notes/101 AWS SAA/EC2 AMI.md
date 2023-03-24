---
created: 2022-04-19 16:22
updated: 2023-03-22 08:43
---
---
**Links**: [[101 AWS SAA Index]]

---
## AMI
- Built for a **specific region**. 
	- **Can be copied to other regions**.
	- In case of a DR strategy where EC2 instances have to be booted up from a single AMI make sure that the *AMI has been copied to all the regions*.
	- We can migrate EC2 instances from one AZ to another.
- If you have a *custom AMI then the boot time will be faster*. You have to make and maintain you own custom AMIs.
- AMIs *can be shared* with other AWS accounts.
- There are 3 options for AMIs: 
	- *Public*: AWS provided 
	- *Marketplace*: Made by someone else and potentially sold.
	- *Custom AMIs*: Made and maintained by us.
- **Process** of creating an AMI:
	- *Start an EC2 instance* and *customise it*.
	- *Stop* the instance (for data integrity)
	- *Build an AMI* - this *will* also *create EBS snapshots*
	- Launch instances from other AMIs
- **AMIs are stored in S3** but we cannot view them.
- We can copy EBS and instance store backed AMIs. 
- There are *no charges on copying an AMI*. 
- Use a **Golden AMI** to get EC2 instances ready quicker. In the Golden AMI install your applications, OS dependencies etc, all the *static installation components*.

> [!important] When the new AMI is copied from region A into region B, it **automatically creates a snapshot in region B because AMIs are based on the underlying snapshots**. 

> [!info]- If you copy an EBS-backed AMI, you will incur charges for the storage of any additional EBS snapshots.
> This means when you copy an EBS backed AMI from an EC2 instance a snapshot is made of the EBS volume. So when AMIs are copied to other regions EBS snapshots are also copied. When you create an EC2 instance from the copied AMI the copied EBS snapshot is used to create a volume.

> [!tip]+ AMIs can help in disaster recovery. How?
> - You can copy an Amazon Machine Image (AMI) of an EC2 instance (EBS volume is also copied) and specify the second Region for the destination.
> - Launch a new EC2 instance from an Amazon Machine Image (AMI) in the second Region

> [!note]- *Copying a source AMI* results in an identical but **distinct target AMI ID**.