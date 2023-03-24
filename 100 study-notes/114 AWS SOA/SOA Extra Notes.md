---
created: 2023-03-11 08:28
updated: 2023-03-24 09:22
---
---
**Links**: [[114 AWS SOA Index]]

---
## Extra
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
- You can *create a dashboard in CloudWatch* to display custom metrics. 
	- To do this you simply *create a dashboard*, *add a widget*, and *choose the metrics* from the custom namespace.

> [!question]- A company uses AWS Organisations to manage several AWS accounts. A department in the company requires a new AWS account. A SysOps Administrator must create the new account and *configure user-defined cost allocation tags*. What should the Administrator do to enable user-defined cost allocation tags?
> Use the *Tag Editor in the new account to create the new user-defined tags*, then use the *Billing and Cost Management console* in the **payer account** to mark the tags as cost allocation tags.

- We can get *503 from S3 hosted website* if the request rate is very high.
- *CloudTrail* can be used to **see the results of the API calls**.

> [!question]- A security team has identified an attack on web applications running on Amazon EC2. The attack uses malformed HTTP headers. Which AWS service or feature can be used to prevent this type of attack from reaching the EC2 instances?
> Application Load Balancer (ALB)
> ---
> - An application load balancer will **block malformed requests** that do not meet the HTTP specification with a *HTTP 400: Bad request error*. An ALB can be placed in front of the EC2 web applications and this will prevent the attack from reaching the instances.
> - The ALB attribute *Drop Invalid Header Fields* setting can be used to control if invalid header fields are removed by the load balancer.

> [!question]- An Amazon EC2 instance was launched from a *Microsoft Windows 2012* AMI and is inaccessible using Remote Desktop Protocol (RDP), or over the network. Another instance was deployed using a different AMI but the same configuration options and is functioning normally. Which next step should a SysOps Administrator take to *troubleshoot* the problem?
> Use EC2Rescue to gather operating system log files for analysis
> ---
> *EC2Rescue for EC2 Windows is a convenient, straightforward, GUI-based troubleshooting tool* that can be run on your Amazon EC2 Windows Server instances to troubleshoot operating system-level issues and collect advanced logs and configuration files for further analysis. EC2Rescue simplifies and expedites the troubleshooting of EC2 Windows instances.

- There are only 2 geolocations:
	- Route53 Geolocation
	- CloudFront Georestriction
- *RDS* and *ElastiCache* offer *maintenance windows*.

> [!question]- Each IT staff member in a company uses a unique IAM user account. Permissions are applied to users using IAM policies and IAM groups. The security team has requested that staff members should log in with their on-premises Active Directory user accounts instead of their IAM user accounts when accessing the AWS Management Console. Which solution can a SysOps Administrator implement to the requirements of the security team?
> Implement a *VPN tunnel and configure an Active Directory connector*.

- *ALB* and *CloudFront access logs* show *layer 7* information whereas *VPC flow logs* show *layer 3/4* information.

> [!note] You *CANT share a snapshot* that's encrypted using the **default AWS KMS** encryption key.

- When using *multiple accounts within a Region* it is important to understand that the **name of the Availability Zone in each account may map to a different underlying AZ**. 
	- For instance, us-east-1a may map to a different AZ in one account vs another.
	- To identify the location of your resources relative to your accounts, you must use the _AZ ID_, which is a unique and consistent identifier for an Availability Zone. 
	- For example, use1-az1 is an AZ ID for the us-east-1 Region and it is the same location in every AWS account.
- In case of AWS Config related questions always try to look for options which avoid writing custom code.

> [!question]- A company wants to use an AWS IAM role with a *SAML 2.0*-compliant identity provider (IdP) and AWS to permit federated users to *access the AWS Management Console*. The workflow should open the AWS Management Console on behalf of the user. Which of the following workflow steps should be included?
> Configure the client to post a *SAML assertion and use an AWS SSO endpoint*.
> ---
> This workflow opens the AWS Management Console on behalf of the user. This requires the use of the AWS SSO endpoint instead of directly calling the AssumeRoleWithSAML API.

- Directory services SG red flag no need to worry.
- If your AMI contains a CloudWatch agent, it’s automatically installed on EC2 instances when you create an EC2 Auto Scaling group.
- When *two instances communicate using public IP addresses*, the following three scenarios are possible: 
	- Traffic between two EC2 instances in the **same AWS Region** stays **within the AWS network**, even when it goes over **public IP addresses**.
	- Traffic between EC2 instances in **different AWS Regions** stays **within** the AWS network **if** there is an **Inter-Region VPC Peering connection** between the VPCs where the two instances reside.
	- Traffic between EC2 instances in **different AWS Regions** where there is **no Inter-Region VPC Peering connection** between the VPCs where these instances reside, is **NOT guaranteed to stay within the AWS network**.
- It *ISN'T possible to restore or recover a deleted or deregistered AMI*.

> [!question]- A video streaming app uses Amazon Kinesis Data Streams for streaming data. The systems administration team needs to be informed of the shard capacity when it is reaching its limits. How will you configure this requirement?
> **Monitor Trusted Advisor** service check results with Amazon *CloudWatch Events* - AWS Trusted Advisor checks for service usage that is more than 80% of the service limit.

- For Lambda function to be configured as a target to EventBridge we have to configure resource based policies.

> [!question]- A retail company stores its business-critical files on an Amazon S3 bucket that is *also configured as a website endpoint*. The company needs a robust configuration that will allow access only through CloudFront. No user or team member should be able to access the files directly from Amazon S3 URL. As a SysOps Administrator, which of the following would you suggest to address this requirement?
> Setup the Amazon S3 bucket as a **custom origin** with CloudFront. Restrict the access to content by setting up custom headers.
> ---
> - If we use an *Amazon S3 bucket configured as a website endpoint, you must set it up with CloudFront as a custom origin*. 
> - You **CANT use the origin access identity feature**. 
> - However, you can restrict access to content on a custom origin by setting up custom headers and configuring your origin to require them.

- If all instances in an Auto Scaling group are protected from *termination during scale in*, and a scale-in event occurs, its *desired capacity is decremented*. 
	- However, the Auto Scaling group *can't terminate the required number of instances* until their instance scale-in protection settings are disabled.
- 5 PB of data - snowball edge.
- Create one CloudWatch dashboard and **add a graph per region** using the *region selector* in the top right corner of the AWS Console.
- By default, when you attach a *non-root EBS volume to an instance, its `DeleteOnTermination` attribute is set to false*. 
	- Therefore, the default is to *preserve these volumes*.
	- After the instance terminates, you can take a snapshot of the preserved volume or attach it to another instance. 
	- You *must delete a volume to avoid incurring further charges*.
- By design, Aurora Serverless connects to a proxy fleet of DB instances that scales automatically. 
	- Because there *isn't a direct DB instance to access and host the log files*, you can't view the logs directly from the Amazon Relational Database Service (Amazon RDS) console. 
	- However, you can view and download logs that are sent to the CloudWatch console.

> [!question]- A telecommunications company runs its business on AWS Cloud with Amazon EC2 instances and Amazon S3 buckets. Of late, users are complaining of **intermittently** receiving 500 Internal Error response when accessing the S3 bucket. The team is looking at a way to track the frequency of the error and fix the issue. What will you suggest to monitor and fix the error?
> Enable Amazon CloudWatch metrics that include a metric for 5xx server errors. *Retrying generally fixes this error*.

> [!question]- An application hosted on Amazon EC2 instances polls messages from Amazon SQS queue for downstream processing. The team is now looking at configuring an Auto Scaling group to scale using the CloudWatch metrics for Amazon SQS queue to process messages without delays. As a Systems Administrator, which feature or dimension of an SQS queue will you pick to collect SQS data from CloudWatch metrics?
> *Queue name of the SQS queue* should be used to fetch the necessary data from CloudWatch metrics.
> ---
> *The only dimension that Amazon SQS sends to CloudWatch is QueueName*. This means that all available statistics are filtered by QueueName.

> [!question]- A developer is trying to access an Amazon S3 bucket for storing the images used by the web application. The S3 bucket has public read access enabled on it. However, when the developer tries to access the bucket, an error pops up - `403 Access Denied`. The confused developer has connected with you to know why he has no access to the public S3 bucket. As a SysOps Administrator, how will you troubleshoot this issue?
> Run the `AWSSupport-TroubleshootS3PublicRead` automation document on AWS Systems Manager to help diagnose issues with accessing objects from a public S3 bucket.

- ALB -> Listeners -> Target Groups

## Interesting Ideas
- To automatically shut down unused instances: create an Amazon CloudWatch alarm that monitors the CPUUtilization metric and stops the EC2 instances if the utilization is <5% for a 30-minute period.
