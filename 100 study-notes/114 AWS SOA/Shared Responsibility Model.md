---
created: 2023-03-06 14:53
updated: 2023-03-06 14:53
---
---
**Links**: [[114 AWS SOA Index]]

---
## Shared Responsibility Model
- *AWS responsibility* - *Security **of** the Cloud*
	- Protecting infrastructure (hardware, software, facilities, and networking) that runs all the AWS services.
	- Managed services like S3, DynamoDB, RDS, etc.
- *Customer responsibility* - *Security **in** the Cloud*
	- For *EC2 instance*, customer is responsible for management of the **guest OS** (including security patches and updates), **firewall & network configuration**, **lAM**.
	- **Encrypting application data**
- Shared controls:
	- Patch Management (for RDS it is AWS, for EC2 its us), Configuration Management, Awareness & Training

![[attachments/Pasted image 20230306144716.png]]

### RDS Example
- AWS responsibility:
	- Manage the underlying EC2 instance, disable SSH access
	- *Automated DB patching*
	- *Automated OS patching*
	- Audit the underlying instance and disks & guarantee it functions
- Your responsibility:
	- *Check the ports/IP/security group inbound rules in DB's SG*
	- In-database user creation and permissions
	- Creating a database with or without public access
	- **Ensure parameter groups or DB is configured to only allow SSL connections**.
	- *Database encryption setting*

### S3 Example
- AWS responsibility:
	- Guarantee you get unlimited storage
	- *Guarantee you get encryption*
	- Ensure separation of the data between different customers
	- Ensure AWS employees can't access your data
- Your responsibility:
	- Bucket configuration
	- **Bucket policy/public setting**
	- IAM user and roles
	- *Enabling encryption*