---
created: 2022-04-19 16:22
updated: 2023-03-16 08:49
---
---
**Links**: [[101 AWS SAA Index]]

---
## VPC Peering 
- **Privately connect two VPC** using AWS network.
- Make them behave as if they were in the same network.
- The VPCs can be in **different region** or **accounts**.
- The **CIDRs must NOT overlap**.
- VPC peering is **not transitive**. This means VPC peering must be enabled for each VPC that needs to communicate with each other. 
	- So if there are 3 VPCs A, B and C. Peering is enabled between A B and B C. But since peering is not transitive A C are not peered.

- We must **update the route tables in each VPCs subnets** to ensure EC2 instances can communicate with each other. This is **very important**.
   
	- Lets suppose the CIDR of *VPC 1 is 10.0.0.0/16* and the CIDR of *VPC 2 is 172.31.0.0/16*.
    - In the route table of VPC1 we need to add this rule → send any traffic for 172.31.0.0/16 to the peering connection
		- ![[attachments/Pasted image 20220430100506.png]]
    - In the route table of VPC2 we need to add this rule → send any traffic for 10.0.0.0/16 to the peering connection
		- ![[attachments/Pasted image 20220430100535.png]]

- We **can reference a security group** in a **peered VPC**.
	- The VPCs must be in the same region otherwise we can't reference security groups in peered VPCs.

> [!caution]+ Note that a VPC peering connection does not support edge to edge routing. This means that if either VPC in a peering relationship has one of the following connections, you **cannot extend the peering relationship** to that connection:
> - A **VPN connection** or an **AWS Direct Connect** connection to a corporate network
> - *An Internet connection* through an **Internet gateway**
> - An Internet connection in a private subnet through a *NAT device*
> - A **gateway VPC endpoint** to an AWS service; for example, an endpoint to Amazon S3.

## VPC Endpoints
- These are **private endpoints within VPC** that allow us to **initiate a private connection to the AWS services**. For example an instance in the private subnet can connect to S3 without going through the public internet.
	- ![[attachments/Pasted image 20220430111219.png]]

- VPC Endpoints are *powered by* **AWS PrivateLink**. So if the question doesn't mention Endpoints then you can go with PrivateLink.
- They are redundant and scale horizontally.

### Why use VPC Endpoint
- Let's take the example of connecting to AWS SNS with and without VPC Endpoint.
	- *Without* VPC Endpoint: Option 1
	    - Traffic goes from the *private subnet to the NAT Gateway to the internet gateway* and then to SNS over the public internet. This is possible since *every AWS service has a public URL*
	    - This *costs us a lot of money* since the **NAT Gateway has an hourly cost** and everything goes through the *public internet and we don’t get the best network performance*.
	
	- *With* VPC Endpoint: Option 2
	    - All the traffic stays private within the AWS network.

	- ![[attachments/Pasted image 20220430111611.png]]
    
- They **remove the need of NATGW and IGW** to access AWS services. This means you *only need access to internet in the private subnet if you want to update the instances* or if you are talking to services which are not inside AWS.

> [!important] [[Site to Site VPN]] and [[Direct Connect]] *cannot access VPC endpoints*. VPC endpoints are just to access AWS services privately within your VPC.

### Types of Endpoints
> [!note]- Differences between interface and gateway endpoint.
> - *Interface endpoint uses SG* for security where as *gateway endpoint uses endpoint policies*.
> - You need to modify route tables for gateway endpoint, not needed for interface endpoint.

#### Interface Endpoint
- It is **powered by AWS PrivateLink**.
- It **provisions an ENI** (*private IP address*) as an entry point. 
	- ![[attachments/Pasted image 20220430112420.png]]

- We **must attach a security group to the ENI**.
- Supports most services
- You pay an *hourly rate* for the provisioned end points.

#### Gateway Endpoint
- **Provisions a gateway** that **must be used as a target in a route table**.
	- A Gateway Endpoint is a gateway that you specify as a target for a route in your route table for traffic destined to a supported AWS service.
	- ![[attachments/Pasted image 20230308091316.png]]
- There are *no SGs or ENIs with gateway endpoint*.

- It **only** supports **2** services **S3** and **DynamoDB**.
	- You can use two types of VPC endpoints to access Amazon S3: gateway endpoints and interface endpoints
	- You can have both the endpoints independently in a VPC.

> [!important]+ **Endpoint policies** control the security in case of gateway endpoint.
> - When you create a VPC endpoint, you can *attach an endpoint policy* that controls access to the service to which you are connecting.  
> - An endpoint policy *does not override or replace IAM user policies or service-specific policies* (such as S3 bucket policies). 
> - It is a separate policy for *controlling access from the endpoint to the specified service*.

> [!note]- Gateway endpoint is *more cost efficient* than interface endpoint.
> It is free.

> [!question]- When is Interface endpoint preferred over Gateway endpoint?
> Interface Endpoint is preferred when *access is required from on-premises* (*Site to Site VPN or Direct Connect*), a *different VPC* or a *different region*.