---
created: 2022-04-19 16:22
updated: 2023-03-14 09:09
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
- ALIAS points a hostname to **an AWS resource**.
	- It *CANNOT be used for on premise resources*.
- You can **use apex domain names with ALIAS**.
-   It works for **both** **root** and **non root** domain. Eg: `mydomain.com` **would work** with ALIAS.

> [!note]- Route53 **DOESNT charge for alias queries**, they *cost effective over CNAME*.
> They are **free of charge**

- They have a **native health check capability**.
- It is an **extension of DNS functionality**.
- Alias records are **always** of type **A** or **AAAA**.
	- ![[attachments/Pasted image 20230307095008.png]]
- You **CANNOT set** the **TTL** it is set **automatically by Route53**

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

> [!caution] You CANNOT set ALIAS record for *EC2 DNS* name

> [!note]- CNAME over ALIAS for **3rd party** applications.
>  So, if you register the DNS name `covid19survey.com`, the zone apex is `covid19survey.com`. You *can't create a CNAME record* for `covid19survey.com`, but you can create an **alias record** for `covid19survey.com` that routes traffic to `www.covid19survey.com`.
> 
> An alias record *can only redirect queries to selected AWS resources* such as S3 buckets, CloudFront distributions, and another record in the same Route 53 hosted zone; however a *CNAME record can redirect DNS queries to any DNS record*. So, you can **create a CNAME** record that redirects queries from `app.covid19survey.com` to `app.covid19survey.net`.

> [!question]- A SysOps Administrator has been tasked with setting up a record set in Amazon Route 53 to point to an Application Load Balancer (ALB). The hosted zone and the ALB are in different accounts. What is the *MOST cost-effective and efficient solution to this requirement*?
> Create an **alias record** in the hosted zone pointing to the Application Load Balancer.
> ---
> - It is *possible to create an Alias record that points to a resource in another account*.
> - *CNAMEs records do incur costs* and this is a less efficient solution as there is more complexity.