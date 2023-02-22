---
created: 2023-02-06 22:18
updated: 2023-02-20 14:52
---
---
**Links**: [[102 AWS DVA Index]]

---
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
- Scalable solution to make the application tier stateless and outsource the session information - ElastiCache
- *Long-lived TCP connections* between clients and instances cause *uneven traffic load distribution by design*.
- **S3 Object Ownership** is an Amazon S3 bucket setting that you can use to control *ownership of new objects that are uploaded to your buckets*. 
	- **By default**, when other AWS accounts upload objects to your bucket, the objects remain owned by the **uploading account**. 
	- With **S3 Object Ownership**, any new objects that are written by other accounts with the `bucket-owner-full-control` canned access control list (ACL) *automatically become owned by the bucket owner*, who then has full control of the objects.
	- S3 Object Ownership has *two settings*: 
		- **Object writer** – The **uploading account** will own the object. 
		- **Bucket owner preferred** – The **bucket owner** will own the object if the object is uploaded with the `bucket-owner-full-control` canned ACL. 
		- *Without this setting and canned ACL, the object is uploaded and remains owned by the uploading account*.
- We can use SSM parameter store if we don't want to redeploy the application and just to change some environment variables.
- Use **CloudWatch Events + Lambda** for running **periodic jobs**.

> [!note]- **Adding read replicas will NOT help in reducing latency** when compared to a caching solution.

- *Lambda*, *IAM* and *Cognito* services are all available as HTTP APIs in API Gateway.
	- AWS Web Application Firewall (AWS WAF) however, is only available in REST APIs.

- For RDS **automated backups** are limited to a **single AWS Region** while **manual snapshots** and **Read Replicas** are supported across **multiple Regions**.

- Customers may use four mechanisms for controlling access to Amazon S3 resources:
	- IAM policies
	- Bucket policies
	- ACLs
	- **Query string authentication (presigning URLS)**

- DynamoDB can also be used for handling sessions *but the latency will not be as low as ElastiCache*.

> [!question]- A company uses an Amazon S3 bucket to store a large number of sensitive files relating to eCommerce transactions. *The company has a policy that states that all data written to the S3 bucket must be encrypted*. How can a Developer ensure compliance with this policy?
> Create an S3 *bucket policy* that *denies any S3 Put request* that does not include the `x-amz-server-side-encryption`.

- We can customise the Amazon Cognito hosted web Ul and add the company logo.
- Go for customer managed AWS policies over AWS managed AWS policies for more security and fine grained control.

> [!question]- A web application has been deployed on AWS. A developer is concerned about exposure to common exploits that could affect application availability or compromise security. Which AWS service can protect from these threats?
> AWS WAF.

- Even if you are doing an upsert using indirectly using `dynamodb:UpdateItem` you need `dynamodb:PutItem` permission.
- API Gateway resource policies:
	- *API Gateway supports resource policies*, which are used to *control access to API Gateway resources such as REST APIs, WebSocket APIs,* and resources within those APIs such as methods, stages, and deployment. 
	- Resource policies are similar to IAM policies in that they are JSON documents that specify who can access the resource and what actions they can perform.
	- Resource policies can be used to *provide fine-grained control over access to API Gateway resources*, and they can be used in conjunction with IAM roles and policies to provide comprehensive access control. 
	- For example, you can use resource policies to:
		- *Restrict access to specific API methods based on the IP address* or the AWS account of the caller.
		- Grant read-only access to a specific stage of an API to a particular IAM user or role.
		- **Allow anonymous access to certain API methods while requiring authentication for others**.
	- Resource policies are typically *used in scenarios where you need to provide access to API Gateway resources to **external entities** that don't have an IAM role or user*. 
		- For example, you might use a resource policy to allow a third-party API gateway to access your API, or to *allow access from a partner organisation that doesn't have an AWS account*.
- **Canary**: Traffic is shifted in two increments.
	- Use when traffic has to be shifted quickly.
- **Linear**: Traffic is shifted in equal increments with an equal number of minutes between each increment.
- *Create standalone policies* instead of using inline policies.
- Create an **AWS CloudTrail trail and apply it to all regions**, configure logging to a single S3 bucket

> [!question]- A team of Developers require access to an AWS account that is a member account in AWS Organisations. The administrator of the master account needs to restrict the AWS services, resources, and API actions that can be accessed by the users in the account. What should the administrator create?
> A **service control policy**.
> ---
> You can configure the SCPs in your organisation to work as either of the following:
> A **deny list** – actions are allowed by default, and you specify what services and actions are prohibited
> An **allow list** – actions are prohibited by default, and you specify what services and actions are allowed

- In the scenario the Lambda function will be invoked 40 times per second and run for 100 seconds. Therefore, there can be up to 4,000 executions running concurrently which is above the default per-region limit of 1,000 concurrent executions.
	- This can be easily rectified by contacting AWS support and requesting the concurrent execution limit to be increased.
- If your access keys have been compromised delete them.

> [!question]- An application is running on a fleet of EC2 instances running behind an Elastic Load Balancer (ELB). The EC2 instances session data in a shared Amazon S3 bucket. Security policy mandates that *data must be encrypted in transit*. How can the Developer ensure that all data that is sent to the S3 bucket is encrypted in transit?
> Create an *S3 bucket policy* that denies traffic where **SecureTransport is false**.

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

- **AWS Cloud9** is an integrated development environment, or **IDE** on the **web**.
- Rotate access keys periodically.

> [!question]- An organisation has encrypted a large quantity of data. To protect their data encryption keys they are planning to use envelope encryption. Which of the following processes is a correct implementation of envelope encryption?
> Encrypt plaintext data with a data key and then *encrypt the data key with a top-level **plaintext** master key*.
> ---
> *Envelope encryption* is the practice of *encrypting plaintext data with a data key*, and *then encrypting the data key under another key*.
> 
- If you have *resources that are running inside AWS* that need programmatic access to various AWS services, then the best practice is always to use *IAM roles*. 
	- However, *applications running outside* of an AWS environment will need access keys for *programmatic access* to AWS resources.
- *SNS* also for *mobile notifications*. 
- Stage variables are a API Gateway concept.
- *Memcached* **multithreaded** event-based key/value cache store.
	- Multiple cores.
- Lambda @edge is used for low latency 

- For **pagination** so that the CLI doesn't time out if there are a lot of items we use `--page-size` and `--max-items`
- *Query with eventually consistent reads consumes the least amount of RCU*.
	- As compared to any scan operation or strongly consistent reads combination.
- In *EBS and Lambda* you can deploy either a **jar or a zip** file.
- **A subnet can only be associated with one route table at a time.**
- The **IAM policy simulator** *evaluates the policies* that you choose and *determines the effective permissions* for each of the actions that you specify. 
	- The simulator uses the same policy evaluation engine that is used during real requests to AWS services.
- We can use **CloudFormation Parameters** with **System Manager parameters (SSM)**.
	- CloudFormation will fetch values stored against these keys in Systems Manager in your account and use them for the current stack operation.
- In DynamoDB if you are getting WCU and RCU exceeded exception then you can try exponential backoffs.
- Implementing a *DAX cluster* is *expensive* and CANNOT be one of the solutions if your table is getting throttled and you want to do something in the least expensive way. 

> [!note]- Refactor after reading KMS encryption
> A developer is working on an application that will process files encrypted with a data key generated from a KMS key. The application needs to decrypt the files locally before it can proceed with the processing of the files. Which of the following are valid and secure steps in decrypting data? (Select TWO.)
> When you encrypt your data, your data is protected, but you have to protect your encryption key. One strategy is to encrypt it. **Envelope encryption** is the practice of encrypting plaintext data with a data key, and then encrypting the data key under another key.
> You can even encrypt the data encryption key under another encryption key, and encrypt that encryption key under another encryption key. But, eventually, one key must remain in plaintext so you can decrypt the keys and your data. This top-level plaintext key encryption key is known as the _master key_.
> ![[attachments/Pasted image 20230218085014.png]]

> [!question]- A developer runs a shell script that uses the AWS CLI to upload a large file to an S3 bucket, which includes an AWS KMS key. An `Access Denied` error always shows up whenever the developer uploads a file with a size of 100 GB or more. However, when he tried to upload a smaller file with the KMS key, the upload succeeds.
> The AWS CLI S3 commands perform a multipart upload when the file is large.
> The developer **does not have the `kms:Decrypt` permission**.
> ---
> To perform a multipart upload with encryption using an AWS Key Management Service (AWS KMS) customer master key (CMK), *the requester must have permission to the `kms:Decrypt` and `kms:GenerateDataKey*` actions on the key*. These permissions are required because Amazon **S3 must decrypt and read data from the encrypted file parts before it completes the multipart upload**.
> The option that says: *the developer does not have the `kms:Encrypt` permission* is incorrect because the operation is successful if the developer uploads a smaller file, which signifies that the developer already has the `kms:Encrypt` permission.

- **Delete root user access keys** as a recommended security measure.
- CloudHSM dedicated key management solution.
- You need a **custom runtime to run C++ code in lambda**.

> [!question]- You work for a software development company where the teams are divided into distinct projects. The management wants to have separation on their AWS resources, which will have a detailed report on the costs of each project. Which of the following options is the recommended way to implement this?
> Create *separate AWS accounts* for each project and use **consolidated billing**. 

- AWS WAF can be infront of API Gateway or CloudFront.
	- Can help in protecting from SQL injection attempts.

- KMS question:
	- ![[attachments/Pasted image 20230219092910.png]]
	- When you encrypt your data, your data is protected, but you have to protect your encryption key. One strategy is to encrypt it. _Envelope encryption_ is the practice of encrypting plaintext data with a data key, and then encrypting the data key under another key.
	- You can even encrypt the data encryption key under another encryption key, and encrypt that encryption key under another encryption key. But, eventually, one key must remain in plaintext so you can decrypt the keys and your data. This top-level plaintext encryption key is known as the _master key_. 

- It is recommended that you use the following pattern to **encrypt data locally in your application**:
	- Use the `GenerateDataKey` operation to get a data encryption key (DEK).
	- Use the *plaintext data key* (returned in the `Plaintext` field of the response) to *encrypt data locally*, then **erase the plaintext data key from memory**.
	- *Store the encrypted data key* (returned in the `CiphertextBlob` field of the response) *alongside the locally encrypted data*.

- *AWS OpsWorks*: chef and puppet.
- In a *trust policy*, the **Principal attribute** defines the **AWS services or users** that can assume the IAM role.

> [!question]- Some static assets stored in an S3 bucket need to be accessed by a user on the development account. The S3 bucket is in the production account. According to the company policy, the sharing of full credentials between accounts is prohibited.
> - Log in to the *development account* and *create a policy that will use STS to assume the IAM role in the production account*. Attach the policy to the IAM users. 
> - On the *production account*, create an lAM role and specify the *development account as a trusted entity*.
> - Set the policy that will grant access to S3 for the IAM role created in the *production account*.

- We *CANNOT* import **asymmetric** keys in KMS.
	- *Automatic key rotation* is ONLY for **symmetric** keys.

- The *modules* and your *Lambda code* must be under the **same directory level** before packaging them into a ZIP file.
- AWS recommends *changing your own passwords* and *access keys* **regularly**.
- We **CANT modify a launch configuration** of an ASG we have to create a new one.
	- We can *modify launch templates*.
- The `GenerateDataKey` generates a **unique symmetric data key** for client-side encryption.
- The `context` object in a lambda function provides methods and properties that provide *information about* the *invocation*, *function*, and *execution environment*.
- **Access-Control-Max-Age**  how long the results of a preflight request can be cached.
- There are 2 types of authorisation in *lambda URL*: **NONE** (anyone) and **AWS_IAM** (specific IAM users).
	- We can use lambda URLs for *webhooks* without needing the use of API gateway.
- Read carefully in the question which ports are exposed by the SG. 
	- If port 22 is not exposed then we cannot use SSH.

> [!question]- A user has stored data on an encrypted EBS volume. *The volume was encrypted using the EBS default encryption process*, which creates a *AWS-managed KMS key* for the user upon encrypting the volume. Now the *user wants to share the data with another AWS account*. The AWS account needs the data to be decrypted. Which of the choices below can achieve this?
> *Copy the data to an unencrypted volume* in your account, create a unencrypted snapshot and share access to it.
> ---
> "Create a snapshot and share access to it. Then grant access to the AWS-managed KMS key used to encrypt the volume, then the user can decrypt the volume access the data directly" is incorrect since it is *not possible to share access to a volume encrypted in the default process with an AWS-managed KMS key*. The encryption would *need to be done with a custom KMS key* in order to grant access to it to another account.

- We cannot share AWS managed keys cross accounts.

### KMS Notes:
- For files greater than 4KB we cannot use the CMK to encrypt it. We have to use a data key.
- We can *generate an encrypted and an unencrypted data key from a CMK*.
- We use the unencrypted data key (**plaintext**) to encrypt the data and  store the encrypted data key in the database along side the encrypted data.
	- ![[attachments/Pasted image 20230220104225.png]]
- To decrypt the data key and then use the CMK and then use the decrypted data key to decrypt the data.
	- ![[attachments/Pasted image 20230220104251.png]]
- CMK can be thought of as a key encrypting key and not a data encrypting key. 

- Steps in code:
	- We first generate a CMK and the use the CMK to generate a datakey.
	- Now when we generate a datakey we get two types of responses. 
		- We get a **plaintext version** and a **Ciphertextblob version**.
		- We store the Ciphertextblob version in the database and *delete the plaintext version after we are done encrypting*. 

- CMK (Customer master key) can be *AWS managed* (aws generated) or *Customer managed* (customer created).
