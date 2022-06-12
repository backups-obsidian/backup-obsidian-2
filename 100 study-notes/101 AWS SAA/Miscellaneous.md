---
created: 2022-04-19 09:25
updated: 2022-05-05 15:49
---
---
**Links**: [[101 AWS SAA Index]]

---

## Permission Boundaries
- It sets the maximum permissions an [[IAM Basics#^182218|entity]] can get.
- When you use a policy to set the permissions boundary for a user, it *limits the user's permissions* but does not provide permissions on its own.

>[!caution] They are supported for users and roles and **not for groups**.

- Example evaluation
	- ![[attachments/Pasted image 20220419092908.png]]

- It can be used in **combination** with SCPs and Identity based policies to form a more strict effective permission
	- ![[attachments/Pasted image 20220419093001.png]]

## Different types of policies
- **AWS Managed Policy**: Managed and created by AWS.
- **Customer Managed Policy**: Managed and created by customers generally by editing the existing AWS Managed policies.
- **Inline policies**: Adding a policy that is **attached to an identity** (group, user or role)

> [!question]+ Why use AWS managed or Customer managed policy over inline policy?
> When the **identity is deleted the inline policy gets deleted**. It is also **not reusable** for example if you want to attach the same inline policy to another user you will have to create it again whereas if you create a customer managed policy then you can assign it to any user, role or group.

## Trust Policy
- **Trust policies** define which *principal entities* (accounts, users, roles, and federated users) *can assume the role*. 
- An IAM role is **both an identity and a resource** that supports resource-based policies.
- For this reason, you *must attach both a trust policy and an identity-based policy to an IAM role.* 
- The IAM service supports **only one type of resource-based policy** called a role trust policy, which is attached to an IAM role.

> [!note] Trust policy is the only **resource based policy** supported by IAM service.

## RAM (Resource Access Manager)
- **Share AWS resources** that you own with **other AWS accounts** or share with any account **or within your Organisation**.
- Helps in avoiding resource duplication.
- We can share resources like **Transit Gateway** and **VPC subnets**.

>[!caution]+ **Conditions** related to VPC subnet sharing.
> - Accounts sharing the subnet must be in the **same AWS organisation**.
> - Each account is responsible for its own resources.
> - We **only share the networking layer**.
> -  **Cannot** **view**, **modify** and **delete** resources from other accounts.
> -  The applications can access each other using the private IP.
> - Can reference security group from other accounts from maximum security.

## SSO
- It is a **free service** from AWS.
- [[STS & Identity Federation#SAML 2 0 Federation|Support SAML 2.0]]
- Centrally manage Single Sign-On to access multiple accounts and 3rd-party business applications.
- Integrated with AWS organisations.
- Can be **integrated with on premise AD** using  AWS AD connector or AWS Managed AD.
	- ![[attachments/Pasted image 20220419094652.png]]

> [!tip] When you see words like **SSO** think of **SAML 2.0 federation**, **AssumeRoleWithSAML** and **STS**.

### Difference between SSO and AssumeRoleWithSAML
- 3rd party login portal and STS is replaced by AWS SSO.
- Everything else remains the same.
![[attachments/Pasted image 20220419095113.png]]
