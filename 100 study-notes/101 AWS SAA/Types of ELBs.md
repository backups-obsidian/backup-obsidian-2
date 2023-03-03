---
created: 2022-04-19 16:22
updated: 2023-03-03 08:00
---
---
**Links**: [[101 AWS SAA Index]]

---

- There are 4 types of load balancers

## Classic Load Balancer
- Oldest one and **deprecated**.
- Do not use.
- Supported TCP (layer 4), HTTP and HTTPs (layer 7)

## Application Load Balancer
- Great for **micro services** and **container based applications**.
- Supports only **HTTP, HTTPS (layer 7)** load balancing.
- Supports **HTTP/2** and **websockets**.
- Load balancing to **multiple HTTP applications across machines** (**target groups**)
	- ![[attachments/Pasted image 20220609150032.png]]
- Load balancing to **multiple applications on the same machine** (ex: containers)
- It *supports redirects* from HTTP to HTTPS. 
- Can be also used as an internal load balancer.

- **Routing** to different target groups can be based on
	- *path*: `example.com/users` & `example.com/posts`
	- *hostname *: `one.example.com` & `other.example.com`
	- *query strings*: `example.com/users?id=123&order=false` 
	- *headers*
	- *request methods*
	- *source ip*

> [!tip] No routing is possible based on client's geography.

- Application Load Balancers support **Weighted Target Groups routing**. With this feature, you will be able to do weighted routing of the traffic forwarded by a rule to multiple target groups. This enables various use cases like **blue-green**, **canary and hybrid deployments** without the need for multiple load balancers

> [!important] We get a *fixed hostname* for the load balancer. We *don't get any public IP*.

- If the EC2 machines behind the load balancer want to **access the IP address of the client** and other information like protocol then they have to **use some special headers** since *communication between the ALB and the EC2 machine takes place over a private network* and connection termination takes place at ALB. 
	- EC2 instances always see the traffic coming from the private IP address of the ALB. 
	- `X-Forwarded-For`: *Source IP address*
	- `X-Forwarded-Proto`: *Protocol* 
	- `X-Forwarded-Port`: *Port* 
	- These headers are automatically added by the ALB

- ALB **supports authentication** from *OIDC (Open ID Connect)* compliant identity providers such as Google, Facebook and Amazon. It is *implemented through an authentication action on a listener rule* that integrates with **Amazon Cognito user pools**.
 ^92a439
- **Health checks** are performed using **HTTP and HTTPS**.
- **Health checks can fail** for the following reasons:
	- The *security group* of the EC2 instance does not allow for traffic from the security group of the Application Load Balancer.
	- The *route* for the health check is *misconfigured*. 

### Target Groups
- *Health checks happen at the target group level*.
- ALBs can route to multiple target groups.
- Supported target groups by ALB are:
	- **EC2 instances** (can be *managed by an Auto Scaling Group*): *HTTP*
	- **ECS tasks** (*managed by ECS* itself): *HTTP*
	- **Lambda functions**: *HTTP request is translated into a JSON event*
	- **IP Addresses**: must be *private IPs*. They are used for the following  ^343219
		- *Instances in a peered VPC*.
		- AWS *resources that are addressable by IP address and port*.
		- *On-premises resources linked to AWS* through **Direct Connect** or a **VPN connection**.

> [!important]- Specifying targets based on **instance IDs** and **IP addresses**.
> - If you specify targets using an **instance ID**, traffic is routed to instances using the **primary private IP** address specified in the primary network interface for the instance.
> - If you specify targets using **IP addresses**, you can route traffic to an instance using **ANY private IP address** from one or more network interfaces. *This enables multiple applications on an instance to use the same port*.

## Network Load Balancer

- Only supports **TCP/UDP/TLS (secure TCP) (layer 4)** load balancing.
- Used for **extreme performance**. It can handle *millions of requests per second*.
- Less latency (*100 ms*) as compared to ALBs (*400 ms*).
- It maintains a *single connection* between the client and the EC2 machine. So we can *passthrough traffic* from NLB and *terminate SSL at EC2 instances*.

> [!caution]- NLB has **one static IP per AZ** and **supports assigning elastic IP**. This feature is important. In *ALB and CLB we only get fixed hostname*.
> - Go for NLBs if the question mentions anything about IPs since ALBs can be target groups for NLBs. 
> - But if the question mentions multi region balancing then go for [[Global Accelerator]].

> [!note] We *cannot use SGs* with NLBs.

> [!note] NLBs also *support TLS offloading* just like ALBs and CLBs (SSL only).

### Target Groups

- Supported target groups are:
	- **EC2 instances**
	- **IP Addresses**: must be *private IPs*. They are used for [[Types of ELBs#^343219|the same reasons as ALB]]
	- **ALBs**

### Request Routing
- If you specify targets using an **instance ID**, traffic is *routed to instances* using the **primary private IP address** specified in the primary network interface for the instance. The load balancer rewrites the destination IP address from the data packet before forwarding it to the target instance.
- If you specify targets using **IP addresses**, you can route traffic to an instance using **any private IP address from one or more network interfaces**. This enables *multiple applications on an instance to use the same port*. Note that each network interface can have its security group. The load balancer rewrites the destination IP address before forwarding it to the target.

## Gateway Load Balancer

- **Newest addition**
-  *Inspect the traffic* before going to the application using firewalls, intrusion detection, deep packet inspection etc.
-  Operates at the **lowest level (layer 3)** as compared to other load balancers. 
-   It uses the **GENEVE** protocol on **6081** 
- **Single source of entry and exit** for all traffic.
	- ![[attachments/Pasted image 20220420155358.png]]

### Target Groups

- Supported target groups are:
	- **EC2 instances**
	- **IP addresses**: must be *private IPs*. Used for the same things as ALB and ELB.
	
