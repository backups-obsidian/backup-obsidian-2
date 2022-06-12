---
created: 2022-04-19 16:22
updated: 2022-05-01 11:57
---
---
**Links**: [[101 AWS SAA Index]]

---
## Introduction
- It is a **global service**.
- How DNS works
	- ![[attachments/Pasted image 20220422190045.png]]

- Route 53 is **fully managed**, **highly reliable**, **authoritative** (the customer can update DNS records) **DNS**.
- Route53 is also a *domain registrar* like GoDaddy.

> [!important] This is the only service in AWS which provides *100% availability SLA*.

> [!info] It is called route 53 because it is reference to the *traditional DNS port 53*.

- In Route53 we define *records*. Each records contains the following
    -   **Domain/subdomain** name
    -   **Record types**
			- A : IPv4
			- AAAA: IPv6
			- CNAME: *maps one hostname to another*.
			- NS: Name servers for the hosted zone. They *control how traffic is routed to the domain*.
    -   **IP Value**
    -   [[Route53 Routing Policies]]
    -   **TTL** - amount of time record should be cached in DNS resolvers.

> [!caution]- We can't create a CNAME record for the top node of a DNS namespace (**Zone Apex**)
> Example: you can't create for `example.com`, but you can create for `www.example.com`

## Hosted Zones
-   Hosted zones is a *container for records* which defines how to route traffic to a domain and its sub domains.
	- ![[attachments/Pasted image 20220422191439.png]]
-   We have **two** kinds of hosted zones **public** and **private**.
-   If we buy a *public domain* name then we use the *public hosted zone*.
-   **Private hosted zones** are for domain names that are not publicly available but are private. It can be only resolved by us in our **VPC**.
-   For **any hosted zone** we are going to **pay 0.5$**
- Public and private hosted zones work in the exact same way, the only difference is that public hosted zones allow anyone from the internet to query your records where as in private hosted zone the records can be queried only from the VPC.

## Inbound and Outbound Endpoints
- Amazon Route 53 effectively *connects user requests to infrastructure running in AWS* – such as Amazon EC2 instances  
- It can also be used to **route users to infrastructure outside of AWS**. 
- By **default**, Route 53 Resolver automatically *answers DNS queries for local VPC domain* names for EC2 instances. 
- You can **integrate** DNS resolution between **Route 53 Resolver and DNS resolvers on your on-premises** network by *configuring forwarding rules*.
- To resolve any *DNS queries for resources in the AWS VPC from the on-premises network*, you can create an **inbound endpoint** on Route 53 Resolver. DNS resolvers on the on-premises network can forward DNS queries to Route 53 Resolver via this endpoint.
	- ![[attachments/Pasted image 20220501115457.png]]

- To resolve *DNS queries for any resources in the on-premises network from the AWS VPC*, you can create an **outbound endpoint** on Route 53 Resolver. Route 53 Resolver can conditionally *forward queries to resolvers on the on-premises network via this endpoint*. 
	- ![[attachments/Pasted image 20220501115514.png]]

> [!note] To remember: **in** means queries **from on premises**.

## TTL
- Stands for Time To Live
- *The client will cache the result for the duration of TTL*. This means that in the duration of TTL the client **will not make a DNS query** for that particular domain.
	- ![[attachments/Pasted image 20220422192231.png]]

- The idea behind TTL is that *we don’t want to query the records too often* since we don’t expect records to change a lot.
	- **High TTL**: 24 hours
		- less traffic on Route 53 
		- *outdated records*.
	- **Low TTL**:  60 sec.
		- More traffic on Route 53 which means you will be *paying more*
		- Records are outdated for less time
		- Easy to change records
		
-  TTL will determine the amount you spend for Route 53 and how frequently the records should be updated.

> [!important] TTL is mandatory for every record *expect the alias* records.

## ALIAS
- ALIAS points a hostname to **an AWS resource**
- You can **use apex domain names with ALIAS**.
-   It works for **both** **root** and **non root** domain. Eg: `mydomain.com` **would work** with ALIAS.
-  They are **free of charge**
- They have a **native health check capability**.
- It is an **extension of DNS functionality**.
- Alias records are **always** of type **A** or **AAAA**.
- You **cannot set** the **TTL** it is set **automatically by Route53**

> [!tip] For AWS resources always go for ALIAS over CNAME

- ALIAS **targets**
	- Elastic Load Balancers
	- CloudFront Distributions
	- API Gateway
	- Elastic Beanstalk environments
	- S3 Websites
	- VPC Interface Endpoints
	- Global Accelerator accelerator
	- Route 53 record in the same hosted zone

> [!caution] You cannot set ALIAS record for *EC2 DNS* name

> [!note]- CNAME over ALIAS
>  So, if you register the DNS name `covid19survey.com`, the zone apex is `covid19survey.com`. You *can't create a CNAME record* for `covid19survey.com`, but you can create an **alias record** for `covid19survey.com` that routes traffic to `www.covid19survey.com`.
> 
> An alias record *can only redirect queries to selected AWS resources* such as S3 buckets, CloudFront distributions, and another record in the same Route 53 hosted zone; however a *CNAME record can redirect DNS queries to any DNS record*. So, you can **create a CNAME** record that redirects queries from `app.covid19survey.com` to `app.covid19survey.net`.