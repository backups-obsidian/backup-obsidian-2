---
created: 2022-04-29 09:03
updated: 2022-06-01 15:01
---
---
**Links**: [[101 AWS SAA Index]]
**Recommended Reads**: [[../102 AWS DVA/SSM Parameter Store vs Secrets Manager|SSM Parameter Store vs Secrets Manager]]

---

- SSM stands for **Systems Manager**
- **Secure storage for configuration and secrets**.
- Optional encryption using KMS
- **Serverless**, scalable, durable with easy to use SDK.
- **Version tracking** for configuration and secrets.
- **Integration with CloudFormation**
- Configuration management *path* & *IAM*
	- ![[attachments/Pasted image 20220429090459.png]]
	- ![[attachments/Pasted image 20220601123313.png]]

- *Notifications using CloudWatch events*. In [[Config]] we had notifications using either CloudWatch events or SNS.
- We *can inject sensitive data into ECS* using SSM parameter store and secrets manager.
- *Standard parameters tier is free* whereas *advanced parameter tier* is not.
- *Parameter policies* (only for advanced parameters)
	- Allow to assign a TTL to a parameter (expiration date) to force updating or deleting sensitive data such as passwords
	- Can assign multiple policies at a time
		- ![[attachments/Pasted image 20220601122959.png]]

- If preparing for any interviews or want some hands on, watch this
	- https://www.udemy.com/course/aws-certified-developer-associate-dva-c01/learn/lecture/11851458#overview