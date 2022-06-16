---
created: 2022-04-18 18:50
updated: 2022-06-17 00:53
---
---
**Links**: [[../100 home | 100 Home]]
**Recommended Reads**: [[../102 AWS DVA/102 AWS DVA Index | AWS DVA Index]]

---

[[Introduction]]

## IAM
[[IAM Basics]]
[[STS & Identity Federation]]
[[Directory Services]]
[[Organisations]]
[[Miscellaneous]]

- **Random**
	- We can make calls to IAM using SDK or CLI but we can also make *direct calls to IAM web service* using the **Query API**(not recommended by AWS).
	- There shouldn't be an **explicit deny**.

## EC2
[[EC2 Basics]]
[[EC2 Instance Purchasing Options]]
[[EC2 Spot Instances & Fleet]]
[[EC2 Placement Groups]]
[[EC2 ENI]]
[[EC2 Hibernate]]
[[EC2 EBS]]
[[EFS]]
[[Instance store]]
[[EC2 AMI]]
[[EC2 Metadata]]
[[EC2 Lifecycle states]]

## ASGs
[[ASG]]
[[Scaling Policies]]
[[Termination Policy & Life Cycle Hooks]]
[[Launch Configuration-Template]]

## ELBs
[[ELB]]
[[Types of ELBs]]
[[Stickiness, Cross zone balancing & Connection Draining]]

## Databases
[[RDS]]
[[RDS Read Replica & Multi AZ]]
[[RDS Security]]
[[Aurora]]
[[ElastiCache]]
[[DynamoDB]]
[[Other Databases]]

## Route53
[[Route53]]
[[Route53 Routing Policies]]
[[Route53 Health Checks]]

## S3
[[S3]]
[[S3 Encryption]]
[[S3 Websites]]
[[S3 Replication]]
[[S3 Storage Classes]]
[[S3 Lifecycle Rules]]
[[S3 Performance]]
[[S3 Miscellaneous]]

## CloudFront & Global Accelerator
[[CloudFront]]
[[CloudFront Advanced]]
[[Global Accelerator]]

## AWS Storage
[[AWS Snow]]
[[AWS FSx]]
[[AWS Storage Gateway]]
[[AWS Transfer Family]]

## Decoupling Applications
[[SQS]]
[[SNS]]
[[Kinesis]]
[[Amazon MQ]]
[[Event Processing]]

> [!important]- **SQS vs SNS vs Kinesis**
> ![[attachments/Pasted image 20220425162952.png]]

## Containerisation
[[ECS]]
[[ECR & EKS]]

For *logging* in both EKC and ECS use **CloudWatch Container Insights** to *collect*, *aggregate*, and *summarise metrics* and logs from your containerised applications and micro services. 

## Serverless
In the beginning serverless just meant AWS Lambda but now it means anything that’s managed like databases, messaging, storage etc.
[[Lambda]]
[[API Gateway]]

- SAM (Serverless Application Model)
	- **Framework** for **developing**, **testing** and **deploying** serverless applications.
	- All the *configuration* is in **YAML**.
	- SAM can help us run Lambdas, DynamoDB, API Gateway etc **locally** in our system for **development**.
	- SAM can use **CodeDeploy to deploy Lambda functions**

## Encryption
[[KMS]]
[[SSM Parameter Store]]
[[Secrets Manager]]
[[CloudHSM]]

## Security
[[Shield]]
[[WAF & Firewall]]
[[GuardDuty]]
[[Inspector & Macie]]

## Monitoring and Auditing
[[CloudWatch]]
[[EventBridge]]
[[CloudTrail]]
[[Config]]

## VPC
[[VPC & Subnets]]
[[VPC Internet Access]]
[[DNS in VPC]]
[[NACLs]]
[[VPC Peering and Endpoints]]
[[AWS PrivateLink]]
[[VPC Flow Logs]]
[[Site to Site VPN]]
[[Direct Connect]]
[[Transit Gateway]]
[[IPv6 & Egress Internet Gateway]]

- *Traffic mirroring* to capture and inspect network traffic in our VPC in a *non intrusive manner*.
- **Networking Cost**:
	-  Always use *private IP over public IP* for cost savings.
		- In case of *inter AZ transfer* if you are using *private IP then it costs you less than public IP*.
		- In the same AZ no cost if using private IP.
	- You don't pay for data coming in, you always **pay for data going out of AWS**.
		- Try to keep as much internet traffic within AWS to minimise costs
			- ![[attachments/Pasted image 20220430214328.png]]
		- Examples on how to avoid egress charges
			- Use an Amazon EC2 instance in the same Region as the S3 bucket
			- Use lambda@edge for compressing data before sending to users.
		- Always use VPC Endpoints over NAT Gateway.
	- There is no price when data goes from S3 to CloudFront
		- ![[attachments/Pasted image 20220430214822.png]]

## Disaster Recovery
[[Disaster Recovery]]
[[DMS]]
[[DataSync & Backup]]

## Other Services
[[Other Services]]
[[ElasticBeanstalk]]
[[HPC in AWS]]

## Miscellaneous
- Billing is a **global** service.
- **Unpredictable load** always go for **serverless** option.
- All the snapshots are stored in S3.
- *CloudFront* allows you to increase the performance and reduce the cost of the application *without making any application changes*.

- Different layers of caching
	- ![[attachments/Pasted image 20220501161025.png]]

- Use *Golden AMI*, *EC2 User Data* and *ElasticBeanstalk* for instantiating applications quickly.
	- Create a *Golden AMI* with the *static installation components* already setup
	- Use *EC2 user data* to customise the *dynamic installation* parts at boot time


- Pillars of **well architected framework**.
	- *Operational Excellence*
	- *Security*
	- *Reliability*
	- *Performance Efficiency*
	- *Cost optimisation*

- **HA Bastion**: 
	- We will another create a bastion host in a new AZ for backup.
	- We can use a *NLB to route traffic* to the bastion hosts.
	- We **cannot use ALB** since it works for HTTP level traffic and for SSH we need TCP level traffic.

## References
- [AWS and Cloud Interview Questions - YouTube](https://www.youtube.com/playlist?list=PLiH9_MU-6RjJVmB9QznLHSO5OHrlWBSCh)
- Stephane Maerek Udemy course
- Tests in Udemy by Stephane, Neal Davis and Jon Bonso.