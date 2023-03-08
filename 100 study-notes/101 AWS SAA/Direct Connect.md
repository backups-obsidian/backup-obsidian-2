---
created: 2022-04-19 16:22
updated: 2023-03-08 10:14
---
---
**Links**: [[101 AWS SAA Index]]

---
## Direct Connect
- Also known as **DX**.
- It provides a **dedicated private connection** from a remote network *to your VPC*.
- Dedicated connection *must be setup between your Data Centre and AWS Direct Connect locations*.
- You need to **setup a Virtual Private Gateway on your VPC**. We also needed a VPG in [[Site to Site VPN]].
- We can access public and private resources on the same network. Like we *can access public resources (S3) and private (EC2) on same connection*.

- **Use Cases**:
    - **Increase bandwidth throughput** working with large data sets lower cost
    - **More consistent network experience** applications using real-time data feeds
    - **Hybrid Environments** (on premise + cloud)
- Supports both IPv4 and IPv6.
- **Everything is private** and *nothing goes over the public internet*. There by we can say we are bypassing the ISPs.

- There are **2 connection types**:
	- *Dedicated*: *better speed* with a physical ethernet port dedicated to the customer.
	- *Hosted*: 

- We have **private virtual interfaces** in DX.

> [!note]- *Specific location DX locations* also known as Direct Connect Partners, Both *public and private resources*, *VPG*
> We have a private VIF (virtual interface) for connecting to EC2 instances in the private subnet and a public VIF for connecting to public resources like S3.
> ---
> ![[attachments/Pasted image 20220430194657.png]]

> [!caution] It can take more than **1 month** to *establish a DX connection*. So if you want something quick go for Site to Site VPN.


### Direct Connect Gateway
- You *integrate Direct Connect Gateway with an existing Direct Connection first*.
- If you want to setup a Direct Connect to **one or more VPC** in many **different regions** (**same account**), you must use a Direct Connect Gateway.
- For comparison in [[Site to Site VPN#VPN CloudHub|VPN CloudHub]] we had one VPCs and multiple sites. In Direct connect gateway we have *multiple VPCs and one site*.
	- ![[attachments/Pasted image 20220430195200.png]]

- A direct connect gateway **can be associated to**:
	- **VPG**: In the above example picture it is associated with VPG shown by the lock symbol. *The VPCs can't communicate with each other they can only communicate to the site*.
	- [[Transit Gateway]]: The VPCs can communicate to each other. A transit gateway is attached to a Direct Connect gateway using a **transit virtual interface**.

> [!note] Keywords for Direct connect gateway: *multiple VPCs*, *many different regions*, *same account*, *one on premise region*.

### Encryption
- **Data** in transit is **not encrypted** **but** is **private**
- [[Site to Site VPN#^a5f822|AWS Direct Connect + VPN]] provides an IPsec-encrypted private connection

### Resiliency
> [!tip]- We can use *site to site connection as a backup* for DX instead of using a second DX connection as it can be expensive.
> ![[attachments/Pasted image 20230308101302.png]]

- **High Resiliency**: One connection at multiple locations.
	- *Wording*: Opt for *one Direct Connect connection* at each of the *multiple Direct Connect locations*
	- ![[attachments/Pasted image 20220430200332.png]]
- **Maximum resiliency**: Separate connections terminating on separate devices in more than one location.
	- *Wording*: Opt for *two separate Direct Connect connections* terminating on *separate devices* in *more than one Direct Connect location*.
	- ![[attachments/Pasted image 20220430200345.png]]
- **Non critical** production or development workload:
	- *Wording*: Opt for at least two Direct Connect connections terminating on different devices at a single Direct Connect location
	- ![[attachments/Pasted image 20220502161454.png]]

> [!note] The *inner lines are the connections*. What they are passing through are the locations.

> [!important] For resiliency in DX, *DX location* has a *higher preference than the number of connections*.
