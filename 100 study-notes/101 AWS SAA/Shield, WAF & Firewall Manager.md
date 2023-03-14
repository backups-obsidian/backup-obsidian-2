---
created: 2022-04-29 10:03
updated: 2023-03-13 08:34
---
---
**Links**: [[101 AWS SAA Index]]

---
## Shield
- To **protect from DDoS attacks**.
- *AWS Shield Standard*:
	- *Free service* that is *activated for every AWS customer*.
	- Provides protection from attacks such as *SYN/UDP Floods*, Reflection attacks and other layer 3/layer 4 attacks.
- *AWS Shield Advanced*:
	- Optional DDoS mitigation service (*$3,000 per month per organisation*)
	- *Protect against more sophisticated attack* on Amazon EC2, Elastic Load Balancing (ELB), Amazon CloudFront, AWS Global Accelerator, and Route 53
	- *24/7 access to AWS DDoS response team* (DRP)
	- Protect against higher fees during usage spikes due to DDOS. *Reimbursement*.

- Enable *consolidated billing for organisation*.
- Shield can be used with:
	- **ALB**
	- **Route53**
	- Elastic IP
	- **CloudFront**
 
## WAF
- Protects our *web applications* from common **web exploits (layer 7 - HTTP)**
- It can be deployed on **3** things: 
	- **ALB** 
	- **API Gateway**  
	- **CloudFront**

- To use WAF we **must define WACL** (web access control lists).
- In WACL there can be **rules on different things**. 
	- There can be rules on **IP addresses**, HTTP Headers, HTTP body or URI strings
		- We can **string match** statements to match things like IP.
	- We can *block countries* using the **geo-match** feature or allow/block only certain IPs. Allowing certain IPs is done using WAF IP **set** statement.
	- We can also apply some **Rate Based Rules for DDoS protection**. For example an IP should not do more than 5 requests per second.

- It can **protect from basic attacks** such as **SQL injection** and **XSS**.

> [!caution] For DDoS you can either go for [[Shield, WAF & Firewall Manager#Shield | Shield]] or *Rate Based Rules of WAF*.

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

## Architecture for DDoS protection
![[attachments/Pasted image 20230306152723.png]]

## Firewall Manager
- Manage **rules of all accounts of an AWS organisation**. 
	- Like *centrally manage EC2 Security Groups* and AWS Shield Advanced across all AWS accounts in your AWS Organisation.

- What can we **manage** using firewall:
	- Common set of security rules
	- *WAF rules* (Application Load Balancer, API Gateways, CloudFront)
	- *AWS Shield Advanced* (ALB, CLB, Elastic IP, CloudFront)
	- *Security Groups* for EC2 and *ENI* resources in VPC

> [!note] **No NACLs** configuration.