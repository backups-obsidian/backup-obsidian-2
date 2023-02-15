---
created: 2022-05-30 09:35
updated: 2023-02-14 19:37
---
---
**Links**: [[102 AWS DVA Index]]

---
> [!tip]- To remember: Manager rotates.

## Difference
### Secrets Manager
- *More Expensive* 
- **Automatic rotation** of secrets with AWS Lambda
	- Lambda function is provided for **RDS**, **Redshift**, **DocumentDB** (built in integration)
- **KMS encryption is mandatory**

> [!tip]+ Keywords
> - Databases
> - Automatic Rotation

### SSM Parameter Store:
- *Less expensive*
- Simple API
- **NO automatic secret rotation** 
	- We *can enable rotation using Lambda triggered by CW Events*
- **KMS encryption is optional** but secrets can be encrypted using KMS.
- Encrypted secrets using **SecureString**.
- *Can pull a Secrets Manager secret using the SSM Parameter Store API*

- Both of these services can integrate with CloudFormation
- Secret rotation
	- ![[attachments/Pasted image 20220601150445.png]]