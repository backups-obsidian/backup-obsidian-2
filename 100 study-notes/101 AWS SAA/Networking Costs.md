---
created: 2023-03-08 10:29
updated: 2023-03-09 08:46
---
---
**Links**: [[101 AWS SAA Index]]

---
## Networking Costs
### Minimising inter AZ/region network cost
> [!note] Inbound traffic (**ingress**) from outside to AWS is **free**.

-  Always use *private IP over public IP* for **cost savings** and **better network performance**.
	- In case of *inter AZ transfer* if you are using *private IP then it costs you less than public IP*.
	- If EC2 instances are *within the same AZ* and are *using private IP* to communicate then there will be no traffic charges.
	- Use *same AZ* for **maximum cost savings** at the *cost of HA* (High Availability).
- Diagram:
	- ![[attachments/Pasted image 20230309083325.png]]

### Minimising egress traffic network cost
> [!note] Outbound traffic (**egress**) from AWS to outside is **charged**.

- *Try to keep as much internet traffic within AWS* to minimise costs
	- ![[attachments/Pasted image 20220430214328.png]]
- *Direct Connect location* that are *co-located in the same AWS Region*
result in *lower cost for egress network*.

### S3 Data Transfer Pricing
- *S3 ingress*: **free**
- S3 to Internet: $0.09 per GB
- S3 Transfer Acceleration:
	- Faster transfer times (50 to 500% better)
	- Additional cost on top of Data Transfer Pricing: +*$0.04 to $0.08 per GB*
- *S3 to CloudFront*: **$0.00 per GB**
- *CloudFront to Internet*: $0.085 per GB (**slightly cheaper than S3**)
	- Caching capability (lower latency)
	- **Reduce costs associated with S3 Requests** Pricing (*7x cheaper with CloudFront*)
- *S3 Cross Region Replication*: $0.02 per GB
- Diagram:
	- ![[attachments/Pasted image 20230309084202.png]]

### NAT Gateway vs Gateway VPC Endpoint
- Examples on how to avoid egress charges
	- Use an Amazon EC2 instance in the same Region as the S3 bucket
	- Use lambda@edge for compressing data before sending to users.
- **Always use VPC Endpoints over NAT Gateway**
	- ![[attachments/Pasted image 20230309084529.png]]