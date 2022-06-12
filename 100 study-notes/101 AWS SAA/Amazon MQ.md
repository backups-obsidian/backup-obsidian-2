---
created: 2022-04-25 16:26
updated: 2022-04-25 16:26
---
---
**Links**: [[101 AWS SAA Index]]
**Keywords**: *migrating*, *other protocols*

---
- **SNS, SQS** are **cloud native services** and use **proprietary protocols** from AWS.
- There can be traditional applications running on premise and using different protocols.
- So when migrating the application from on premise to cloud instead of reengineering everything we can use Amazon MQ.
- **Amazon MQ = managed Apache Active MQ.**
- It doesn’t scale as well as SNS or SQS.
- For **fail over** we need AWS **EFS storage**
	- ![[attachments/Pasted image 20220425162549.png]]