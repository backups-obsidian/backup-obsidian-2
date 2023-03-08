---
created: 2022-04-19 16:22
updated: 2023-03-08 08:14
---
---
**Links**: [[101 AWS SAA Index]]

---
## VPC
- VPC is a *regional resource*.
- When you use your AWS account you have a default VPC in each region with a public subnet in each AZ but no private subnets.
- Amazon VPC enables you to launch AWS resources into a virtual network that you've defined. 
	- This virtual network closely resembles a traditional network that you'd operate in your own data centre, with the benefits of using the scalable infrastructure of AWS.
- Amazon *VPC is the networking layer for Amazon EC2*.
- VPC is **logically isolated** from other virtual networks in the AWS Cloud.
- We can have **multiple VPCs in an AWS region** 
	- **5 per region** is the *soft limit*

- **Max CIDR per VPC is 5**
	- **Min** size is **/28** (*16* IP addresses) 
	- **Max** size is **/16** (*65536* IP addresses)

- VPC is private and *only private IP ranges are allowed*. 
	- Class A: `10.0.0.0` to `10.255.255.255` (`10.0.0.0/8`) <- Big networks
	- Class B: `172.16.0.0` to `172.31.255.255` (`172.16.0.0/12`) <- *AWS default range*.
	- Class C: `192.168.0.0` to `192.168.255.255` (`192.168.0.0/16`) <- Home networks

> [!important]- Your *VPC CIDR should not overlap with your other networks* (corporate).
> - We do this to ensure that there are no problems when we try to connect these networks.
> - Also it should be in the CIDR range of *16 to 28*.

### Default VPC
- The default VPC has a **default subnet in every AZ**.
- All new accounts get a default VPC
- *New EC2 instances are launched in the default VPC* if no subnet is specified.
- **Default VPC has internet connectivity** and all EC2 instances inside it will have public IPv4 addresses.
- We also get a **public and private IPv4 DNS name** for the EC2 instances.
- We are given a default VPC because if we don’t have one it will be very difficult for the new comers.

## Subnets
- Once the VPC is created next step is to add subnets to the VPC.

- AWS **reserves 5 addresses** in a **subnet** (first 4 and last 1). These addresses are not available for use and cannot be assigned to EC2 instances. Example `10.0.0.0/24`
	- 10.0.0.*0*: **Network** Address
	- 10.0.0.*1*: *reserved by AWS* for the **VPC router**
	- 10.0.0.*2*: *reserved by AWS* for mapping to **Amazon-provided DNS**
	- 10.0.0.*3*: *reserved by AWS* for **future** use
	- 10.0.0.*255*: Network **Broadcast** Address. 

- *3 are reserved by AWS* and *2 are in general* reserved by any network. Router can also be thought of as general because any network will have it so mainly 2 by AWS.

> [!caution]- If you asked you need x IP address and what should be the CIDR range always remember 5 IP addresses are reserved.
> If you need *29* IP addresses then you need a CIDR of */26*.

- A VPC spans all the AZs in the region but a **subnet must reside entirely within one Availability Zone and cannot span zones**

> [!caution]- There will be questions asking how many subnets should be created
> - Always remember you have to atleast create 2 subnets in an AZ. *One private and one public* and for HA you need *atleast 2 AZs*. So minimum total of *4 subnets*.
> - There can be more and this depends on the application structure described by the question. If the question demands that the DB should be in a different private subnet from the application layer private subnet then there will be a total of *6 subnets* required for HA.

- In a public subnet we generally choose a small CIDR range since there are not many devices (bastion hosts, load balancer etc) that will be hosted in the public subnet.
- While creating subnets there should be no overlapping IP addresses.

## Why subnets and VPC
- At a high level, you can think of a VPC in AWS as a logical container that separates resources you create from other customers within the Amazon Cloud. It is you defining a network of your own within Amazon. You can think of a **VPC like an apartment** where your **furniture and items are analogous to databases and instances**. The walls of your apartment isolate and protect your things from being accessible to other tenants of the apartment complex.

- **Subnets would then be analogous to the different rooms in your apartment**. They are containers within your VPC that segment off a slice of the CIDR block you define in your VPC. **Subnets allow you to give different access rules and place resources in different containers where those rules should apply**. You wouldn't have a big open window in your bathroom on the shower wall so people can see you naked, much like you wouldn't put a database with secretive information in a public subnet allowing any and all network traffic. You might put that database in a private subnet (i.e. a locked closet).