---
created: 2022-04-30 19:10
updated: 2022-05-06 20:10
---
---
**Links**: [[101 AWS SAA Index]]

---
## VPG & CGW
- VPN Connections can be *configured in minutes* and are a good solution if 
	- You have an immediate need 
	- Have low to modest bandwidth requirements 
	- Can tolerate the inherent variability in Internet-based connectivity.

- We might have a corporate **on premise data centre** and **we would want to connect** it with our **VPC** in cloud.
- For this we have a **Customer Gateway** in the corporate **data centre** and a **VPN Gateway** in the **VPC**. Using these we would establish a Site to Site VPN through the public internet. These are the *2 major components* required for establishing a site to site VPN.

- **Virtual Private Gateway** (VPG):
    - VPN concentrator on the **AWS side** of the VPN connection
    - VPG is **created and attached to the VPC** from which you want to create the Site-to-Site VPN connection

- **Customer Gateway** (CGW):
    - **customer side** of the VPN connection
    - **Software application** or **physical device** 

> [!important]+ Another use case of VPG
> A VPG is used to setup an AWS VPN connection but you *can also use it in combination with Direct Connect to encrypt all data that traverses the Direct Connect link*. This combination provides an IPsec-encrypted private connection that also reduces network costs, *increases bandwidth throughput*, and provides a *more consistent network experience than internet-based VPN connections*.
> **DX + VPG**

^a5f822

### Customer Gateway Device
- IP of the Customer Gateway.
	- **Public Internet-routable IP address** for your Customer Gateway device
	- A **NAT device** that's enabled for *NAT traversal (NAT-T)*, use the *public IP address of the NAT device*.
	- ![[attachments/Pasted image 20220430192051.png]]
    
> [!question]- What needs to be configured *outside of the VPC* for them to have a successful site-to site VPN connection?
> An **Internet-routable (public) IP address (static)** of the customer gateway's external interface for the on-premises network.
> 
> So the IP address must be *public and static*.

- For this to work **Route Propagation must be enabled** for Virtual Private Gateway in the route table that is associated with subnets.
- If you need to **ping your EC2 instances** from on-premises, make sure you add the **ICMP protocol on the inbound** of your security groups

> [!note] For VPN connections the *only single point of failure will be the customer gateway device* in the on premise architecture.

## VPN CloudHub
- Provide *secure communication between multiple sites*, if you have multiple VPN connections.
-   **Low-cost hub-and-spoke model** 
- For *primary or secondary network connectivity between different locations (VPN only)*
- Because of this the **customer networks can now connect to one another**.
	- ![[attachments/Pasted image 20220430193020.png]]
	- **Only one VPG**.

- Since it is a VPN connection **all the traffic goes over the public internet**.
- To set it up, connect **multiple VPN connections on the same VPG**, setup dynamic routing and configure route tables. 

> [!note]- Use case question
> Your company has *several on-premises sites* across the USA. These sites are currently linked using private connections, but your private connections provider has been recently quite unstable, making your IT architecture partially offline. You *would like to create a backup connection that will use the public Internet to link your on-premises sites*, that you can **failover** in case of issues with your provider.

> [!caution]+ We can also use DX with CloudHub
> - Suppose we have a DX connection to the corporate HQ and multiple site to site VPNs to other locations then with a CloudHub HQ and the sites can communicate with each other along with communicating to the AWS VPC. The *only catch* is that the *DX connection must use the same VPG* (for securing connection) that is being used by the other sites for their site to site VPN.
> -  VPN CloudHub enables your remote sites to communicate with each other, and not just with the VPC. Sites that use **AWS Direct Connect connections to the virtual private gateway** can *also be part of the AWS VPN CloudHub*.
