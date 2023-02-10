---
created: 2023-02-06 22:18
updated: 2023-02-09 19:58
---
---
**Links**: [[102 AWS DVA Index]]

---
## Question Areas
- EC2 - Instance purchasing options
- Elastic Load Balancer - For extent check in video
- NACLs and SGs
- IAM
- Scaling policies
- Health checks load balancers and ASG

## Extra Notes
- Elastic Load balancer *cannot balance between regions*.
- The load balancer also monitors the health of its registered targets and ensures that it routes traffic only to healthy targets.
- Amazon S3, AWS WAF, and Amazon VPC are examples of services that support ACLs.

- Difference between ACM and IAM for certificates.
	- *AWS Certificate Manager* (ACM) is the *preferred tool* to provision, manage, and deploy server certificates.
	- In a **supported Region**, you can use ACM to manage server certificates from the console or programmatically.
	 - *IAM is used as a certificate manager* only when you must support HTTPS connections in a **Region that is not supported by ACM**.
	 - *IAM* supports deploying server certificates in all Regions, but you must *obtain your certificate from an external provider* for use with AWS. You cannot upload an ACM certificate to IAM.
- Amazon recommends *using Target Tracking Scaling policies*.
- By *default*, **IAM users do not have access to the AWS Billing and Cost Management console**. 
	- You can grant users access by **activating IAM user access** to the Billing and Cost Management console and **attaching** an IAM policy to your users.

- **AWS Budgets** lets customers set custom budgets and receive alerts if their costs or usage exceed (or are *forecasted* to exceed) their budgeted amount.
	- **AWS requires approximately 5 weeks of usage data to generate budget forecasts**.
	- If you set a budget to alert based on a forecasted amount, this *budget alert isn't triggered until you have enough historical usage information*.

- **Permissions boundary** and **AWS Organisation Service Control Policy (SCP)** *only limits permissions* but cannot grant permissions. 
	- Permission boundary defines the max permission that can be given to a user.
	- SCPs define the maximum permissions for account members of an organisation or organisational unit (OU).

- EBS volumes are AZ locked.
- Anything you create with Management Console might NOT have everything. For eg:
	- When you use management console to create an ASG using a launch configuration then EC2 instances won't have detailed monitoring enabled by default.
	- For CLI it is always more. For eg: **Detailed monitoring is enabled by default when you create a launch configuration using the AWS CLI or an SDK**.
- In general, when your object size reaches *100 MB*, you should consider using *multipart uploads* instead of uploading the object in a single operation.
	- Multipart uploads can also be useful if you have spotty connection.
- IOPS calculation:
	- io1/io2 is *50:1*: 50 IOPS per GB.
	- gp2 is *3:1*

> [!question]- The development team at a HealthCare company has deployed *EC2* instances in AWS *Account A*. These instances need to access patient data with Personally Identifiable Information (PII) on multiple *S3* buckets in another AWS *Account B*.
> - Create an *IAM role with S3 access in Account B* and set **Account A as a trusted entity**. 
> - Account A needs to be set as the trusted entity for the role in Account B since *only trusted entities can assume a role*.
> - Create another role (instance profile) in Account A and attach it to the EC2 instances in Account A and add an inline policy to this role to assume the role from Account B.

- RDS **Postgres** and **MySQL** IAM database authentication.