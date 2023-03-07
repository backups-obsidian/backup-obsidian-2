---
created: 2023-02-09 11:22
updated: 2023-03-07 09:03
---
---
**Links**: [[102 AWS DVA Index]]

---
## Security Miscellaneous Services
- **AWS Trusted Advisor**: AWS best practices in terms of cost and infra.
- **IAM Access Analyzer**: AWS IAM Access Analyzer helps us **identify the resources** in your organisation and accounts, such as *Amazon S3 buckets*, IAM roles, KMS keys etc that are **shared with an external entity**.
	- We define a **zone of trust** (AWS Account or Organisation)
	- Anything *outside the zone of trust* will be reported as *findings*.
		- ![[attachments/Pasted image 20230307081852.png]]
	- This lets you identify unintended access to your resources and data, which is a security risk.
- **IAM Access Advisor**: *User Level*
	- Access advisor shows the service permissions granted to a user and when those services were last accessed.
	- You can use this information to *revise your policies*.
- **IAM Credentials Report**: *Account Level*
	- A report that lists all your account's users and the *status of their various credentials*.
	- *Tell us that MFA has not been enabled by an user*.
