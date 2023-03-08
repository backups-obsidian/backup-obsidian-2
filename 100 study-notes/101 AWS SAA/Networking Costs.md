---
created: 2023-03-08 10:29
updated: 2023-03-08 10:30
---
---
**Links**: [[101 AWS SAA Index]]

---
## Networking Costs
-  Always use *private IP over public IP* for cost savings.
	- In case of *inter AZ transfer* if you are using *private IP then it costs you less than public IP*.
	- In the same AZ no cost if using private IP.
- You don't pay for data coming in, you always **pay for data going out of AWS**.
	- Try to keep as much internet traffic within AWS to minimise costs
		- ![[attachments/Pasted image 20220430214328.png]]
	- Examples on how to avoid egress charges
		- Use an Amazon EC2 instance in the same Region as the S3 bucket
		- Use lambda@edge for compressing data before sending to users.
	- Always use VPC Endpoints over NAT Gateway.
- There is no price when data goes from S3 to CloudFront
	- ![[attachments/Pasted image 20220430214822.png]]