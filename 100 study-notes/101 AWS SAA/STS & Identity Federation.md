---
created: 2022-04-18 20:21
updated: 2023-03-18 09:04
---
---
**Links**: [[101 AWS SAA Index]]

---

## STS
- Security Token Service
- Grants **temporary access** by providing **short lived tokens** which act as temporary credentials.
- Valid for 15 minutes to an **1 hour**(max) after which it must be refreshed.
- It is used for assuming roles. [[IAM Basics#^fe4632 | IAM role can be assumed by]]

> [!question]+ Why assume roles in your own AWS account.
> Imagine that you have Amazon EC2 instances that are critical to your organisation. Instead of directly granting your users permission to terminate the instances, you can *create a role with those privileges*. Then allow administrators to *switch to the role* when they need to terminate an instance. Doing this adds the following layers of protection to the instances:
>
> - You must *explicitly grant your users permission to assume the role*.
> - Your users *must actively switch to the role* using the AWS Management Console or assume the role using the AWS CLI or AWS API.
> - You can add multi-factor authentication *(MFA) protection to the role* so that only users who sign in with an MFA device can assume the role. 

^a42567

- You assume a role in the current or cross account using the **AssumeRole** API.
- IAM users must have permission to switch to roles in the console. You don't need this if you are assuming role using CLI or SDK.
- You can define principals which can access a particular IAM Role.

> [!tip]- Changing roles using management console vs CLI or SDK
> If you are changing your role using the management console then you lose your current permissions. For example if you are administrator and you assume some role specific to EC2 deletion then you can perform administrative roles as long as you are in this role. You have to exit out of the role.
>
> ---
> *An application doesn't have to exit the role the way a user in the console does*; rather the application simply stops using the temporary credentials and resumes making calls with the original credentials.

- It is advised to use roles for cross account access instead of creating IAM users since creating separate identities (and passwords) for users who work in both accounts, managing credentials for multiple accounts makes identity management difficult.

## Identity Federation

### What is Federation
- External source of truth for managing users
- Like you already have users in your organisation and a way of authenticating them, such as by signing in to your corporate network, you *don't have to create separate IAM users* for them. Instead, you can *federate those user identities into AWS*.
- Federated users don't have permanent identities in your AWS account the way that IAM users do. To assign permissions to federated users, you can create an entity referred to as a role and define permissions for the role. When a federated user signs in to AWS, the user is associated with the role and is granted the permissions that are defined in the role.
- Federation lets **users outside of AWS to assume temporary role for accessing AWS resources**.
- Using federation, we don't need to create lAM users (**user management is outside of AWS**) 
- There are different flavours of federation.

### SAML 2.0 Federation
- *Large enterprises*.
- To integrate with ADFS (**Active Directory File System**) or any **SAML 2.0 based system**.
- STS API: **AssumeRoleWithSAML**
- SAML 2.0 is the **old way for SSO**. [[Miscellaneous#SSO|AWS SSO]] is the new way.
- **Flow diagrams**
	- ![[attachments/Pasted image 20220418205329.png]]
	- ![[attachments/Pasted image 20220418205355.png]]
- For using this the **IDP** (Identity provider) must be **compatible with SAML**.

> [!note]- If we want to to redirect the user to the management console then we need to use SAML assertion with SSO endpoint as shown in diagram 1.

### Custom Identity broker application
- IDP **not compatible with SAML**.
- STS API: **AssumeRole** or **GetFederationToken**
- The *identity broker must determine the appropriate IAM policy*.
- It is exactly like SAML but we have to do *a lot more manual work*.

> [!important]+
> After getting the STS token in SAML 2.0 or custom identity broker we need call the AWS **federation endpoint** and supply the temporary security credentials to **request a sign-in token**.

### Web Identity Federation
- Not recommended by AWS. **Cognito is preferred**.
- STS API: **AssumeRoleWithWebIdentity**

### AWS Cognito
- We have **Identity pools** and **User pools**.
- STS API: **AssumeRoleWithWebIdentity**
- In Cognito we can have **MFA**.

#### User Pools
- Create a serverless database of **user for your mobile apps**. 
- *Simple login*: Username (or email) / password combination with the possibility to verify emails / phone numbers and *add MFA*
-   *Can enable* **Federated Identities** (Facebook, Google, SAML...)
- Sends back a JSON Web Tokens (*JWT*).
- With a user pool, *users can sign in to web or mobile apps* through Amazon Cognito. Something like okta or keycloak. Manages all the authentication.

> [!important] Can be integrated with [[Types of ELBs#^92a439|ALB]] and [[API Gateway#Cognito User Pools|API Gateway]] for authentication.

#### Identity Pools
- Provide **direct access to AWS resources** such as *S3* and *DynamoDB* from the Client Side.
- Provides **temporary access**.
- Can *use federated identities* with identity pool also

> [!note] Identity pool is the one that *uses AssumeRoleWithWebIdentity* and not user pool.