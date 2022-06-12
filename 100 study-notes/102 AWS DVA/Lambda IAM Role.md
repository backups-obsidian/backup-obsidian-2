---
created: 2022-05-24 09:54
updated: 2022-06-09 15:34
---
---
**Links**: [[102 AWS DVA Index]]

---
- An IAM role also known as **execution role** must be attached to lambda function to give it permissions to access other AWS resources.
- Sample managed policies for Lambda:
	- **AWSLambdaBasicExecutionRole**:  *Upload logs to CloudWatch*.
	- AWSLambdaKinesisExecutionRole: Read from Kinesis
	- AWSLambdaDynamoDBExecutionRole: Read from DynamoDB Streams
	- *AWSLambdaSQSQueueExecutionRole*: *Read from SQS*
	- AWSLambdaVPCAccessExecutionRole: Deploy Lambda function in VPC
	- AWSXRayDaemonWriteAccess: Upload trace data to X-Ray.

- When you use an **event source mapping** to invoke your function, Lambda uses the **execution role to read event data**.
	- In asynchronous and synchronous invocations lambda was invoked by other services so we didn't need IAM role with specific permissions.
	- In event source mapping *lambda is the one invoking SQS queue or kinesis* hence it needs an execution policy to read event data.

> [!note] Best practice: create *one lambda execution role per function*

- When our **lambda is invoked by other resources** then we use **resource based policies**.
	- This gives other *AWS services* or *AWS accounts* to use our lambda resources.
	- Similar to S3 bucket policies

- An *IAM principal can access Lambda*:
	- If the *lAM policy* attached to the principal authorises it (e.g. *user access*)
	- OR if the *resource-based policy* authorises (e.g. *service access*)

> [!important]- When an AWS service like *S3 calls your Lambda function* (asynchronous invocation), then the *resource-based policy gives it access*.
> If our lambda is performing some kind of operation on S3 like list buckets then its execution role should have S3 list bucket policy.

> [!note]+ Cross account access
> The development team at a retail organisation wants to allow a *Lambda function in its AWS Account A to access a DynamoDB table in another AWS Account B*.
> - Create an IAM role in Account B with access to DynamoDB. 
> - *Modify the trust policy of the role in Account B to allow the execution role of Lambda to assume this role*. 
> - Update the Lambda function code to add the AssumeRole API call