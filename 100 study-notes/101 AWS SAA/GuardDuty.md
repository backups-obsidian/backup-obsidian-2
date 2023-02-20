---
created: 2022-04-19 16:22
updated: 2023-02-20 09:16
---
---
**Links**: [[101 AWS SAA Index]]

---
- Amazon GuardDuty offers **threat detection** that enables you to continuously monitor and protect your AWS accounts, workloads, and **data stored in Amazon S3** or *EC2* machines.
- **Intelligent threat discovery** to protect AWS account.
- Uses ML algorithms, anomaly detection
- Input data includes:  
	- **VPC flow logs** 
	- **DNS logs** 
	- **CloudTrail** *data and management events*. Since S3 can be categorised under cloud trail data events it can also monitor data stored in S3.
    
- **Disabling** the service *will delete all remaining data*, including your findings and configurations before relinquishing the service permissions and resetting the service.
- Can protect against **crypto currency attacks**. It has a *dedicated finding* for it.

> [!question]- A systems administrator of a company wants to detect and remediate the compromise of *services such as Amazon EC2 instances and Amazon S3 buckets*.
> GuardDuty