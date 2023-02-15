---
created: 2022-05-23 16:28
updated: 2023-02-15 09:02
---
---
**Links**: [[102 AWS DVA Index]]

---
## Asynchronous Invocations
> [!note]- The main difference between asynchronous and synchronous invocations is that in *asynchronous invocations we don't get the results* we just get a **status code of 202**.
> Same status code of 202 is returned when the function is invoked asynchronously regardless if the function fails or succeeds.

- They are for services which invoke lambda function because of an **event**. 
	- *S3*
	- *Simple Notification Service* (SNS)
	- *Cloud Watch Events*/EventBridge 
	- There are more services which make use of asynchronous processing but it is not important for exam.

> [!tip]- We can invoke Lambda at regular intervals using CloudWatch Events. Completely serverless.

> [!note]- The above services can invoke the lambda because of lambda's **resource based policy**.
> ![[attachments/Pasted image 20220523165610.png]] 
> This says S3 can invoke our function

- The events are placed in an **Event Queue**
- Lambda attempts to retry on errors
	- **Max 3 tries** 
	- *Minute wait after 1st* and *then 2 minutes wait*
- Make sure the processing is **idempotent**. That is *in case of retries the result should be the same*.
- If the function is retried, you will see *duplicate logs entries in CloudWatch Logs*
- Can **define a DLQ** (dead-letter queue) **SNS or SQS** for failed processing (*need correct IAM permissions*). Or we can use [[Lambda Destinations]].
	- We will need to modify the execution role of the lambda and include SQS permission. 
	- ![[attachments/Pasted image 20220523163231.png]]

> [!question]- An application uses AWS Lambda to process many files. The Lambda function takes approximately 3 minutes to process each file and *does not return any important data*. A Developer has written a script that will invoke the function using the AWS CLI. What is the FASTEST way to process all the files?
> Invoke the Lambda function asynchronously with the **invocation type `Event`** and process the files in parallel.
> ---
> **`RequestResponse`** is used for synchronous invocations

### CloudWatch with Lambda
- Two ways
	- *Serverless cron/rate*
		- A **resource based policy** is created so that CloudWatch can invoke lambda.
	- *CodePipeline EventBride rule* to invoke a lambda on state changes
