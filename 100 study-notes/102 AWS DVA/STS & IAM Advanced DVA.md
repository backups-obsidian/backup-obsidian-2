---
created: 2022-05-30 09:35
updated: 2023-02-09 14:25
---
---
**Links**: [[102 AWS DVA Index]]

---
## STS
- Allows to grant *limited and temporary access to AWS resources* (up to 1 hour).
- **AssumeRole**: *Assume roles* within *your account or cross account*
- AssumeRoleWithSAML: return credentials for users logged with SAML
- AssumeRoleWithWebldentity: 
	- Return credentials for users logged with an IdP (Facebook Login, Google Login, OIDC compatible)
	- AWS recommends against using this, and using Cognito Identity Pools instead
- **GetSessionToken**: *for MFA*, from a user or AWS account root user
- GetFederationToken: obtain temporary credentials for a federated user
- **GetCallerldentity**: return details about the IAM user or role used in the API call
- **DecodeAuthorizationMessage**: *decode error message* when an AWS API is denied

### Using STS to assume a role
- Define an IAM Role within your account or cross-account
- Define *which principals can access this lAM Role*
- Use AWS STS (Security Token Service) to retrieve credentials and impersonate the IAM Role you have access to (**AssumeRole** API)
	- ![[attachments/Pasted image 20220531090004.png]]
- Temporary credentials can be valid between 15 minutes to 1 hour

### STS with MFA
- Use **GetSessionToken** from STS 
- Appropriate IAM policy using IAM Conditions 
- `aws:MultiFactorAuthPresent:true`
	- ![[attachments/Pasted image 20220531090217.png]]
- Reminder, GetSession Token returns the following which must be passed while making an API call
	- Access ID
	- Secret Key
	- Session Token
	- Expiration date

## IAM Advanced
### IAM Policy Variables:
- Instead of creating individual policies for each user, you can **use policy variables and create a single policy that applies to multiple users** (a group policy). 
	- Policy variables act as *placeholders*. 
	- When you make a request to AWS, the placeholder is replaced by a value from the request when the policy is evaluated.
- As an example, the following policy gives each of the users in the group full programmatic access to a user-specific object (their own "home directory") in Amazon S3.
	- ![[attachments/Pasted image 20230209142405.png]]

### IAM Policies & S3 Bucket Policies
- IAM Policies are attached to users, roles, groups
- S3 Bucket Policies are attached to buckets
- When evaluating if an IAM Principal can perform an operation X on a bucket, the **union** of its assigned lAM Policies and S3 Bucket Policies will be evaluated.
	- ![[attachments/Pasted image 20220531090533.png]]

- Example 1:
	- IAM Role attached to EC2 instance, authorises RW to my bucket
	- No S3 Bucket Policy attached
	- *EC2 instance can read and write to my_bucket*
- Example 2:
	- IAM Role attached to EC2 instance, authorises RW to my bucket
	- S3 Bucket Policy attached, *explicit deny* to the IAM Role
	- EC2 instance *cannot read and write to my_bucket*
- Example 3:
	- IAM Role attached to EC2 instance, *no S3 bucket permissions*
	- S3 Bucket Policy attached, *explicit RW allow to the lAM Role*
	- EC2 instance *can read and write to my_bucket*
- Example 4:
	- IAM Role attached to EC2 instance, *explicit deny* S3 bucket permissions
	- S3 Bucket Policy attached, *explicit RW allow* to the lAM Role
	- EC2 instance *cannot read and write to my_bucket*

### Dynamic Policies
- How do you assign each user a `/home/<user>` folder in an S3 bucket?
	- Option 1:
		- Create an IAM policy allowing Georges to have access to `/home/georges`
		- Create an AM policy allowing Sarah to have access to `/home/sarah`
		- Create an lAM policy allowing Matt to have access to `/home/matt`
		- One policy per user! *This doesn't scale*
	- Option 2:
		- **Create one dynamic policy** with IAM
		- Leverage the special **policy variable** `${aws:username}`
		- ![[attachments/Pasted image 20220531091246.png]]

### Inline vs Managed Policies
- AWS Managed Policy
	- Maintained by AWS
	- Good for power users and administrators
	- Updated in case of new services / new APIs
- Customer Managed Policy
	- **Best Practice**, *re-usable*, can be applied to many principals
	- *Version Controlled + rollback*, central change management
- Inline
	- Strict one-to-one relationship between policy and principal
	- **Policy is deleted if you delete the lAM principal**
	- You may also get **limit exceeded error** (2 Kb) and cannot specify a lot of permissions

### Passing Roles
- To configure many AWS services, you must *pass an lAM role to the service* (this happens only once during setup)
- The service will later assume the role and perform actions
- Example of passing a role:
	- To an EC2 instance
	- To a Lambda function
	- To an ECS task
	- To CodePipeline to allow it to invoke other services
- For this, you need the lAM permission `iam:PassRole`
	- ![[attachments/Pasted image 20220531092059.png]]
- It often comes with `iam:GetRole` to view the role being passed
- Example:
	- ![[attachments/Pasted image 20220531093636.png]]

#### Can a role be passed to any service?
- **No: Roles can only be passed to what their trust allows**
- A *trust policy for the role that allows the service to assume the role*
	- ![[attachments/Pasted image 20220531092450.png]]

