---
created: 2022-04-30 19:44
updated: 2022-05-01 21:39
---
---
**Links**: [[101 AWS SAA Index]]

---
## Transit Gateway
- For having **transitive peering** between **thousands of VPCs** and **on-premises**, hub-and-spoke (**star**) **connection**
	- For context in [[Site to Site VPN#VPN CloudHub|VPN CloudHub]] one AWS VPC multiple sites. All the sites can talk to each other.
	- For context in [[Direct Connect#Direct Connect Gateway|Direct Connect Gateway]] we have multiple VPCs but one site. Depending upon its connection to either VPG (Virtual Private Gateway) or Transit Gateway VPCs can't and can talk to each other respectively.
	- ![[attachments/Pasted image 20220430201315.png]]

- *All the VPCs and on premise sites can talk to each other*.
- **Regional resource**, can work cross-region by *peering Transit Gateway across regions*.
	- ![[attachments/Pasted image 20220430203803.png]]

- **Route Tables are used to limit which VPC can talk with other VPC**
- Works with Direct Connect Gateway and Site to Site VPN connections
- **Only AWS service** which supports **IP Multicast** (not supported by any other AWS service)
- You can use AWS *Resource Access Manager* to share *Transit Gateway with other accounts*.

> [!important]+ You can **attach** the following resources to your transit gateway:
> - One or more *VPCs* 
> - One or more *Site to Site VPN connections*
> - One or more *AWS Direct Connect gateways*. DX connections can only be attached to transit gateway using the direct connect gateway.
> - One or more *transit gateway peering connections*

> [!question]- A company has established a dedicated network connection from its on-premises data centre to AWS Cloud using AWS Direct Connect (DX). The core network services, such as the Domain Name System (DNS) service and Active Directory services, are all hosted on-premises. The company has new AWS accounts that will also require consistent and dedicated access to these network services.
> Create a new Direct Connect gateway and integrate it with the existing Direct Connect connection. Set up a Transit Gateway between AWS accounts and associate it with the Direct Connect gateway.

> [!note] Go for transit gateway if the question mentions *multiple VPCs* or *multiple VPCs talking to each other*.

> [!note]+ Transit Gateway is *never the cheapest option*. So if the question mentions the word *cost effective* or *cheapest* then **don't go for transit gateway**.
> But it is always the **most scalable** and **resource efficient** option.

### Transit Gateway: ECMP/ECMR Site to Site VPN
- ECMP = **Equal-cost multi-path routing**
- Routing strategy to allow to **forward a packet over multiple best path**
- **Use case**: create *multiple Site to-Site VPN connections* to *increase the bandwidth of your connection to AWS*.

> [!note] Keywords: **scale the throughput of VPN connections**, *best path*, *additional VPN tunnels*.