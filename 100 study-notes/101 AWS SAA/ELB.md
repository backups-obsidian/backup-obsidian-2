---
created: 2022-04-20 15:02
updated: 2023-03-02 19:52
---
---
**Links**: [[101 AWS SAA Index]]

---
## Introduction
- **Managed** load balancer by AWS.
- **Serve multiple AZs in the same region**. For multi region load balancing we need [[Route53]].
- ELBs handle failures of EC2 instances **by NOT sending them traffic** and **provides regular health check of EC2 instances**.
	- The health check is done *on a port and a route*. For example:
		- ![[attachments/Pasted image 20220420150839.png]]
	- If the response is *not 200* then the *instance is unhealthy* and *no traffic will be sent to it*.
	
- ELBs provide **SSL termination**.
- Separates public traffic from private traffic.
- Load balancer security group will receive traffic from anywhere. This security group will be used as an inbound rule by the EC2 instances. [[EC2 Basics#^52df0e|Security group referencing]].
	- ![[attachments/Pasted image 20220420151342.png]] 
	- We use port 80 for EC2 instances since TLS termination happens at load balancer.

## SSL/TLS
- Traffic is encrypted between the client and the load balancer.
- **TLS** is the **newer** version of SSL. To remember T > S. 
- The load balancer performs SSL termination. We *use HTTP inside our private VPC*.
- The load balancer **uses** **X.509** certificate.
- We **manage** the **certificates** using **AWS Certificate Manager** (ACM).

## SNI
- **Server name indication**.
- It solves the critical problem of **loading multiple SSL certs onto one web server**.
	- ![[attachments/Pasted image 20220420161151.png]]
- Supported by **ALB**, **NLB** and **CloudFront**. 
	- Not supported by CLB. For SNI in CLB we have to use CloudFront distribution.
- In order to use SNI, all you need to do is *bind multiple certificates to the same secure listener on your load balancer*. 
	- ALB will *automatically choose the optimal TLS certificate* for each client.

> [!note] *Wildcard certificates* can *only handle sub domains* and NOT domains.

## Health Checks
- Target Health Statuses
	- *Initial*: registering the target
	- Healthy
	- Unhealthy
	- *Unused*: *target is not registered*
	- Draining: de-registering the target
	- **Unavailable**: **health checks disabled**

> [!note]- If a *target group* contains *only unhealthy targets*, ELB routes requests **across its unhealthy targets**.
> This is done assuming the health check has been wrongly setup.