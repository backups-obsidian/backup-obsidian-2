---
created: 2022-04-18 21:09
updated: 2022-04-18 23:59
---
---
**Links**: [[101 AWS SAA Index]]

---
## Introduction
- It is a **Global** service.
- It allows us to manage **multiple AWS accounts** in the organisation.
- The **main account** is called the **master account** and cannot be changed and other accounts are known as **member accounts**.
- Member accounts can only be **part of one AWS organisation** but they **can be moved** from one AWS organisation to another.

> [!question]- How to move an AWS member account from Organisation A to B
> To move an account from organisation A to B **Remove** the member account from the old organisation. Send an **invite** to the member account from the new Organisation. **Accept** the invite to the new organisation from the member account
> ---
> *Remove* -> *Invite* -> *Accept*

> [!question]- How to make master account join the new organisation.
> **Remove** all the members from the current organisation. **Delete the organisation**. **Invite** the master to the new account.

-  You can **invite other accounts** to join your organisation.
-  **Consolidated billing** across all the accounts. **All the AWS accounts should fall under a single consolidated billing** for the monthly fee **to be charged only once**.
-   Pricing benefits from aggregated usage
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
- We can whitelist or blacklist IAM actions applied at the **OU** or **account level** but **does not apply to Master account**.

> [!caution] SCPs have no effect on **master account**

- **SCP is applied** to all the **Users and Roles of the Account, including Root**. So if you are not able to use EC2 then the admin of that account will also not be able to use EC2
- A user can access a service if **both SCP and IAM policies allow it**. If denied by either of them then user won't be access the service.
- SCP **doesn't apply to service linked roles**. Service-linked roles enable other AWS services to integrate with AWS Organisations and can't be restricted by SCPs.

> [!caution] SCPs must have an **explicit allow**. By default everything is denied.

- Only **service control policies (SCPs)** in organisations can **restrict the permissions** that are granted to the **root user of an account**.

- **SCP hierarchy**
	- ![[attachments/Pasted image 20220418235317.png]]

