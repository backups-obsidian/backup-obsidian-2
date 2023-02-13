---
created: 2022-05-16 12:39
updated: 2023-02-12 18:38
---
---
**Links**: [[102 AWS DVA Index]]

---
- **Version control**.
## Why AWS CodeCommit?
- **Private Git repositories**
- *No size limit on repositories* (scale seamlessly)
- *Fully managed*, highly available
- Code only in AWS Cloud account => *increased security and compliance*
- Security (encrypted, access control, etc)
- Integrated with Jenkins, AWS CodeBuild, and other CI tools
- You can setup **SNS/Lambda** to do a *code analysis after each commit*.

> [!question]- Your organisation has developers that merge code changes regularly to an AWS CodeCommit repository. Your pipeline has AWS CodeCommit as the source and you would like to configure a rule that reacts to changes in CodeCommit.
> Use CloudWatch Event rules 

## Security
- **Authentication**: **3 ways** (2 HTTPS, 1 SSH)
	- **Git credentials**: These are *IAM-generated user name and password pair* you can use to communicate with CodeCommit repositories over **HTTPS**.
	- **SSH Keys**: Are locally generated public-private key pair that you can associate with your IAM user to communicate with CodeCommit repositories over SSH.
	- **AWS access keys**: You can use these keys with the credential helper included with the AWS CLI to communicate with CodeCommit repositories over **HTTPS**.
 
- **Authorization**:
	- *IAM policies* to manage users/roles permissions to repositories
- **Encryption**:
	- Repositories are automatically *encrypted at rest using AWS KMS*
	- *Encrypted in transit* (can only use HTTPS or SSH both secure)
- **Cross-account Access**:
	- *Do NOT share your SSH keys* or your AWS credentials
	- Use an lAM Role in your AWS account and use *AWS STS (AssumeRole API)*