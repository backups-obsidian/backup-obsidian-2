---
created: 2023-01-31 21:49
updated: 2023-01-31 21:49
---
---
**Links**: [[102 AWS DVA Index]]

---
> [!question]- Difference between AWS IAM roles and AWS resource based policies
> - AWS IAM (Identity and Access Management) roles are used to delegate permissions to AWS resources to trusted entities such as IAM users, AWS services, and external IdPs. They allow you to define what actions can be performed on specific AWS resources by who or what.
> - AWS resource-based policies, on the other hand, are policies that you attach directly to a resource, such as an Amazon S3 bucket or an Amazon SNS topic. Resource-based policies give permissions to specific AWS accounts or to all AWS accounts to access your resource.
> - In summary, IAM roles provide a way to manage access at the user or service level, while resource-based policies provide a way to manage access at the resource level.

> [!question]- So suppose I want to access AWS lambda should I have an AWS IAM role for accessing it or a resource based policy?
> - In order to access an AWS Lambda function, you would need an IAM role. An IAM role is used to grant permissions to entities such as IAM users, AWS services, and external IdPs to access AWS resources.
> - When you create a Lambda function, you can specify an IAM role that determines what other AWS resources your function can access. This role is known as an execution role. The execution role grants permissions that determine what other AWS resources your Lambda function can access.
> - You can also use resource-based policies to control access to the Lambda function itself, for example, to control who can invoke the function. However, in order to access the Lambda function and perform any actions, an IAM role is required.

> [!question]- If my lambda functions wants to access a SQS queue in my account then do I need a execution role for lambda or a resource based policy for SQS?
> - Both an IAM execution role for the Lambda function and a resource-based policy for the SQS queue are required for the Lambda function to access the SQS queue.
> - The IAM execution role for the Lambda function defines the permissions that the function has to access other AWS resources. To allow the Lambda function to access an SQS queue, you need to grant the required permissions to the execution role. This can be done by attaching an IAM policy to the role that allows the necessary actions on the SQS queue.
> - The resource-based policy for the SQS queue, on the other hand, determines who or what has permission to access the queue. To allow the Lambda function to access the queue, you need to add a statement to the queue's policy that grants the required permissions to the execution role of the Lambda function.
> - In summary, both the IAM execution role for the Lambda function and the resource-based policy for the SQS queue must be configured correctly for the Lambda function to access the SQS queue.

