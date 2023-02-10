---
created: 2022-04-29 20:22
updated: 2023-02-09 17:05
---
---
**Links**: [[101 AWS SAA Index]]

---
## Internet Gateway
- An internet gateway **allows EC2 instances in a VPC to connect to the internet**.
- It scales horizontally and is **highly available** and redundant.
- It must be **created separately from a VPC**.
-  **One VPC** can only be attached to **one IGW** and vice versa
-   **IGW on their own do not allow internet access** the *route tables must also be edited*.
- When you create an IGW it will be in detached state attach it to the VPC.
- For instances in the *public subnet* with access to internet *NAT is performed by Internet Gateway*.

## Route Tables
- For each VPC we create we get a **default route table**.
- In the beginning we since we haven't created any route tables for subnets all the subnets are **implicitly attached to the default/main route table**.
- So we create our own route tables and then *explicitly associate them with their respective subnets*. I have created two, one for private subnet and another one for public subnet.
- Now to make public subnet available to the internet we go to the public route table and edit its route. We say for *any route other than 10.0.0.0/16 send it to the internet gateway*.
	- ![[attachments/Pasted image 20220430074720.png]]

- A subnet is deemed to be a **Public Subnet** if it has a **Route Table that directs traffic to the Internet Gateway**. Otherwise there is no difference between a public and private subnet.
	- ![[attachments/Pasted image 20220430075135.png]]

- In cases where your **EC2 instance cannot be accessed from the Internet (or vice versa)**, you usually have to check two things:
    - **Does it have an EIP or public IP address**?
    - Is the route table properly configured?

> [!note]- A subnet can only be associated with one route table at a time.

## Bastion Hosts
- If we create an EC2 instance in the private subnet we won’t be able to SSH into it since no internet access it allowed.
- If we give the private subnet access to the internet gateway then it would make the EC2 instances in the private subnet public thereby defeating the purpose of a private subnet.
- To **connect to the instances in the private subnet we use a bastion host** in the public subnet.
	- ![[attachments/Pasted image 20220430075614.png]]

- The **security group** of **private EC2 instances should only allow SSH from the bastion host Security Group**.
-  A bastion host is an **EC2 instance in the public subnet** which is then connected to all the private subnets.
- *Bastion host security group must be tightened*. Only open ssh port that to from a specific IP.
- We can SSH into the private EC2 instance from the bastion host since **traffic can move between private and public subnets**. In the *route tables it is defined 10.0.0.0/16 to local*.
	- ![[attachments/Pasted image 20220430075529.png]]

- If you ssh into the private EC2 instance from the bastion host and then try ping [google.com](http://google.com) it won’t work since the private instances don’t have access to the internet. So **both outbound and inbound is blocked**. 
- For outbound internet access in private subnet we need a NAT gateway.
- These are **small instances** since you don’t have to do a lot of processing.

## NAT Instance
- It is **outdated** and replaced by NAT Gateway **but still in the exam**.
- NAT stands for *Network Address Translation*.
- NAT instances must be **launched in a public subnet**.
- They **allow EC2 instances in the private subnet to connect to the internet**.
- They should have an **elastic IP attached to them**.
- *Route tables* must be *configured* to **route traffic from the private subnets to the NAT instance**.
- We have to *provision and manage an EC2 instance* for NAT Instance.
- We must **disable source and destination check** in EC2.
- We have preconfigured AMIs for NAT Instances.
- NAT instances are **not highly available** and reliant.
- A lot of configuration is needed to make them work.

> [!caution]- Always prefer NAT Gateway over NAT instance. Extra things NAT Instances provide over NAT Gateway
> - NAT instance can be used as a *bastion host*.
> - *Security Groups can be associated* with a NAT Instance. 
> - NAT instance supports port forwarding
> 
> All these things cannot be done with a NAT Gateway.

## NAT Gateway
- They are **AWS managed NAT**, higher availability, higher bandwidth and no administration.
- A NAT Gateway is an AWS service that allows a private subnet to have access to the Internet, but prevents the Internet from initiating a connection directly to the instances.
	- Traffic destined for the Internet will flow from the private subnet to the NAT Gateway in the public subnet, and then out to the Internet through the Internet Gateway.

- We *pay per hour of usage and bandwidth* so it is *not a free service like VPC or IGW*.

- NAT Gateway is **created in a specific AZ** and uses an **Elastic IP.**
- NAT gateway is **resilient only within a single AZ**.
	- You must create *multiple NAT gateways in multiple AZs for fault tolerance*.
	- Single NAT Gateway in an AZ is enough. No need to have multiple NAT Gateways in an AZ.
	- ![[attachments/Pasted image 20220430081404.png]]

> [!question]- How would you make sure that NAT Gateway is not a single point of failure in your architecture?
> Create a *NAT Gateway in each availability zone*. Configure the route table in each private subnet to ensure that instances use the NAT Gateway in the **same availability zone**.

- It **can’t be used by the EC2 instance in the same subnet**.

- For internet access to in private subnet a NAT Gateway **requires an IGW to be present in VPC** also the public subnet must have a route to IGW. 
	- The route is **Private Subnet → NATG (Public Subnet) → IGW**

- There is *no need to manage any security groups*. For NAT Instances we need security Groups.
- 5 Gbps *bandwidth with automatic scaling* upto 45 Gbps.
- It must be in a **public subnet**.
	- ![[attachments/Pasted image 20220430080750.png]]

- After creating the NAT gateway in the public subnet we need to **modify the route tables of the private subnet to use the NAT gateway**.
	- ![[attachments/Pasted image 20220430080937.png]]

## VPC Reachability Analyser
- It **doesn’t send packets** just analyses the configuration.
- It is a network diagnostics tool that troubleshoots network connectivity between two endpoints in your VPC(s)
- It *builds a model* of the network configuration, then checks the reachability based on these configurations (it doesn't send packets) 
- When the destination is reachable it produces hop-by-hop details of the virtual network path  
- When not reachable it *identifies the blocking component(s)* (e.g., configuration issues in SGs, NACLs, Route Tables, ...)
- **Use cases**: *troubleshoot connectivity issues*, ensure network configuration is as intended etc.