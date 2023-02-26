---
created: 2022-04-17 15:43
updated: 2023-02-25 15:53
---
---
**Links**: [[../100 home | 100 Home]]
**Recommended Reads**: [[../101 AWS SAA/101 AWS SAA Index | AWS SAA Index]]

---
## IAM
[[../101 AWS SAA/IAM Basics | IAM Basics]]
[[../101 AWS SAA/Miscellaneous | IAM Miscellaneous]]

## EC2
[[../101 AWS SAA/EC2 Basics | EC2 Basics]]
[[../101 AWS SAA/EC2 Instance Purchasing Options | EC2 Instance Purchasing Options]]
[[../101 AWS SAA/EC2 EBS | EC2 EBS]] - IOPS calculation is important
[[../101 AWS SAA/EC2 AMI | EC2 AMI]]
[[../101 AWS SAA/Instance store | Instance Store]]
[[../101 AWS SAA/EFS | EFS]]

## ASG
[[../101 AWS SAA/ASG | ASG]]
[[../101 AWS SAA/Scaling Policies | ASG Scaling Policies]]
[[../101 AWS SAA/Launch Configuration-Template | Launch Configuration vs Launch Template]]

## ELB
[[../101 AWS SAA/ELB | ELB]]
[[../101 AWS SAA/Types of ELBs | Types of ELBs]]
[[../101 AWS SAA/Stickiness, Cross zone balancing & Connection Draining | Stickiness, Cross zone balancing and Connection Draining]]

## Databases
[[../101 AWS SAA/RDS | RDS]]
[[../101 AWS SAA/RDS Read Replica & Multi AZ | RDS Read Replica and Multi AZ]]
[[../101 AWS SAA/RDS Security | RDS Security]]
[[../101 AWS SAA/Aurora | Aurora]]
[[../101 AWS SAA/ElastiCache | ElastiCache]]
[[ElastiCache DVA]]
[[ElasticCache Replication]]

## Route53
[[../101 AWS SAA/Route53 | Route53]]
[[../101 AWS SAA/Route53 Routing Policies | Route53 Routing Policies]]
[[../101 AWS SAA/Route53 Health Checks | Route53 Health Checks]]

## VPC
[[../101 AWS SAA/VPC Internet Access | VPC Internet Access]]
[[../101 AWS SAA/NACLs | NACLs]]
[[../101 AWS SAA/VPC Peering and Endpoints | VPC Peering and Endpoints]]
[[../101 AWS SAA/VPC Flow Logs | VPC Flow Logs]]

- Summary:
	- ![[attachments/Pasted image 20230211180311.png]]

## S3
![[../101 AWS SAA/101 AWS SAA Index#S3]]
[[S3 Access Points]]

## CLI and AWS Limits
[[CLI]]
[[AWS Limits]]

## CloudFront
[[../101 AWS SAA/CloudFront | CloudFront]]
[[../101 AWS SAA/CloudFront Advanced | CloudFront Advanced]]
[[CloudFront DVA]]

## ECS
[[../101 AWS SAA/ECS | ECS]]
[[ECS DVA]]

## ElasticBeanstalk
[[../101 AWS SAA/ElasticBeanstalk | ElasticBeanstalk]]
[[ElasticBeanstalk DVA]]
[[ElasticBeanstalk Deployment Modes]]
[[ElasticBeanstalk Docker]]

## CI/CD
- Tech Stack
	- ![[attachments/Pasted image 20220516161339.png]]

[[CodeCommit]]
[[CodePipeline]]
[[CodeBuild]]
[[CodeDeploy]]
[[CodeStar]]
[[CodeArtifact]]
[[CodeGuru]]

## CloudFormation
[[CloudFormation]]
[[CloudFormation Template Components]]
[[CloudFormation Intrinsic Functions]]
[[CloudFormation RollBacks]]
[[CloudFormation Advanced]]

## Monitoring, Troubleshooting & Auditing
- AWS CloudWatch offer us: 
	- *Metrics*: Collect and track key metrics
	- *Logs*: Collect, monitor, analyse and store log files
	- *Events*: Send notifications when certain events happen in your AWS. Recommended to use EventBridge.
	- *Alarms*: React in real-time to metrics/events

[[../101 AWS SAA/CloudWatch | CloudWatch]]
[[../101 AWS SAA/EventBridge | EventBridge]]
[[X-Ray]]
[[X-Ray Advanced]]
[[../101 AWS SAA/CloudTrail | CloudTrail]]

## Messaging: SQS, SNS & Kinesis
[[../101 AWS SAA/SQS | SQS]]
[[SQS DVA]]
[[../101 AWS SAA/SNS | SNS]]
[[../101 AWS SAA/Kinesis | Kinesis]]
[[Kinesis DVA]]

## Lambda
[[../101 AWS SAA/Lambda | Lambda]]

Three ways in which Lambda processes events

- [[Lambda Synchronous Invocation]]
- [[Lambda Asynchronous Invocation]]
- [[Lambda Event Source Mapping]]

[[Lambda Destinations]]
[[Lambda IAM Role]]
[[Lambda Configuration & Deployments]]
[[Lambda Performance]]
[[Lambda Miscellaneous]]

## DynamoDB
[[../101 AWS SAA/DynamoDB|DynamoDB]]
[[DynamoDB Capacity]]
[[DynamoDB Basic APIs]]
[[DynamoDB Indexes]]
[[DynamoDB Locking]]
[[DynamoDB DVA]]

## API Gateway
[[../101 AWS SAA/API Gateway|API Gateway]]
[[API Gateway DVA]]
[[API Gateway Integration Types]]
[[API Gateway Caching]]
[[API Gateway Monitoring]]
[[API Gateway Security]]

## SAM & CDK
[[SAM]]
[[CDK]]

## Cognito & Step Functions 
[[Cognito]]
[[Step Functions]]

## Security
[[STS & IAM Advanced DVA]]
[[../101 AWS SAA/KMS|KMS]]
[[KMS DVA]]
[[KMS Working]]
[[SSM Parameter Store vs Secrets Manager]]
[[MFA]]
[[Security Miscellaneous Services]]

## Other Services
[[Other Services DVA]]
[[../101 AWS SAA/GuardDuty | Guard Duty]]
[[../101 AWS SAA/WAF & Firewall#WAF| WAF]]

## Miscellaneous
- Typical 3 tier architecture
	- ![[attachments/Pasted image 20220514121352.png]]
	- *Data subnet* is also a *private subnet*.

[[DVA - Extra Notes]]
## Exam Tips
- There are **very very few trick questions**
- If a solution seems feasible but *highly complicated, it's probably wrong*
- Whenever there is a negative keyword like **NOT** then the chances of the option being the correct answer is very less.
- Whenever you have questions related to debugging why something is not working then options containing the word *permissions* becomes a potential answer.
- When you see *automatic retries* think about AWS SDK.
- If you need to figure out why some service is *slow (performance issues)* just go with *X-Ray*.
