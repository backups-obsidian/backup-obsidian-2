---
created: 2022-04-19 16:22
updated: 2023-05-31 17:15
---
---
**Links**: [[101 AWS SAA Index]]

---
## ENI
- It stands for Elastic Network Interface.
- It is a **logical component in a VPC** and represents a *virtual network card*.
- ENIs can be created independently of EC2 and then attached to EC2 instances.
- Main use case is for **fail overs** by attaching to different EC2 instances.
- If you create an EC2 instance an ENI will be automatically created for you. If you terminate you EC2 instance then the ENI that was automatically created will be deleted.
- ENIs *don't cost any money*.
- **Attributes of an ENI**:
	- *One primary private IPv4*, one or more secondary IPv4
	- One Elastic IP per private IPv4.
	- One public IPv4.
	- One MAC address.

> [!caution] ENIs are bound to a **specific AZ**.

