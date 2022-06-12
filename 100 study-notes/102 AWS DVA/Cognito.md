---
created: 2022-05-30 09:35
updated: 2022-05-30 12:26
---
---
**Links**: [[102 AWS DVA Index]]

---
## Cognito
- We want to give our users an identity so that they can *interact with our application*.
- **Cognito User Pools**:
	- Sign in functionality for *app users*
	- Integrate with *API Gateway* & *Application Load Balancer*
- **Cognito Identity Pools** (Federated Identity):
	- Provide *AWS credentials to users* so they can access AWS resources directly
	- Integrate with Cognito User Pools as an identity provider
- Cognito Sync:
	- Synchronise data from device to Cognito.
	- *Is deprecated and replaced by App Sync*

> [!note] In questions if you see *hundreds of users*, *mobile users*, *authenticate with SAML*, *OpenID connect* then go for cognito over IAM

### Cognito User Pools
- Create a **serverless database of user for your web & mobile apps**
- Features
	- Simple login: Username (or email) / password combination
	- Password reset
	- Email & Phone Number Verification
	- Multi-factor authentication (MFA)
	- Federated Identities: users from Facebook, Google, SAML...
	- Block users if their credentials are compromised elsewhere
- *Login sends back a JSON Web Token (JWT)*
	- ![[attachments/Pasted image 20220530094045.png]]
- Integrations: **ALB & API Gateway**
	- ![[attachments/Pasted image 20220530094218.png]]
- We can *trigger lambda on different events* like user sign up, post authentication etc.
	- ![[attachments/Pasted image 20220530094848.png]]
- *Hosted authentication UI*
	- Cognito has a hosted authentication Ul that you can add to your app to handle sign up and sign-in workflows
	- Using the hosted UI, you have a foundation for integration with social logins, OIDC or SAML
	- Can customise with a custom logo and custom CSS

### Cognito Identity Pools (Federated Identity)
- Get identities for users so they **obtain temporary AWS credentials**
	- We don't want to create IAM users for these users since there are so many of them and it can be a security risk.
- Your identity pool (e.g identity source) can include:
	- *Public Providers* (Login with Amazon, Facebook, Google, Apple)
	- Users in an Amazon *Cognito user pool*
	- *OpenID Connect* Providers & *SAML* Identity Providers
	- Developer Authenticated Identities (custom login server)
	- Cognito Identity Pools allow for *unauthenticated (guest) access*
- *Users can then access AWS services directly or through API Gateway*
- The **IAM policies** applied to the credentials are defined in Cognito
- They can be customised based on the user_id for *fine grained control*

> [!question]- Example Question
> You have a photo-sharing application where you want your users to log in with Twitter. **Users should also have permission to directly access their own data** in the _Users_ DynamoDB table and access their own photos in an S3 bucket that contains photos for all users. Which of the following you can use?
> 
> Cognito Identity Pools 

#### Steps
- Get the token from an Identity provider
- Give the token to Cognito Identity Pool which will be verified against the identity provider
- Upon successful verification get the temporary credentials from STS and give it to the user.
- Now the users can talk directly to the AWS Services using the temporary credentials
	- ![[attachments/Pasted image 20220530120432.png]]
- If we use *Cognito User Pools as the Identity Provider* then Cognito Identity Pool will verify the tokens against Cognito User Pool. 
	- Useful if you want to maintain an internal database of Users
	- ![[attachments/Pasted image 20220530120646.png]]

#### IAM Roles
- How does Cognito Identity Pool decide which role to assign to which user?
- *Default IAM roles* for **authenticated and guest users**
	- ![[attachments/Pasted image 20220530121102.png]]
- We define _rules to choose the role for each user based on the **user's ID**_
	- ![[attachments/Pasted image 20220530121300.png]]
	- Here the rule is that user is only allowed get and put actions on the bucket which has a prefix equal to the user id
- Users are mapped to IAM roles & policies
- You can partition your user's access using policy variables
- *lAM credentials* are obtained by Cognito Identity Pools *through STS*
- The **roles must have a trust policy of Cognito Identity Pools**

### Cognito Sync
- Deprecated service
- Replaced by **AppSync**
- _Store preferences, configuration, state of **app**_
- *Cross device* synchronisation (any platform iOS, Android, etc...)
- *Offline capability* (synchronisation when back online)
- Store data in datasets (up to 1MB), up to 20 datasets to synchronise
- **Push Sync**: silently *notify across all devices* when identity data changes
- **Cognito Stream**: stream data from *Cognito into Kinesis*
- **Cognito Events**: execute *Lambda functions* in response to events 