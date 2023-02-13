---
created: 2022-04-19 16:22
updated: 2023-02-13 16:06
---
---
**Links**: [[101 AWS SAA Index]]
**Recommended Reads**: [[../102 AWS DVA/102 AWS DVA Index#Lambda|Lambda DVA]]

---
## Lambda
- They *run on demand*.
- You are only going to be *billed for the time your function is running*.
- **Scaling is automated** and quick.
	- Since Lambda functions can scale extremely quickly, its a good idea to *deploy a CloudWatch Alarm* that notifies your team when function *metrics such as ConcurrentExecutions or Invocations* exceeds the expected threshold.

- **Increasing the RAM will improve the CPU and network →** Lambda allocates compute power in proportion to the memory you allocate to your function. 
	- This means *you can **over-provision memory** to run your functions faster and potentially reduce your costs (since the functions are billed for the time which they are run)*. 
	- However, *AWS recommends that you should not over provision your function time out settings*. Always understand your code performance and set a function time out accordingly. *Over provisioning function timeout often results in Lambda functions running longer than expected and unexpected costs*.
- It has extensive support for different languages. Even if a language is not supported by AWS there is *Custom Runtime API which is community supported*.
- You can run **containers on lambda** but it **must** implement **Lambda Runtime API**. ECS/fargate are preferred for running arbitrary docker images.
- Lambda seamlessly integrates with a lot of AWS services. It has a *direct integration with S3*.
- If you intend to *reuse code* in more than one Lambda function, you should consider creating a **Lambda Layer** for the reusable code.

- *Some use cases* of lambda: thumbnail creation when an image is uploaded to S3, *serverless cron jobs* using CloudWatch events. If you would have used EC2 instances then you will have to run the instance 24x7 and pay for the time when the CRON job is not running

> [!note] Lambda and in general any serverless application is best for intermittent and *bursty load*.

> [!note] You can package and deploy lambda functions as *container images*.

### Using our own VPC
> [!caution]+ By **default**, Lambda functions always **operate from an AWS owned VPC** and hence *have access to any public internet address or public AWS APIs*. Once a *Lambda function is VPC enabled*, it will need a *route through a NAT gateway in a public subnet* to access public resources.
> - Lambda function *scales automatically* based on the number of events it processes. 
> - If your *Lambda function accesses a VPC*, *you must make sure that your **VPC has sufficient ENI capacity** to support the scale requirements of your Lambda function*. 
> - It is also recommended that you *specify at least one subnet in each Availability Zone in your Lambda function configuration*.
> - By specifying subnets in each of the Availability Zones, your Lambda function can run in another Availability Zone if one goes down or runs out of IP addresses. 
> - If your *VPC does not have sufficient ENIs or subnet IPs, your Lambda function will not scale* as requests increase. 
> - You will see an *increase in invocation errors with EC2 error types* like `EC2ThrottledException` . 

- To enable your Lambda function to *access resources inside your private VPC*, you must provide additional *VPC-specific configuration* information that includes **VPC subnet IDs** and **security group IDs**. AWS Lambda uses this information *to set up elastic network interfaces (ENIs)* that enable your function.
	- ![[attachments/Pasted image 20220426205040.png]]

- For more clarity read [[../102 AWS DVA/Lambda Configuration & Deployments#Lambda in VPC|Lambda in VPC]]

### Pricing
- It is **very cheap**.
- **Easy pricing**: 
	- *pay per request* (no of requests): First *1,000,000 requests are free* then $0.20 per million requests thereafter ($0.0000002 per request) 
	- *compute time* (duration): 
		- *400,000 GB-seconds* of compute time *per month* if *FREE*
		- *400,000 seconds if function is 1 GB RAM 3,200,000 seconds if function is 128 MB RAM*
		- After that $1.00 for 600,000 GB-seconds

### Limits
- The memory limit is from **128MB** to **10GB** with **increments** of **1MB**. 
- **Maximum** execution time is **15 minutes**. Not suitable for long running tasks.
- **Environment Variables** limit is **4KB**
	- We can use env variables for *storing database connection strings*.
- We can have **1000 concurrent executions (lambdas)** *per AWS account in a region*. This can be increased on request.
- We can have **disk capacity of 512 MB** in */tmp* in the function container.
- Size of the compressed zip should be *50 MB* and uncompressed (code + dependencies) zip should be *250 MB*.

> [!caution] A bigger deployment package has *no effect* on your cold start.

### @Edge
- You deploy **Lambda functions alongside your CloudFront CDN.**
- This can be useful in **building more responsive applications** or **customise the CDN content**.

> [!important]+ We can use lambda to *change CloudFront requests and responses*. They are of *4 types*.
> - After CloudFront receives a request from a viewer (*viewer request*)
> - Before CloudFront forwards the request to the origin (*origin request*)
> - After CloudFront receives the response from the origin (*origin response*)
> - Before CloudFront forwards the response to the viewer (*viewer response*)
> ---
> ![[attachments/Pasted image 20220426202134.png]]

- We can *generate responses to viewers without ever sending the request to the origin*. This can be done by using the *Viewer Request* and *Viewer Response* lambda functions.
	- Global application
	- ![[attachments/Pasted image 20220426202424.png]]

- Why use lambda@edge
	- *Website Security and Privacy*
	- Dynamic Web Application at the Edge
	- Search Engine Optimisation (*SEO*)
	- Bot Mitigation at the Edge
	- A/B Testing
	- User *Authentication and Authorisation*
	- User *Tracking and Analytics*

> [!note] Keywords: *process the data in close geographical proximity to their users*

### Encryption
- When you create or update Lambda functions that use *environment variables*, *AWS Lambda encrypts them* using the AWS *Key Management Service*. 
- When your Lambda function is invoked, those values are decrypted and made available to the Lambda code.
- The first time you create or update Lambda functions that use environment variables in a region, a *default service key is created for you automatically within AWS KMS*. This key is used to encrypt environment variables. 
- For more control (like audit and rotation) create keys in KMS and then use them. This is done using the **encryption helpers**.

> [!question]- Why use encryption helpers?
> Although Lambda encrypts the environment variables in your function by default, the *sensitive information would still be visible to other users who have access to the Lambda console*. This is because Lambda uses a default KMS key to encrypt the variables, which is usually accessible by other users. The *best option in this scenario is to use encryption helpers to secure your environment variables*.
>
> Env variables must be decrypted in the application, also the lambda must have permission to use KMS *Decrypt API*.