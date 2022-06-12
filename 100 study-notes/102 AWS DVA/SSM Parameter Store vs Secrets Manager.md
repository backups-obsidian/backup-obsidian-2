---
created: 2022-05-30 09:35
updated: 2022-06-01 15:06
---
---
**Links**: [[102 AWS DVA Index]]

---
## Difference
- Secrets Manager:
	- *More Expensive* 
	- *Automatic rotation* of secrets with AWS Lambda
		- Lambda function is provided for RDS, Redshift, DocumentDB
	- **KMS encryption is mandatory**

- SSM Parameter Store:
	- *Less expensive*
	- Simple API
	- No secret rotation (*can enable rotation using Lambda triggered by CW Events*)
	- **KMS encryption is optional**
	- *Can pull a Secrets Manager secret using the SSM Parameter Store API*

- Both of these services can integrate with CloudFormation
- Secret rotation
	- ![[attachments/Pasted image 20220601150445.png]]