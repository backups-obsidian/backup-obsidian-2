---
created: 2023-02-06 22:18
updated: 2023-02-25 15:57
---
---
**Links**: [[102 AWS DVA Index]]

---
## Extra Notes
- The *load balancer* also monitors the health of its registered targets and ensures that it *routes traffic only to healthy targets*.
- Amazon S3, AWS WAF, and Amazon VPC are examples of services that support ACLs.

- Amazon recommends *using Target Tracking Scaling policies*.
- By *default*, **IAM users do not have access to the AWS Billing and Cost Management console**. 
	- You can grant users access by **activating IAM user access** to the Billing and Cost Management console and **attaching** an IAM policy to your users.

- **AWS Budgets** lets customers set custom budgets and receive alerts if their costs or usage exceed (or are *forecasted* to exceed) their budgeted amount.
	- **AWS requires approximately 5 weeks of usage data to generate budget forecasts**.
	- If you set a budget to alert based on a forecasted amount, this *budget alert isn't triggered until you have enough historical usage information*.

- **Permissions boundary** and **AWS Organisation Service Control Policy (SCP)** *only limits permissions* but cannot grant permissions. 
	- Permission boundary defines the max permission that can be given to a user.
	- SCPs define the maximum permissions for account members of an organisation or organisational unit (OU).

- Anything you create with Management Console might NOT have everything. For eg:
	- When you use management console to create an ASG using a launch configuration then EC2 instances won't have detailed monitoring enabled by default.
	- For CLI it is always more. For eg: **Detailed monitoring is enabled by default when you create a launch configuration using the AWS CLI or an SDK**.

- IOPS calculation:
	- io1/io2 is *50:1*: 50 IOPS per GB.
	- gp2 is *3:1*

> [!question]- The development team at a HealthCare company has deployed *EC2* instances in AWS *Account A*. These instances need to access patient data with Personally Identifiable Information (PII) on multiple *S3* buckets in another AWS *Account B*.
> - Create an *IAM role with S3 access in Account B* and set **Account A as a trusted entity**. 
> - Account A needs to be set as the trusted entity for the role in Account B since *only trusted entities can assume a role*.
> - Create another role (instance profile) in Account A and attach it to the EC2 instances in Account A and add an inline policy to this role to assume the role from Account B.

- **S3 Object Ownership** is an Amazon S3 bucket setting that you can use to control *ownership of new objects that are uploaded to your buckets*. 
	- **By default**, when other AWS accounts upload objects to your bucket, the objects remain owned by the **uploading account**. 
	- With **S3 Object Ownership**, any new objects that are written by other accounts with the `bucket-owner-full-control` canned access control list (ACL) *automatically become owned by the bucket owner*, who then has full control of the objects.
	- S3 Object Ownership has *two settings*: 
		- **Object writer** – The **uploading account** will own the object. 
		- **Bucket owner preferred** – The **bucket owner** will own the object if the object is uploaded with the `bucket-owner-full-control` canned ACL. 
		- *Without this setting and canned ACL, the object is uploaded and remains owned by the uploading account*.

> [!note]- **Adding read replicas will NOT help in reducing latency** when compared to a caching solution.

- For RDS **automated backups** are limited to a **single AWS Region** while **manual snapshots** and **Read Replicas** are supported across **multiple Regions**.

- Customers may use four mechanisms for controlling access to Amazon S3 resources:
	- IAM policies
	- Bucket policies
	- ACLs
	- **Query string authentication (presigning URLS)**


> [!question]- A company uses an Amazon S3 bucket to store a large number of sensitive files relating to eCommerce transactions. *The company has a policy that states that all data written to the S3 bucket must be encrypted*. How can a Developer ensure compliance with this policy?
> Create an S3 *bucket policy* that *denies any S3 Put request* that does not include the `x-amz-server-side-encryption`.

- Go for *customer managed AWS policies over AWS managed AWS policies* for more security and fine grained control.

> [!question]- A web application has been deployed on AWS. A developer is concerned about exposure to common exploits that could affect application availability or compromise security. Which AWS service can protect from these threats?
> AWS WAF.

- Even if you are doing an upsert using indirectly using `dynamodb:UpdateItem` you need `dynamodb:PutItem` permission.

- **API Gateway resource policies**:
	- *API Gateway supports resource policies*, which are used to *control access to API Gateway resources such as REST APIs, WebSocket APIs,* and resources within those APIs such as methods, stages, and deployment. 
	- Resource policies are similar to IAM policies in that they are JSON documents that specify who can access the resource and what actions they can perform.
	- Resource policies can be used to *provide fine-grained control over access to API Gateway resources*, and they can be used in conjunction with IAM roles and policies to provide comprehensive access control. 
	- For example, you can use resource policies to:
		- *Restrict access to specific API methods based on the IP address* or the AWS account of the caller.
		- Grant read-only access to a specific stage of an API to a particular IAM user or role.
		- **Allow anonymous access to certain API methods while requiring authentication for others**.
	- Resource policies are typically *used in scenarios where you need to provide access to API Gateway resources to **external entities** that don't have an IAM role or user*. 
		- For example, you might use a resource policy to allow a third-party API gateway to access your API, or to *allow access from a partner organisation that doesn't have an AWS account*.
- Create an **AWS CloudTrail trail and apply it to all regions**, configure logging to a single S3 bucket.

> [!question]- A team of Developers require access to an AWS account that is a member account in AWS Organisations. The administrator of the master account needs to restrict the AWS services, resources, and API actions that can be accessed by the users in the account. What should the administrator create?
> A **service control policy**.
> ---
> You can configure the SCPs in your organisation to work as either of the following:
> A **deny list** – actions are allowed by default, and you specify what services and actions are prohibited
> An **allow list** – actions are prohibited by default, and you specify what services and actions are allowed

> [!question]- A developer has a user account in the Development AWS account. He has been asked to *modify resources in a Production AWS account*. What is the MOST secure way to provide temporary access to the developer?
> Create a cross-account access role, and use `sts:AssumeRole` API to get short-lived credentials

> [!question]- A company needs to encrypt a large quantity of data. The data encryption keys must be generated from a *dedicated, tamper-resistant hardware device*. To deliver these requirements, which AWS service should the company use?
> **AWS CloudHSM**
> ---
> We cannot use *AWS KMS* since it *uses shared infrastructure* (multi-tenant) and is therefore not a dedicated HSM.

> [!question]- An application uses Amazon EC2, and Application Load Balancer and Amazon CloudFront to serve content. The security team have reported *malicious activity from a specific range of IP addresses*. How can a Developer prevent the application from being targeted by these addresses again?
> Add a rule to a Web ACL using **AWS WAF** that *denies the IP address ranges*.

- **Amazon Inspector** is an **automated security assessment service** that helps improve the security and compliance of applications deployed on AWS.
	- Amazon Inspector automatically *assesses applications for exposure, vulnerabilities, and deviations from best practices*.
	- **EC2 instances**.

- Rotate access keys periodically.

> [!question]- An organisation has encrypted a large quantity of data. To protect their data encryption keys they are planning to use envelope encryption. Which of the following processes is a correct implementation of envelope encryption?
> Encrypt plaintext data with a data key and then *encrypt the data key with a top-level **plaintext** master key*.
> ---
> *Envelope encryption* is the practice of *encrypting plaintext data with a data key*, and *then encrypting the data key under another key*. Some key has to be plaintext to encrypt the other key.
> 

- If you have *resources that are running inside AWS* that need programmatic access to various AWS services, then the best practice is always to use *IAM roles*. 
	- However, *applications running outside* of an AWS environment will need access keys for *programmatic access* to AWS resources. 
	- Create a separate user for such applications.

- *SNS* also for *mobile notifications*. 

- For **pagination** so that the CLI doesn't time out if there are a lot of items we use `--page-size` and `--max-items`
- *Query with eventually consistent reads consumes the least amount of RCU*.
	- As compared to any scan operation or strongly consistent reads combination.

> [!caution]- **A subnet can only be associated with one route table at a time.**
> Makes sense because if it is bound to multiple route tables and there are conflicting routes it won't know which route to choose.

- The **IAM policy simulator** *evaluates the policies* that you choose and *determines the effective permissions* for each of the actions that you specify. 
	- The simulator uses the same policy evaluation engine that is used during real requests to AWS services.

- We can use **CloudFormation Parameters** with **System Manager parameters (SSM)**.
	- CloudFormation will fetch values stored against these keys in Systems Manager in your account and use them for the current stack operation.

- Implementing a *DAX cluster* is *expensive* and CANNOT be one of the solutions if your table is getting throttled and you want to do something in the least expensive way. 

> [!note]- In envelope encryption we can even encrypt the data encryption key under another encryption key, and encrypt that encryption key under another encryption key. But, eventually, one key must remain in plaintext so you can decrypt the keys and your data. This top-level plaintext key encryption key is known as the _master key_.
> ![[attachments/Pasted image 20230218085014.png]]

- **Delete root user access keys** as a recommended security measure.
- You need a **custom runtime to run C++ and Rust code in lambda**.

> [!question]- You work for a software development company where the teams are divided into distinct projects. The management wants to have separation on their AWS resources, which will have a detailed report on the costs of each project. Which of the following options is the recommended way to implement this?
> Create *separate AWS accounts* for each project and use **consolidated billing**. 

- *AWS OpsWorks*: chef and puppet.
- In a *trust policy*, the **Principal attribute** defines the **AWS services or users** that can assume the IAM role.

> [!question]- Some static assets stored in an S3 bucket need to be accessed by a user on the development account. The S3 bucket is in the production account. According to the company policy, the sharing of full credentials between accounts is prohibited.
> - Log in to the *development account* and *create a policy that will use STS to assume the IAM role in the production account*. Attach the policy to the IAM users. 
> - On the *production account*, create an lAM role and specify the *development account as a trusted entity*.
> - Set the policy that will grant access to S3 for the IAM role created in the *production account*.

- We *CANNOT* import **asymmetric** keys in KMS.

- The *modules* and your *Lambda code* must be under the **same directory level** before packaging them into a ZIP file.

- AWS recommends *changing your own passwords* and *access keys* **regularly**.

- The `context` object in a lambda function provides methods and properties that provide *information about* the *invocation*, *function*, and *execution environment*.
- **`Access-Control-Max-Age`**  how long the results of a preflight request can be cached.
- There are 2 types of authorisation in *lambda URL*: **NONE** (anyone) and **AWS_IAM** (specific IAM users).
	- We can use lambda URLs for *webhooks* without needing the use of API gateway.
- Read carefully in the question which ports are exposed by the SG. 
	- If port 22 is not exposed then we cannot use SSH.

> [!question]- A user has stored data on an encrypted EBS volume. *The volume was encrypted using the EBS default encryption process*, which creates a *AWS-managed KMS key* for the user upon encrypting the volume. Now the *user wants to share the data with another AWS account*. The AWS account needs the data to be decrypted. Which of the choices below can achieve this?
> *Copy the data to an unencrypted volume* in your account, create a unencrypted snapshot and share access to it.
> ---
> "Create a snapshot and share access to it. Then grant access to the AWS-managed KMS key used to encrypt the volume, then the user can decrypt the volume access the data directly" is incorrect since it is *not possible to share access to a volume encrypted in the default process with an AWS-managed KMS key*. The encryption would *need to be done with a custom KMS key* in order to grant access to it to another account.

- We *CANNOT share AWS managed keys cross accounts*.
- Elasticache Memcached *DOESN'T* support **encryption at rest**.
- *Social media accounts* mean Amazon *Cognito*.
- *Reserved concurrency of the lambda* should be **less** than the number of *database connections*.
- **DynamoDB Adaptive Capacity** is *available for both provisioned and on-demand capacity modes*, and provides *automatic adjustment of capacity* based on actual usage patterns to improve the scalability and cost-effectiveness of DynamoDB applications.
- Go for **SSE-KMS over SSE-S3** when the question demands **auditing** features.