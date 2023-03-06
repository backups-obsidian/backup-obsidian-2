---
created: 2022-04-18 21:09
updated: 2023-03-06 14:37
---
---
**Links**: [[101 AWS SAA Index]]

---
## Introduction
- It is a **Global** service.
- It allows us to manage **multiple AWS accounts** in the organisation.
- The **main account** is called the **management account** and cannot be changed and other accounts are known as **member accounts**.
- Member accounts can only be **part of one AWS organisation** but they **can be moved** from one AWS organisation to another.

> [!question]- How to move an AWS member account from Organisation A to B
> To move an account from organisation A to B **Remove** the member account from the old organisation. Send an **invite** to the member account from the new Organisation. **Accept** the invite to the new organisation from the member account
> ---
> *Remove* -> *Invite* -> *Accept*

> [!question]- How to make master account join the new organisation.
> **Remove** all the members from the current organisation. **Delete the organisation**. **Invite** the master to the new account.

-  You can **invite other accounts** to join your organisation.
-  **Consolidated billing** across all the accounts. 
	- **All the AWS accounts should fall under a single consolidated billing** for the monthly fee **to be charged only once**.
- *Pricing benefits* from aggregated usage
	- We can share reserved instances and Savings Plans discounts across accounts.
- We can **create member accounts** using **API**.
- We can **group accounts** into **Organisation Units** (OUs).
	- ![[attachments/Pasted image 20220418233940.png]]

>[!tip]+ How to identify questions related to organisations
> There will be words like **sharing**, **centrally manage**, consolidated billing.

## Multi Account Strategies
- Create accounts per department, per cost centre, per dev / test prod, based on regulatory restrictions (using SCP), for better resource isolation (ex: VPC), to have separate per-account service limits, isolated account for logging
- Enable **CloudTrail** on all accounts, **send logs to central S3 account**.
- Send **CloudWatch** Logs to **central logging account**.

## SCP (Service Control Policies)
- Used for managing permissions easily from a central location.
- We can whitelist or blacklist IAM actions applied at the **OU** or **account level** but **does not apply to *management* account**.

> [!caution] SCPs have no effect on **master account**

- **SCP is applied** to all the **Users and Roles of the Account, including Root**. 
	- So if you are not able to use EC2 then the admin of that account will also not be able to use EC2
- A user can access a service if **both SCP and IAM policies allow it**. If denied by either of them then user won't be access the service.
- SCP **doesn't apply to service linked roles**. Service-linked roles enable other AWS services to integrate with AWS Organisations and can't be restricted by SCPs.

> [!caution] SCPs must have an **explicit allow**. By default everything is denied.


- Only **service control policies (SCPs)** in organisations can **restrict the permissions** that are granted to the **root user of an account**.

- *SCPs are inherited*. 
	- So if an upper OU has an SCP attached to it then the OU under it will also inherit it.
- **SCP hierarchy**
	- ![[attachments/Pasted image 20230306112533.png]]

- We can configure SCPs in 2 different strategies:
	- **Block list**: In case of block list we *first allow all the services* and *then deny the services* to which we want to prevent access.
		- ![[attachments/Pasted image 20230306113031.png]]
	- **Allow list**: In allow list we *only allow the services to which we want to allow access*. Now since SCPs must have an explicit allow, other services are denied.
		- ![[attachments/Pasted image 20230306113052.png]]

## SOA Content
### Reserved Instances Sharing
- For billing purposes, **the consolidated billing feature of AWS Organisations treats all the accounts in the organisation as one account**.
	- This means that all accounts in the organisation can receive the hourly cost benefit of Reserved Instances that are purchased by any other account.
- The payer account (master account) of an organisation *can turn off Reserved Instance (RI) discount and Savings Plans discount sharing* for any accounts in that organisation, including the payer account.
	- This means that Rls and Savings Plans discounts aren't shared between any accounts that have sharing turned off.
- *To share an Rl or Savings Plans discount with an account, both accounts must have sharing turned on*.

> [!question]- You manage a set of AWS Accounts using AWS Organisation which has Consolidated Billing feature enabled and Reserved Instance Discount Sharing turned on. You have an AWS Account that purchased a set of reserved EC2 instances that the owner doesn't want to share with the AWS Organisation. What should you do?
> *Disable Reserved Instance Discount Sharing at the AWS account level*.

### IAM Policies
- Use `aws:PrincipalOrgID` condition key in your resource-based policies to restrict access to IAM principals from accounts in an AWS Organisation.
	- ![[attachments/Pasted image 20230306114351.png]]

### Tag Policies
- Helps you **standardise tags across resources** in an AWS Organisation
	- Ensure consistent tags, audit tagged resources, maintain proper resources categorisation, etc.
- You define tag keys and their allowed values.
- Helps with *AWS Cost Allocation Tags* and *Attribute-based Access Control*.
- *Prevent any non-compliant tagging operations* on specified services and resources (has no effect on resources without tags).
- Generate a report that lists all tagged/non-compliant resources.
- Use CloudWatch Events to monitor non-compliant tags.