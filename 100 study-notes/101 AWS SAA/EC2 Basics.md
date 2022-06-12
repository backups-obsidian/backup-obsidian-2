---
created: 2022-04-19 10:02
updated: 2022-05-02 13:21
---
---
**Links**: [[101 AWS SAA Index]]

---
## Introduction
- It is a **region scoped**.
- When you **stop and start** an instance its **public IP will change** but private IP remains same. If you want a fixed IP then you will have to use *Elastic IPs*.
- When you *start and stop an instance the underlying host may change*. In general it is changed.
	- ![[attachments/Pasted image 20220420105457.png]]

## vCPUs
- vCPUs (virtual CPUs) are nothing but the **number of threads**. When select an EC2 instance you only see the vCPU.
- Multiple threads can run on one vCPU (multithreading).
- So if `m5.2xlarge` has 4 CPUs and 2 threads per CPU then we have a total of 8 vCPUs.
- We can **optimise the vCPU** for cost savings. May be you need more RAM but less number of vCPUs or you don’t need multithreading for HPC workloads.
	- ![[attachments/Pasted image 20220419195118.png]]
- There is a vCPU based limit on all types of [[EC2 Instance Purchasing Options]] per region. Like we can purchase *only 20 reserved instances in a region*. To increased this limit we have to request AWS.

## Elastic IPs
-  We can attach Elastic IP to **only one instance at a time**.
- We can only have **5 Elastic IP** in our account.
- Overall we should try and avoid using Elastic IP they reflect poor architecture.
- For Elastic IPs you are *charged as long as you have them and don’t use them*. Once we provision an Elastic IP we need to *associate it with an EC2 instance as soon as possible*. Once you are done using the machine then *disassociate the Elastic IP* from the machine and then *release it*.
- If you instance is in a *stopped or terminated state* and has an *Elastic IP attached* to it then you will be *billed* for it. If it is running you won't be billed for the Elastic IP.

> [!tip]- So for elastic IP to not be billed three things have to be followed
> - The *Elastic IP address is associated* with an Amazon EC2 instance.
> - The *instance* associated with the Elastic IP address is *running*.
> - The **instance has only one Elastic IP address attached to it**.

## EC2 User Data
- Using EC2 User Data we can run a script when our **instance boots for the first time**.
- We can run **shell scripts** and **cloud init directives**.
- It is not run when rebooting instances *but*
	- You can *update your configuration* to ensure that your user data scripts and cloud-init directives run *every time you restart your instance*. By default, the scripts are run, only once during the boot process while first launching the instance.

- It helps in *automating boot tasks*.

> [!caution] EC2 User Data script **runs with the root user**. Any command you have in there will have sudo rights.

> [!tip] You need to have the following at the beginning of you script `#!/bin/bash`

## Instance Types
- **7** types of instances
	- **General Purpose**: for everything. `t2.micro` is a general purpose instance.
	- **Compute optimised**: for *HPC*, *gaming servers*, *dedicated web servers*, *ML*.
	- **Memory optimised**: for in memory data bases like *redis*, real time processing of *unstructured data* (key value pairs.)
	- **Storage optimised**: for *databases*, *OLTP* systems, *relational and non relational databases*.

- `m5.2xlarge`: Here `m` is the **instance class**. `5` is the **generation** which AWS upgrades over time and `2xlarge` is the instance **class size**.

## Security Groups
- It is **region scoped** just like EC2.
- Security groups act to **firewall** to EC2 instances.
- Security groups are **stateful**. Once traffic goes in it will come out unaffected by the outbound rules.
- Security groups can only contain **allow rules**. So you can only make the security groups more permissive.
- A single security group can be attached to multiple instances and a single instance can have multiple security groups. *Many to many relation*.
- **Security Groups live outside of EC2** so if the traffic is blocked the EC2 instance won’t see it.
-  If your application is not accessible (time out) then it is a security group issue. If the application gives a connection refused error then it is an application error.

>[!important] For a **new security group** all *inbound* traffic is *blocked* and all *outbound* traffic is *allowed*

>[!important] For the **default security group** *inbound* traffic is **allowed from the same SG** and all *outbound* traffic is *allowed*.

- So outbound rule is allowed in both the new and default security group.
- You can delete the all outbound traffic allowed rule if you don’t want your instances to access the internet.
- Using security group you create inbound/outbound rules based on **port**, **protocol**, and **source**/**destination**.
- We can **reference security groups** in other security groups. Like EC2 instances can reference load balancer security group in the their inbound rules source. ^52df0e
- **Important ports** 
	- 22: SSH, SFTP
	- 21: FTP
	- 80, 443: Web traffic
	- **3389**: RDP

## Miscellaneous
- Private key to access EC2 instances are also known as **key pairs** in questions.
