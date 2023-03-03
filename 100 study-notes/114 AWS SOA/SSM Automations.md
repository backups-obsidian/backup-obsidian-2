---
created: 2023-03-02 14:53
updated: 2023-03-02 15:00
---
---
**Links**: [[114 AWS SOA Index]]

---
## SSM Automations
- Simplifies common *maintenance* and deployment tasks of EC2 instances and other AWS resources.
	- Example: restart instances, create an AMI, EBS snapshot
- **Automation Runbook**
	- **SSM Documents of type Automation**
	- Defines actions preformed on your *EC2 instances or AWS resources*
	- *Pre-defined runbooks* (AWS) or create custom runbooks
- Can be *triggered*
	- Manually using AWS Console, AWS CLI or SDK
	- By *Amazon EventBridge*
	- On a *schedule* using Maintenance Windows
	- By **AWS Config for rules remediations**
- Automations are from outside the EC2 instances whereas run command was from inside the EC2 instances.
- An example use case would be *restarting a whole fleet of EC2 instances* without SSH access and without coding (by using AWS defined runbooks).