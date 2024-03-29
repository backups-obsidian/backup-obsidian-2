---
created: 2022-05-14 15:43
updated: 2023-02-19 13:06
---
---
**Links**: [[102 AWS DVA Index]]

---
## CLI
### Basics
- Some AWS CLI commands (such as EC2) can become expensive if they succeed, say if we wanted to try to create an EC2 Instance.
	- Some AWS CLI commands (**not all**) contain a **`--dry-run`** option to **simulate API calls**.
- When you run API calls and they *fail*, you can get a long *error message* this error message can be *decoded using the **STS** command line*:
	- `aws sts decode-authorization-message --encoded-message actual_long_message`
	- We need to have access to **sts's (not IAMs)**  `DecodeAuthorisationMessage` API.
	- I didn't have to do it on my instance though.

> [!caution] If no region is specified in `aws configure` then the default region is **us-east-1**.

> [!note] AWS CLI requires Python as its runtime. Boto3.

### CLI Profile
- This is something that will not be asked in the exam but you should know as a developer.
- When you run `aws configure` and don't specify any profile it stores your credentials under the default profile.
- A `~/.aws` directory is created which contains 2 files `credentials` and `config`.
	- ![[attachments/Pasted image 20220514161317.png]]
- You can save credentials of multiple AWS accounts using profiles
	- `aws configure --profile my-other-aws-account`
	- ![[attachments/Pasted image 20220514161423.png]]
- Try to keep profile names short
- If you want to execute commands against any non default profile then we have to explicitly specify it.
	- Eg: `aws s3 ls --profile my-other-aws-account`

> [!tip] If you don't specify any profile then the default profile is used.

## Credentials
### Credentials Provider Chain
- *command line options* → *environment variables* → *CLI credentials file* → *CLI config file* → *container credentials* → *EC2 instance profile credentials* 
	- ![[attachments/Pasted image 20220514165940.png]]
	 - **`--region`**, **`--output`**, **`--profile`**
- SDK default chain
	- ![[attachments/Pasted image 20220514170020.png]]

> [!question]- An application running on a fleet of EC2 instances use the AWS SDK for Java to copy files into several AWS buckets using access keys stored in environment variables. A Developer has modified the instances to use an assumed IAM role with a more restrictive policy that allows access to only one bucket. However, *after applying the change the Developer logs into one of the instances and is still able to write to all buckets*. What is the MOST likely explanation for this situation?
> The *AWS credential provider looks for instance profile credentials last*.

> [!note] Environment variables have a higher precedence over `aws configure` credentials.

> [!question]- Question on AWS credentials scenario
> - An application deployed on an EC2 instance is *using environment variables with credentials from an lAM user to call the Amazon S3 API*. **Very bad practice**.
> - The *IAM user has S3FullAccess* permissions.
> - The application only uses one S3 bucket, so according to best practices: An IAM Role & EC2 Instance Profile was created for the EC2 instance
> - The Role was assigned the minimum permissions to access that one S3 bucket
> - The AM Instance Profile was assigned to the EC2 instance, but it still had access to all S3 buckets. Why?
>
> The *credentials chain is still giving priorities to the environment variables*.

### Credentials Best Practices
> [!caution] NEVER EVER STORE AWS CREDENTIALS IN YOUR CODE

- Best practice is for *credentials to be inherited from the credentials chain*
- If using working within AWS, use IAM Roles
	-  EC2 Instances Roles for EC2 Instances
	-  ECS Roles for ECS tasks
	-  Lambda Roles for Lambda functions
- If working *outside of AWS*, use *environment variables* / *named profiles*

> [!question]- You're running an application on an on-premises server. The application needs to perform API calls to an S3 bucket. How can you achieve this in the most secure manner?
> - *Create an IAM user to be used by the application*, then generate IAM credentials and put the credentials into *environment variables*.
> - Here, it's about creating a dedicated IAM user for the application, as using your own personal IAM credentials would blur the lines between actual users and application

## Signing AWS API Requests
- When you call the **AWS HTTP API** (sending HTTP requests to an AWS service), you *sign the request* so that AWS can identify you, *using your AWS credentials* (access key & secret key)
	- If you use the **SDK or CLI**, the **HTTP requests are automatically signed** for you
- If you are *not using the SDK or CLI* then you need to use Signature v4 (**SigV4**) protocol to sign them. It is extremely complicated.

> [!tip]- At a high level remember that *SigV4* is for *signing your requests to AWS*
> Any API call done to AWS must be signed with our credentials and the process is called SigV4.

- SigV4 request examples (*Header* and *Query String*)
	- ![[attachments/Pasted image 20220514171519.png]]
	- We saw the *query string SigV4* when we generated *presigned URLs in S3*.