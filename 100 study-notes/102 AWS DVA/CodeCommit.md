---
created: 2022-05-16 12:39
updated: 2022-05-17 13:56
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

## Security
- **Authentication**:
	- *SSH Keys*: AWS Users can configure SSH keys in their IAM Console
	- *HTTPS*: with *AWS CLI Credential helper* or Git Credentials for lAM user
- **Authorization**:
	- *IAM policies* to manage users/roles permissions to repositories
- **Encryption**:
	- Repositories are automatically *encrypted at rest using AWS KMS*
	- *Encrypted in transit* (can only use HTTPS or SSH both secure)
- **Cross-account Access**:
	- *Do NOT share your SSH keys* or your AWS credentials
	- Use an lAM Role in your AWS account and use *AWS STS (AssumeRole API)*