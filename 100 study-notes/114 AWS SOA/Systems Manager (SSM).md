---
created: 2023-03-02 13:07
updated: 2023-03-02 14:36
---
---
**Links**: [[114 AWS SOA Index]]

---
## Systems Manager
- Helps us manage a *fleet* of **EC2 instances** and **on-premise instances**.
- **Patching automation for enhanced compliance**.
- Easily detect problems
- Works for both Windows and Linux OS
- Integrated with CloudWatch metrics / dashboards
- *Integrated with AWS Config*
- *Free service*
- We need to **install the SSM agent** on the systems we want to control.
	- Installed *by default on Amazon Linux 2 AMI* & some Ubuntu AMI
- If there is an *issue* with the systems manager then make sure
	- The EC2 instances have a *proper IAM role* (`AmazonSSMManagedInstanceCore`) to allow SSM actions
	- SSM agent is running

## Resource Groups
- Using tags to create resource groups.
	- Create, view or *manage logical group of resources* thanks to tags
- Example: Production versus development environments
	- ![[attachments/Pasted image 20230302143324.png]]
- *Regional service*
- **Works with EC2, S3, DynamoDB, Lambda, etc..**

> [!note] We create resource groups so that we can *operate on the group level* while using SSM.