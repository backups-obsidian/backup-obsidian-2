---
created: 2022-04-19 16:22
updated: 2023-03-11 09:55
---
---
**Links**: [[101 AWS SAA Index]]

---
## VPC Flow Logs
- Capture information about **IP traffic** in the VPC.
- Used for [[GuardDuty]] for security.
- They can be at the **VPC level** or the **subnet level** or at the **ENI level**. Go for interface level for more security
- They are very helpful in *troubleshooting connectivity issues*.
- The logs can be sent to **two destinations**: **S3** and **CloudWatch Logs**.
- They also **capture information for AWS managed interfaces** like ALB, ElastiCache, Redshift, NATGW etc
- The logs have a specific syntax and it is best to *analyse it using Athena*.
- To *troubleshoot issues* with you VPC look at the **`action`** (ACCEPT/REJECT) field.
- Architecture to alert when we have *too many SSH access* in the VPC:
	- ![[attachments/Pasted image 20230308091820.png]]

> [!note]- After you've created a flow log, you *CANNOT change its configuration or the flow log record format*. 
> For example, you *CANT associate a different IAM role with the flow log or add or remove fields in the flow log record*. 
> Instead, you can **delete the flow log** and **create a new one** with the required configuration.