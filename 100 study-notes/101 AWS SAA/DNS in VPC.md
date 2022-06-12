---
created: 2022-04-19 16:22
updated: 2022-04-30 08:49
---
---
**Links**: [[101 AWS SAA Index]]

---
## DNS Resolution
- Decides if **DNS resolution from Route53 resolver** server is supported for VPC.
- `enableDnsSupport`: **True (default)** 
	- It **queries the Amazon Provider DNS server at 169.254.169.253** or 
	- The reserved IP address at the base of the **VPC IPv4 network range plus two (.2)**

- If we want to *visit google.com* then since we have enabled DNS support the instances can *reach out to the Route53 resolver* at one of the two IPs mentioned above to get the IP address of google.
	- ![[attachments/Pasted image 20220430083904.png]]

- If  *DNS Support disabled* it is still possible to get DNS resolution but we will have to create our own *custom DNS server*.
	- ![[attachments/Pasted image 20220430084008.png]]

## DNS Hostnames
- It is responsible for giving public DNS hostnames to the EC2 instances in that subnet. If it is false we only get a private DNS.

- `enableDnsHostnames`
	- **true** → **default** VPC
		- In a default VPC instances will be assigned a public and private DNS hostname
	- **false** → **newly created**/non default VPCs
		- In a non-default VPC instances will be assigned a private but *not a public DNS hostname*
	- ![[attachments/Pasted image 20220430084254.png]]

- For it to work **enableDnsSupport should be True**.
- If we use custom DNS domain names *in a Private Hosted Zone in Route53 then both attributes should be true*.

> [!caution] So for public hostnames the *DNS resolution* and *DNS hostname* of the VPC configuration should be *enabled*.
