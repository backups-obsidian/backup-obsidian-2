---
created: 2023-03-02 18:50
updated: 2023-03-02 19:01
---
---
**Links**: [[114 AWS SOA Index]]

---
## SSM Session Manager
- Allows you to **start a secure shell** on your EC2 and on-premises servers.
- Access through AWS Console, AWS CLI, or Session Manager SDK
- **DOES NOT need SSH access, bastion hosts, or SSH keys**
- Supports Linux, macOS, and Windows
- Log connections to your instances and execute commands
	- *Session log data* can be sent to *S3* or *CloudWatch Logs*
- CloudTrail can intercept **`StartSession`** events to perform some automation.
- *IAM Permissions*
	- Control which users/ groups can *access Session Manager* and which instances
	- Use **tags to restrict access** to only specific EC2 instances
		- ![[attachments/Pasted image 20230302185554.png]]
	- Access SSM + write to 53 + write to CloudWatch for writing the logs to S3 and CloudWatch
- Optionally, we *can restrict commands a user can run* in a session.

### Difference between SSH and Session Manager
- It is *way more controlled* and has a *lot of compliance* around it.

![[attachments/Pasted image 20230302185759.png]]