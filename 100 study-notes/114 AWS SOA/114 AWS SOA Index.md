---
created: 2023-03-01 15:32
updated: 2023-03-11 08:29
---
---
**Links**: [[../100 home | 100 Home]]
**Recommended Reads**:
- [[../101 AWS SAA/101 AWS SAA Index | AWS SAA]]
- [[../102 AWS DVA/102 AWS DVA Index | AWS DVA]]

---
## EC2
[[../101 AWS SAA/EC2 Basics | EC2 Basics]]
[[../101 AWS SAA/EC2 Placement Groups | EC2 Placement Groups]]
[[EC2 Troubleshooting]]
[[../101 AWS SAA/EC2 Instance Purchasing Options | EC2 Instance Purchasing Options]]
[[../101 AWS SAA/EC2 Spot Instances & Fleet | EC2 Spot Instances and Fleet]]
[[EC2 Burstable Instances]]
[[EC2 Status Checks]]
[[../101 AWS SAA/EC2 Hibernate | EC2 Hibernate]]
[[EC2 - SOA]]

## AMI
[[../101 AWS SAA/EC2 AMI | EC2 AMI]]
[[AMI - SOA]]

## Systems Manager (SSM)
[[Systems Manager (SSM)]]
[[SSM Documents]]
[[SSM Automations]]
[[../101 AWS SAA/SSM Parameter Store | SSM Parameter Store]]
[[SSM Inventory]]
[[SSM State Manager]]
[[SSM Patch Manager]]
[[SSM Session Manager]]

- **OpsWorks** is for using **managed** *chef (Recipes)* or *Puppet (manifests)* on AWS.
	- It is an alternative to SSM.

## ELB
[[../101 AWS SAA/ELB | ELB]]
[[../101 AWS SAA/Types of ELBs | Types of ELBs]]
[[../101 AWS SAA/Stickiness, Cross zone balancing & Connection Draining | Stickiness, Cross zone balancing & Connection Draining]]
[[ELB Monitoring and Troubleshooting]]
[[ELB SOA]]

## ASG
[[../101 AWS SAA/ASG | ASG]]
[[../101 AWS SAA/Launch Configuration-Template | Launch Configuration and Template]]
[[../101 AWS SAA/Scaling Policies | Scaling Policies]]
[[../101 AWS SAA/Termination Policy & Life Cycle Hooks#Life cycle hooks | Life cycle hooks]]
[[ASG SOA]]

## ElasticBeanstalk
[[ElasticBeanstalk SOA]]

## CloudFormation
[[../102 AWS DVA/CloudFormation | CloudFormation]]
[[../102 AWS DVA/CloudFormation Template Components | CloudFormation Template Components]]
[[../102 AWS DVA/CloudFormation Intrinsic Functions | CloudFormation Intrinsic Functions]]
[[../102 AWS DVA/CloudFormation RollBacks | CloudFormation Rollbacks]]
[[../102 AWS DVA/CloudFormation Advanced | CloudFormation Advanced]]
[[CloudFormation SOA]]

## Lambda
[[../101 AWS SAA/Lambda | Lambda]]
[[../102 AWS DVA/Lambda IAM Role | Lambda Permissions]]
[[../102 AWS DVA/Lambda Configuration & Deployments#Logging & Tracing | Logging & Tracing]]
[[../102 AWS DVA/Lambda Configuration & Deployments#Tracing | X-ray tracing]]
[[../102 AWS DVA/Lambda Performance | Lambda Performance]]
[[../102 AWS DVA/Lambda Configuration & Deployments#Lambda Concurrency | Lambda Concurrency]]
[[Lambda Monitoring]]

- EventBridge lambda - Asynchronous, resource based policies
- S3 Events - Asynchronous, resource based policies

## EBS & EFS
[[../101 AWS SAA/EC2 EBS | EBS]]
[[../101 AWS SAA/Instance store | Instance Store]]
[[EBS SOA]]
[[../101 AWS SAA/EFS | EFS]]
[[EFS SOA]]

## S3
[[../101 AWS SAA/S3 | S3]]
[[../101 AWS SAA/S3 Websites | S3 Websites]]
[[../101 AWS SAA/S3 Encryption | S3 Encryption]]
[[../101 AWS SAA/S3 Replication | S3 Replication]]
[[../101 AWS SAA/S3 Storage Classes | S3 Storage Classes]]
[[../101 AWS SAA/S3 Lifecycle Rules | S3 Lifecycle Rules]]
[[../101 AWS SAA/S3 Performance | S3 Performance]]
[[../101 AWS SAA/S3 Miscellaneous | S3 Miscellaneous]]
[[../102 AWS DVA/S3 Access Points | S3 Access Points]]

- VPC Endpoint Gateway:
	- `vpce`: one or more endpoints
	- `vpc`: all endpoints
	- ![[attachments/Pasted image 20230304103959.png]]

## Advanced Storage
[[../101 AWS SAA/AWS Snow | AWS Snow]]
[[../101 AWS SAA/AWS FSx | AWS FSx]]
[[../101 AWS SAA/AWS Storage Gateway | AWS Storage Gateway]]
[[AWS Storage Gateway SOA]]

## CloudFront
[[../101 AWS SAA/CloudFront | CloudFront]]
[[CloudFront SOA]]
[[CloudFront Caching SOA]]

## Databases
[[../101 AWS SAA/RDS | RDS]]
[[../101 AWS SAA/RDS Read Replica & Multi AZ | RDS Read Replica & Multi AZ]]
[[../101 AWS SAA/RDS Security | RDS Security]]
[[../102 AWS DVA/RDS Proxy | RDS Proxy]]
[[RDS SOA]]
[[Aurora SOA]]
[[../101 AWS SAA/ElastiCache | ElastiCache]]
[[../102 AWS DVA/ElasticCache Replication | ElastiCache Replication]]
[[ElastiCache SOA]]

## Monitoring
[[../101 AWS SAA/CloudWatch | CloudWatch]]
[[../101 AWS SAA/EventBridge | EventBridge]]
[[../101 AWS SAA/CloudTrail | CloudTrail]]
[[../101 AWS SAA/Config | Config]]

- Difference between CloudWatch, Config and CloudTrail (Example Load Balancer)
	- ![[attachments/Pasted image 20230306101317.png]]

## Account Management
[[Account Management]]
[[../101 AWS SAA/Organisations | Organisations]]
[[AWS Control Tower]]
[[AWS Service Catalog]]
[[AWS Budgets]]
[[AWS Compute Optimiser]]

## Disaster Recovery
[[../101 AWS SAA/DataSync & Backup | DataSync & Backup]]

## Security & Compliance
[[Shared Responsibility Model]]
[[../101 AWS SAA/Shield, WAF & Firewall Manager | Shield, WAF & Firewall]]
[[../101 AWS SAA/Inspector & Macie | Inspector & Macie]]
[[../101 AWS SAA/GuardDuty | GuardDuty]]
[[../101 AWS SAA/Other Services#Trusted Advisor | Trusted Advisor]]
[[../101 AWS SAA/KMS | KMS]]
[[KMS SOA]]
[[../101 AWS SAA/CloudHSM | CloudHSM]]
[[AWS Artifact]]
[[../102 AWS DVA/Other Services DVA#ACM (AWS Certificate Manager) | AWS Certificate Manager]]
[[../101 AWS SAA/Secrets Manager | Secrets Manager]]
[[../102 AWS DVA/SSM Parameter Store vs Secrets Manager | SSM Parameter Store vs Secrets Manager]]

- We can do pentesting on cloud but it is limited to only a few services.
	- We *CANNOT do DoS or DDoS attacks on our system*.
	- Cannot perform pentesting on Route53.

## Identity
[[../102 AWS DVA/Security Miscellaneous Services | Security Miscellaneous Services]]
[[../101 AWS SAA/STS & Identity Federation | STS & Identity Federation]]
[[../102 AWS DVA/STS & IAM Advanced DVA | STS Advanced]]
[[../102 AWS DVA/Cognito | Cognito]]
[[AWS IAM Identity Centre]]

## Route53
[[../101 AWS SAA/Route53 | Route53]]
[[../101 AWS SAA/Route53 Routing Policies | Route53 Routing Policies]]
[[../101 AWS SAA/Route53 Health Checks | Route53 Health Checks]]
[[Route53 SOA]]

## VPC
![[../101 AWS SAA/101 AWS SAA Index#VPC]]

## Other Services
[[Other Services SOA]]

## Extra Notes
[[SOA Extra Notes]]

## Resources
- [AWS Certified Sysops Administrator - Associate Certification (amazon.com)](https://aws.amazon.com/certification/certified-sysops-admin-associate/)
	- Scroll to the bottom to the exam review section for practice questions.
- [AWS-Certified-SysOps-Administrator-Associate_Exam-Guide.pdf (awsstatic.com)](https://d1.awsstatic.com/training-and-certification/docs-sysops-associate/AWS-Certified-SysOps-Administrator-Associate_Exam-Guide.pdf)
- [AWS Certified SysOps Administrator - Associate Exam – Free Exam Q&As, Page 1 | ExamTopics](https://www.examtopics.com/exams/amazon/aws-certified-sysops-administrator-associate/view/)