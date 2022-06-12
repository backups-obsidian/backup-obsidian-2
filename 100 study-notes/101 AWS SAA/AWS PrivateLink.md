---
created: 2022-04-30 21:31
updated: 2022-04-30 21:35
---
---
**Links**: [[101 AWS SAA Index]]

---
## Why PrivateLink
- Suppose you want to share some service in you VPC to the customer VPC. 
	- One option is to use the service over *public internet*. This is not ideal.
	- Another option is to use VPC peering but it *exposes your whole VPC to the customer* which is not ideal.

## PrivateLink
- Most secure & scalable way to **expose service to 1000s of VPC (own or other accounts)**
- Does not require VPC peering, internet gateway, NAT, route tables...
- Requires a **network load balancer (Service VPC)** (your end) and **ENI (Customer VPC) or GWLB**. 
	- ![[attachments/Pasted image 20220430213125.png]]
	- To remember which side needs what remember since the service VPC contains the service there should be some kind of load balancing hence NLB in service VPC.
- This ENI is also known as **PrivateLink endpoint** which is implemented on the *customer side*.
