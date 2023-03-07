---
created: 2023-03-07 10:14
updated: 2023-03-07 10:56
---
---
**Links**: [[114 AWS SOA Index]]

---
## S3 Website with Route53
- For `acme.example.com`:
	- Create an **S3 bucket with the same name as the target record (`acme.example.com`)**
	- *Enable S3 website on the bucket* (and *enable S3 bucket public settings*)
	- Create a Route 53 **Alias record** to the S3 website endpoint or *type A* - IPV4 address
- This **only works for HTTP traffic** (for HTTPS, use CloudFront)

## Hybrid DNS
- By *default*, Route 53 Resolver automatically answers DNS queries for:
	- Local domain names for EC2 instances
	- Records in Private Hosted Zones
	- Records in public Name Servers
- **Hybrid DNS** - *resolving DNS queries between VPC* (Route 53 Resolver) and your networks (*other DNS Resolvers*)
- *Networks* can be:
	- VPC itself/*Peered VPC*
	- **On-premises Network** (*connected through Direct Connect or AWS VPN*)

- There are *2 different endpoints*.
- These endpoints are *associated with one or more VPCs* in the same region.
- Create endpoints in two AZs for high availability.

### Resolver Inbound Endpoint
- DNS Resolvers on your network can forward DNS queries to Route 53 Resolver
- Allows **your DNS Resolvers to resolve domain names for AWS resources** (e.g., EC2 instances) and records in Route 53 Private Hosted Zones
- *DNS queries for resources in the AWS VPC **from** the on-premises network*.
	- **On Premises -> AWS**.
- *Requests coming in from exterior* go into the AWS.
- Diagram:
	- ![[attachments/Pasted image 20230307103959.png]]

### Resolver Outbound Endpoint
- **Route 53 Resolver conditionally forwards DNS queries to your DNS Resolvers**.
- Use **Resolver Rules** to **forward** DNS queries to your DNS Resolvers.
- *DNS queries for any resources in the on-premises network from the AWS VPC*.
	- **AWS -> On premises**
- Requests go out of AWS.
- Diagram:
	- ![[attachments/Pasted image 20230307104153.png]]

#### Resolver Rules
- Control *which DNS queries are forwarded to DNS Resolvers* on your network
- **Conditional Forwarding Rules** (Forwarding Rules)
	- *Forward DNS queries for a specified domain and all its subdomains to target IP addresses*.
- **System Rules**
	- Selectively **overriding** the *behaviour defined in Forwarding Rules* (e.g. don't forward DNS queries for a subdomain acme.example.com)
- **Auto-defined System Rules**
	- Defines *how DNS queries for selected domains are resolved* (e.g., AWS internal domain names, Private Hosted Zones)
- If multiple rules matched, Route 53 Resolver chooses the most specific match 
- Diagram:
	- ![[attachments/Pasted image 20230307105541.png]]
