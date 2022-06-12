---
created: 2022-04-17 15:43
updated: 2022-06-09 12:20
---
---
**Links**: [[../100 home | 100 Home]]
**Recommended Reads**: [[../101 AWS SAA/101 AWS SAA Index | AWS SAA Index]]

---
## Databases
[[ElastiCache DVA]]
[[ElasticCache Replication]]

## Extra
[[CLI]]
[[AWS Limits]]
[[CloudFront DVA]]
[[ECS DVA]]

## ElasticBeanstalk
[[ElasticBeanstalk DVA]]
[[ElasticBeanstalk Deployment Modes]]
[[ElasticBeanstalk Docker]]

## CI/CD
- Tech Stack
	- ![[attachments/Pasted image 20220516161339.png]]

[[CodeCommit]]
[[CodePipeline]]
[[CodeBuild]]
[[CodeDeploy]]
[[CodeStar]]
[[CodeArtifact]]
[[CodeGuru]]

## CloudFormation
[[CloudFormation]]
[[CloudFormation Template Components]]
[[CloudFormation Intrinsic Functions]]
[[CloudFormation RollBacks]]
[[CloudFormation Advanced]]

### Miscellaneous
- Services that use CloudFormation
	- *ElasticBeanStalk* 
	- *SAM*
	- *CodePipeline*

## Monitoring, Troubleshooting & Auditing
- AWS CloudWatch offer us: 
	- *Metrics*: Collect and track key metrics
	- *Logs*: Collect, monitor, analyse and store log files
	- *Events*: Send notifications when certain events happen in your AWS. Recommended to use EventBridge.
	- *Alarms*: React in real-time to metrics/events

[[../101 AWS SAA/CloudWatch | CloudWatch]]
[[../101 AWS SAA/EventBridge | EventBridge]]
[[X-Ray]]
[[X-Ray Advanced]]
[[../101 AWS SAA/CloudTrail | CloudTrail]]

## Messaging: SQS, SNS & Kinesis
[[../101 AWS SAA/SQS | SQS]]
[[SQS DVA]]
[[../101 AWS SAA/SNS | SNS]]
[[../101 AWS SAA/Kinesis | Kinesis]]
[[Kinesis DVA]]

## Lambda
[[../101 AWS SAA/Lambda | Lambda]]

Three ways in which Lambda processes events
- [[Lambda Synchronous Invocation]]
- [[Lambda Asynchronous Invocation]]
- [[Lambda Event Source Mapping]]

[[Lambda Destinations]]
[[Lambda IAM Role]]
[[Lambda Configuration & Deployments]]
[[Lambda Performance]]

### Miscellaneous
- For *logging*:
	- For *each lambda a log group* is created. 
	- The logs are then grouped into streams. We can find the logs if we go inside a particular stream.
- **Best Practices**:
	- *Perform heavy-duty work outside of your function handler*
		- Connect to databases outside of your function handler
		- Initialise the AWS SDK outside of your function handler
		- Pull in dependencies or datasets outside of your function handler
	- Use *environment variables* for:
		- Database Connection Strings, S3 bucket, etc... don't put these values in your code
		- Passwords, sensitive values, etc can be encrypted using KMS
	- *Minimise your deployment package size to its runtime necessities*
		- Break down the function if need be
		- Remember the AWS Lambda limits
		- *Use Layers* where necessary
- **Avoid using recursive code, never have a Lambda function call itself**. This can become very expensive.

## DynamoDB
[[../101 AWS SAA/DynamoDB|DynamoDB]]
[[DynamoDB Capacity]]
[[DynamoDB Basic APIs]]
[[DynamoDB Indexes]]
[[DynamoDB DVA]]

## API Gateway
[[../101 AWS SAA/API Gateway|API Gateway]]
[[API Gateway DVA]]
[[API Gateway Integration Types]]
[[API Gateway Caching]]
[[API Gateway Monitoring]]
[[API Gateway Security]]

## SAM & CDK
[[SAM]]
[[CDK]]

## Cognito & Step Functions 
[[Cognito]]
[[Step Functions]]

## Security
[[STS & IAM Advanced DVA]]
[[../101 AWS SAA/KMS|KMS]]
[[KMS DVA]]
[[KMS Working]]
[[SSM Parameter Store vs Secrets Manager]]

## Other Services
[[Other Services DVA]]

## Miscellaneous
- Typical 3 tier architecture
	- ![[attachments/Pasted image 20220514121352.png]]
	- *Data subnet* is also a *private subnet*.

## Exam Tips
- There are **very very few trick questions**
- If a solution seems feasible but *highly complicated, it's probably wrong*