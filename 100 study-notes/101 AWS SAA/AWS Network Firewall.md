---
created: 2023-03-09 08:48
updated: 2023-03-09 08:53
---
---
**Links**: [[101 AWS SAA Index]]

---
- To protect network on AWS, we've seen
	- Network Access Control Lists (*NACLs*)
	- Amazon VPC security groups (*SGs*)
	- AWS *WAF* (protect against malicious requests)
	- AWS *Shield* & AWS Shield Advanced
	- AWS *Firewall Manager* (to *manage them across accounts*)

But what if we want to protect in a sophisticated way our entire VPC?
## Network Firewall
- **Protects our entire VPC**.
	- Added at the VPC level.
- From **Layer 3 to Layer 7 protection**.
- We can inspect any traffic in any direction.
	- VPC to VPC traffic
	- Outbound to internet
	- Inbound from internet
	- To/from Direct Connect & Site-to-Site VPN
- Internally, the **AWS Network Firewall uses the AWS Gateway Load Balancer**.
	- Everything is managed by AWS.
- Rules can be *centrally managed cross-account by AWS Firewall Manager* to apply to many VPCs.

### Fine Grained Controls
- Supports 1000s of rules
	- *IP & port* - example: 10,000s of IPs filtering
	- *Protocol* - example: block the SMB protocol for outbound communications
	- *Stateful domain list rule groups*: only allow outbound traffic to \*.mycorp.com or third-party software repo
	- General pattern matching using regex
- Traffic filtering: **Allow**, **drop**, or **alert** *for the traffic that matches the rules*.
- Active flow inspection to protect against network threats with intrusion-prevention capabilities (like Gateway Load Balancer, but all managed by AlVS)
- Send *logs* of rule matches to Amazon *S3*, *CloudWatch Logs*, *Kinesis Data Firehose*.