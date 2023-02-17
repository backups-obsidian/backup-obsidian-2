---
created: 2022-05-24 10:24
updated: 2023-02-17 12:49
---
---
**Links**: [[102 AWS DVA Index]]

---
## Environment Variables
- Environment variable = *key/value pair* in **String form**
- Adjust the function behaviour without updating code
- The environment variables are available to your code
- Lambda Service adds its own system environment variables as well
- Helpful to store **secrets** (*encrypted by KMS*)
	- Secrets can be encrypted by the *Lambda service key, or your own CMK*

> [!note]- If you want to *share encrypted secrets* among lambdas then use **SecureString from SSM parameter store**.
> We CANNOT use *KMS encrypted environment variables* since even though the credentials will be encrypted, *these environment variables will only be used by an individual Lambda function*, and **cannot be shared**.

> [!note]- Lambda environment variables can have a **maximum size of 4 KB**.
> Additionally, the direct *Encrypt API of KMS also has an upper limit of 4 KB* for the data payload. 
> To *encrypt 1 MB*, you need to *use the Encryption SDK and pack the encrypted file with the lambda function*.

## Logging & Tracing
- **CloudWatch Logs**:
	- AWS *Lambda execution logs are stored in AWS CloudWatch Logs*
	- Make sure your AWS Lambda function has an *execution role with an IAM policy that authorises writes to Cloud Watch Logs*.
		- It can take 5-10 minutes for logs to show up after a function invocation.
		- If you do not see the logs after this period then it means your lambda is missing execution role.

- **CloudWatch Metrics**: 
	- AWS Lambda *metrics* are displayed in AWS Cloud Watch Metrics
	- *Invocations*, Durations, *Concurrent Executions*
	- *Error count*, *Success Rates*, *Throttles*
	- *Async Delivery Failures*
	- Iterator Age (Kinesis & DynamoDB Streams)

## Tracing
- Enable in Lambda configuration (*Active Tracing*)
- *Runs the X-Ray daemon* for you
- Use AWS **X-Ray SDK in Code**
- Ensure *Lambda Function has a correct IAM Execution Role*
	- The managed policy is called `AWSXRayDaemonWriteAccess`
- *Environment variables* used by lambda to **communicate with X-Ray**
	- `_X_AMZN_TRACE_ID`: contains the *tracing header*
	- `AWS_XRAY_CONTEXT_MISSING`: by default, *LOG_ERROR*
	- `AWS_XRAY_DAEMON_ADDRESS`: the X-Ray Daemon *`IP_ADDRESS:PORT`*

## Lambda in VPC
### Default VPC
- By **default**, your Lambda function is launched **outside your own VPC** (in an *AWS-owned VPC*)
- Therefore it **cannot access resources in your VPC** (RDS, ElastiCache, internal ELB, etc)
	- ![[attachments/Pasted image 20220524104326.png]]

### Our VPC
- To *deploy lambda in our VPC*:
	- You must define the *VPC ID*, the *Subnets* and the *Security Groups*
	- **Lambda will create an ENI** (Elastic Network Interface) in your subnets
	- `AWSLambdaVPCAccessExecutionRole` needs to be assigned to the lambda function.
	- ![[attachments/Pasted image 20220524110228.png]]
- Example: 
	- ![[attachments/Pasted image 20220524104601.png]]
	- In the above example *RDS security group must allow access from the security group of Lambda*.

#### Internet Access
- A **Lambda function in our VPC does not have internet access**

> [!caution]- Deploying a Lambda function in a *public subnet* (which has internet access)  *does not give it internet access or a public IP*
> Instead in **needs to be deployed in a private subnet and use NAT Gateway in public subnet for internet access**.
> ![[attachments/Pasted image 20220524105942.png]]

- Deploying a Lambda function in a *private subnet* gives it internet access **if you have a NAT Gateway/Instance**.
	- You can use *VPC endpoints to privately access AWS services* without a NAT. Like DynamoDB
	- ![[attachments/Pasted image 20220524105007.png]]

> [!note]- Lambda *CloudWatch Logs works even without endpoint or NAT Gateway*
- Also read [[../101 AWS SAA/Lambda#Using our own VPC|using our own vpc]].

## External Dependencies
- If your *Lambda function depends on external libraries*, for example AWS X-Ray SDK, Database Clients, etc
- You need to **install the packages alongside your code** and **zip it together**
	- For `Node.js`, use `npm` & *node_modules* directory
	- For `Python`, use `pip --target` options
	- For `Java`, *include the relevant jar files*
- Upload the **zip straight to Lambda if less than 50MB**, *else to S3 first*
- *Native libraries* work: they *need to be compiled on Amazon Linux*

> [!note]- AWS *SDK comes by default* with every Lambda function

> [!question]- Your Lambda function must use the Node.js drivers to connect to your RDS PostgreSQL database in your VPC. How do you bundle your Lambda function to add the dependencies?
> Put the function and the dependencies in one folder and **zip them together**.

## Lambda with CloudFormation
- **Inline**: 
	- Code is present in the CloudFormation template
	- For very simple code
	- *Cannot include dependencies*
	- Done using `Code.ZipFile` property
- **Through S3**:
	- You must *store the Lambda zip in S3*
	- You must *refer the S3 zip location* in the CloudFormation code
		- **S3Bucket**
		- **S3Key**: full path to zip
		- **S3Object Version**: if versioned bucket
		- ![[attachments/Pasted image 20220524155146.png]]

> [!caution]- If you update the code in S3, but don't update *S3Bucket*, *S3Key* or *S3ObjectVersion*, CloudFormation won't update your function
> In general you would only have to update the *S3ObjectVersion* if you are uploading the zip with the same file name to the same bucket.

### Multiple Accounts
- We need an *execution role* and a *bucket policy*
	- ![[attachments/Pasted image 20220524154655.png]]

## Lambda Layers
- **Custom Runtimes**
	- For example C++ and Rust
- **Externalise dependencies** to re-use them:
	- It helps in *decreasing the upload time*.
	- When we upload code we are *not changing code of the libraries* used by our function. 
	- Lambda layers helps us in *reusing the library dependency layer* removing the need to upload them again and again when only our application source code changes.
	- We can use AWS lambda layers our create our custom lambda layers. 
	- ![[attachments/Pasted image 20220524155737.png]]

## Lambda Container Images
- Deploy *Lambda function as container images* of up to **10GB** from ECR
- *Pack complex dependencies*, large dependencies in a container
- Base images are available for Python, Node.js, Java, NET, Go, Ruby
- Can *create your own image/custom runtime* as long as *base image* implements the **Lambda Runtime API**
	- ![[attachments/Pasted image 20220524160249.png]]
- We can build our own images from *base images provided by AWS*
	- ![[attachments/Pasted image 20220524160426.png]]
- Test the containers locally using the Lambda Runtime Interface Emulator
- Unified workflow to build apps
- You must **create the Lambda function from the same account as the container registry in Amazon ECR** to ensure there are no permission issues.

## Lambda Version & Aliases
### Versions
- When you work on a Lambda function, we work on `$LATEST`
- When we're *ready to publish a Lambda function*, we **create a version**
- **Versions are IMMUTABLE**
	- **Version = code + configuration** (*nothing can be changed* - immutable)
- Immutable means you *cannot the change the code* or *env variables*.
- Versions have increasing version numbers
- Versions *get their own ARN* (Amazon Resource Name)
- *Each version of the lambda function can be accessed*

### Aliases
- Aliases are **pointers to Lambda function versions**
- We can define a *dev*, *test*, *prod* aliases and *have them point at different lambda versions*
- **Aliases are mutable**
- Aliases enable **Blue/Green deployment** by **assigning weights** to lambda functions
	- ![[attachments/Pasted image 20220524161029.png]]
- Aliases enable *stable configuration of our event triggers/destinations*
- Aliases *have their own ARNs*
	- So this means with aliases we can have a *static ARNs which always points to the latest version*.

> [!caution] Aliases **cannot reference aliases**

## Lambda & CodeDeploy
- CodeDeploy can help you **automate traffic shift for Lambda aliases**
	- ![[attachments/Pasted image 20220524161935.png]]
- Feature is *integrated within the SAM framework*
- **Linear**: *grow traffic every N minutes until 100%*. There can be *n steps*.
	- `Linear10PercentEvery3Minutes`
	- `Linear10PercentEvery10Minutes`
- **Canary**: *try X percent then 100%*. There are **only 2 steps**.  
	- Traffic should shift from the previous Lambda function to the new version in the *shortest time possible*, but you *still don't want to shift traffic all-at-once immediately*.
	- `Canary10Percent5Minutes`
	- `Canary10Percent30Minutes`
- **AllAtOnce**: *immediate and risky*
- Can create **Pre & Post Traffic hooks** to check the health of the Lambda function. If anything goes wrong then CodeDeploy can perform a roll back.

## Lambda Errors
- `InvalidParameterValueException`: This will be returned if *one of the parameters* in the request is *invalid*.
	- Example if _you provided an IAM role in the_ `CreateFunction` _API which AWS Lambda is unable to assume_.
- `CodeStorageExceededException`: If you have *exceeded your maximum total code size* per account.
- `ResourceConflictException`: If the *resource already exists*.
- `ServiceException`: If the lambda service encountered an *internal error*.

## Lambda Invocations
- In the `Invoke` API, you have 3 options to choose from for the **InvocationType:**
	- `RequestResponse` (**default**) - Invoke the function **synchronously**. 
		- Keep the *connection open until the function returns a response or times out*.
		- The API response includes the function response and additional data.
	- `Event` - Invoke the function **asynchronously**. 
		- Send events that fail multiple times to the function's dead-letter queue (if it's configured). 
		- The *API response only includes a status code*.
	- `DryRun` - Validate parameter values and verify that the user or role has permission to invoke the function.

## Lambda Concurrency
- If you set the concurrent execution limit for a function, *the value is deducted from the unreserved concurrency pool*. 
	- For example, if your *account's concurrent execution limit is 1000* and you have 10 functions, you can specify a limit on one function at 200 and another function at 100. The remaining 700 will be shared among the other 8 functions.

> [!note]- AWS Lambda will keep the **unreserved concurrency pool at a minimum of 100 concurrent executions**, so that functions that do not have specific limits set can still process requests. So, in practice, *if your total account limit is 1000, you are limited to allocating 900* to individual functions.
> ![[attachments/Pasted image 20230217124541.png]]