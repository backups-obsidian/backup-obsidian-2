---
created: 2023-03-11 08:28
updated: 2023-03-11 09:51
---
---
**Links**: [[114 AWS SOA Index]]

---
> [!question]- An organisation has multiple AWS accounts to manage different lines of business. A user from the Finance account has to access reports stored in Amazon S3 buckets of two other AWS accounts (belonging to the HR and Audit departments) and copy these reports back to the S3 bucket in the Finance account. The user has requested the necessary permissions from the systems administrator to perform this task. As a SysOps Administrator, how will you configure a solution for this requirement?
> Create *identity-based IAM policy* in the *Finance account* that allows the user to *make a request to the S3 buckets in the HR and Audit accounts*. Also, create *resource-based IAM policies in the HR, Audit accounts that will allow the requester from the Finance account* to access the respective S3 buckets.
> ---
> - For requests made from one account to another, the *requester in Account A* must have an *identity-based policy* that allows them to make a request to the resource in Account B. Also, the *resource-based policy in Account B* must *allow the requester in Account A to access the resource*. There must be policies in both accounts that allow the operation, otherwise, the request fails.

- *Scheduled events are managed by AWS*; you cannot schedule events for your instances.

> [!question]- An analytics company generates reports for various client applications, some of which have critical data. As per the company's compliance guidelines, data has to be encrypted during data exchange, for all channels of communication. An Amazon S3 bucket is configured as a website endpoint and this is now being added as a custom origin for CloudFront. How will you secure this channel, as per the company's requirements?
> Configure CloudFront to mandate viewers to use HTTPS to request objects from S3. **CloudFront and S3 will use HTTP to communicate with each other**.

> [!question]- The Chief Technology Officer (CTO) of a healthcare company realised that he *does not have access to an Amazon S3 bucket present in the company's own AWS account*. The CTO is the *root user* for the AWS account and has created other AWS users using the root user account. What is the reason for this behaviour and how can you fix this?
> If an **IAM user, with full access to IAM and Amazon S3**, assigns a *bucket policy to an Amazon S3 bucket and doesn't specify the AWS account root user as a principal*, the root user is denied access to that bucket.
> ---
> - Sometimes, you might have an IAM user with full access to IAM and Amazon S3. If the IAM user assigns a bucket policy to an Amazon S3 bucket and doesn't specify the AWS account root user as a principal, the root user is denied access to that bucket.
> - *However, as the root user, you can still access the bucket*. To do that, modify the bucket policy to allow root user access from the Amazon S3 console or the AWS CLI. Use the following principal, replacing 123456789012 with the ID of the AWS account.

- When you *delete or deregister an Amazon EBS-backed AMI*, any **snapshots created for the volume of the instance during the AMI creation process are retained**. 
	- If you accidentally delete the AMI, you can launch an identical AMI using one of the retained snapshots.
- For an ALB *one target can be associated with multiple target groups*.
- We can use `resource import` to import existing resources and bring it under the control of CloudFormation.
- **S3** is the only service for an *in-place switch from unencrypted to encrypted without impacting existing operations*.

