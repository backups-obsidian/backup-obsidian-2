---
created: 2022-04-29 10:03
updated: 2022-05-02 16:04
---
---
**Links**: [[101 AWS SAA Index]]

---
## WAF
- Protects our *web applications* from common **web exploits (layer 7 - HTTP)**
- It can be deployed on **3** things: 
	- **ALB** 
	- **API Gateway**  
	- **CloudFront**

- To use WAF we *must define WACL* (web access control lists).
- In WACL there can be **rules on different things**. There can be rules on **IP addresses**, HTTP Headers, HTTP body or URI strings
	- We can *block countries* using the **geo-match** feature or allow/block only certain IPs. Allowing certain IPs is done using WAF IP **set** statement.
	- We can also apply some **Rate Based Rules for DDoS protection**. For example an IP should not do more than 5 requests per second.

- It can **protect from basic attacks** such as **SQL injection** and **XSS**

> [!caution] For DDoS you can either go for [[Shield]] or *Rate Based Rules of WAF*.

### Blocking an IP address
- We *cannot block IP addresses using SGs* since there is no deny rule.
- The simplest way of blocking IP address at the subnet level would be using **NACLs deny rules**.
- We can use **WAF with ALB** that gives us dual protection along with NACLs. But it is *more expensive as compared to only NACLs* approach.
	- ![[attachments/Pasted image 20220501184912.png]]
- If we want to block IP addresses at the CloudFront level we have *2 options*:
	- **CloudFront geo restriction** to block IP addresses.
	- **WAF with CloudFront**.
		- The *SG of ALB must allow all the public CloudFront IPs*.
		- *ALB only sees the CloudFront IP* and not the client IP.
		- NACL is not helpful at all since *all the requests are coming from CloudFront’s IP*. *CloudFront sits outside our VPC* so we *cannot use NACL*.
		- ![[attachments/Pasted image 20220501185237.png]]

## Firewall
- Manage *rules of all accounts of an AWS organisation*. 
	- Like *centrally manage EC2 Security Groups* and AWS Shield Advanced across all AWS accounts in your AWS Organisation.

- What can we **manage** using firewall:
	- Common set of security rules
	- *WAF rules* (Application Load Balancer, API Gateways, CloudFront)
	- *AWS Shield Advanced* (ALB, CLB, Elastic IP, CloudFront)
	- *Security Groups* for EC2 and *ENI* resources in VPC

> [!note] **No NACLs** configuration.