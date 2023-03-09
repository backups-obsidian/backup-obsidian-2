---
created: 2022-04-30 20:58
updated: 2023-03-09 08:13
---
---
**Links**: [[101 AWS SAA Index]]

---
## IPv6
- *All IPv6 addresses will be internet-routable* which means there will be *no private IP addresses*.
- **IPv4 cannot be disabled for your VPC and subnets** but IPv6 can.
- We can **enable IPv6** (they're public IP addresses) to operate in **dual-stack mode**
- Your *EC2 instances* will get **at least a private internal IPv4 and a public IPv6**
- So, if you *cannot launch an EC2* instance in your subnet then it's not because it cannot acquire an IPv6 (the space is very large). It's because *there are no available IPv4 in your subnet*. Solution is to *create a new IPv4 CIDR in your subnet*.
- To get IPv6 addresses you *need to enable IPv6 CIDR in VPC*. By default they are disabled.

## Egress Only Internet Gateway
- They are used for **only IPv6 traffic**.
- They are *similar to NAT Gateway* but for IPv6.
	- It allows the instances in the **private subnet** to *initiate outbound connections over IPv6* while *preventing the internet to initiate an inbound IPv6 connection*. Same level as Internet gateway.
		- ![[attachments/Pasted image 20220430210806.png]]
	- Although it works similar to NAT gateway *it is at the VPC level* (similar to internet gateway) whereas NAT Gateway is in the public subnet. It makes sense since *in IPv6 we don't need NAT (network address translation) since we don't have any private IP addresses*.
	- ![[attachments/Pasted image 20220430210645.png]]

- **Route tables of the private subnet must be updated** for this.