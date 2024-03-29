---
created: 2022-04-19 19:08
updated: 2023-03-22 08:37
---
---
**Links**: [[101 AWS SAA Index]]

---
## Introduction
- Helps with **auditing** and recording **strict compliance** of your **AWS resources** based on the rules that we set.
- Using AWS Config we **setup rules** to adhere to the compliance.
- Helps us **record configuration and changes** over time so that we can roll back.
- AWS config is a **per region service** which can be aggregated across regions and accounts.
- There is a possibility of **storing the configuration** data into **S3** (analysed by Athena)
- Not a free service.

> [!question]- Questions solved by AWS config
> - Is there unrestricted SSH access to my security groups?
> - Do my buckets have any public access?
> - How has my ALB configuration changed over time?
> - Password policy
> - Is there a port which I always want closed?

> [!note] Keywords: *compliance*, *history*, *configuration changes*.

## Config Rules
- We can use **AWS managed config rules** (over 75)
- We can make our own **custom config rules**. 
	- It *must be defined in AWS Lambda*.
	- Example: If the EBS disk is of type gp2.
-   Rules can be evaluated/*triggered* for:
    - Each *config change*
    - At *regular time intervals*.
- AWS **config rules** are **just for compliance** and they **DON'T prevent actions from happening**.
- We can view the **compliance & configuration** of a resource over time.
	- ![[attachments/Pasted image 20230306095934.png]]
- Config can be *linked with CloudTrail* to get a full picture.

### Remediations
- *Automate remediation of non-compliant resources* using SSM Automation Documents
- Use **AWS-Managed Automation Documents** or create **custom Automation Documents**
	- You can create *custom Automation Documents* that invokes **Lambda function**

### Notifications
- We can use [[EventBridge]] to **trigger notifications whenever our resources are not compliant**.
	- ![[attachments/Pasted image 20220428094530.png]]

- We can also **send all the configuration changes to SNS** and then use SNS filtering or filtering at client side.
	- ![[attachments/Pasted image 20220428094638.png]]


> [!important]- How can we can use *EventBridge to detect and react to changes in the status of AWS Config events*?
> For example: A company requires that IAM users must rotate their access keys every 60 days. If an access key is found to older it must be removed.
> - The AWS Config rule can be configured using the *"access-keys-rotated" managed rule* which checks if the active access keys are rotated within the number of days specified in maxAccessKeyAge. 
> - The rule is NON_COMPLIANT if the access keys have not been rotated for more than maxAccessKeyAge number of days.
> - *Amazon EventBridge can react to the change of state to NON_COMPLIANT* and trigger an *AWS Lambda function that invalidates and removes the access key*.

## Aggregators (SOA)
- The aggregator is *created in one central aggregator account*.
- Aggregates rules, resources, etc **across multiple accounts & regions**
	- ![[attachments/Pasted image 20230306101052.png]]
- If using AWS Organisations, no need for individual Authorisation
- **Rules are created in each individual source AWS account**.
- Can *deploy rules to multiple target accounts* using *CloudFormation StackSets*.