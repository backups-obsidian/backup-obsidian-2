---
created: 2022-05-30 09:35
updated: 2023-02-20 08:48
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
	- **Integrate with Cognito User Pools as an identity provider**
- Cognito Sync:
	- Synchronise data from device to Cognito.
	- *Is deprecated and replaced by App Sync*

- Cognito returns **Cognito ID**

> [!note] In questions if you see *hundreds of users*, *mobile users*, *authenticate with SAML*, *OpenID connect* then go for cognito over IAM

### Cognito User Pools
- Create a **serverless database of user for your web & mobile apps**
- Features
	- Simple login: Username (or email) / password combination
	- Password reset
	- Email & Phone Number Verification
	- **Multi-factor authentication (MFA)**
	- Federated Identities: users from Facebook, Google, SAML...
	- Block users if their credentials are compromised elsewhere
- *Login sends back a JSON Web Token (JWT)*
	- ![[attachments/Pasted image 20220530094045.png]]
- Integrations: **ALB & API Gateway**
	- ![[attachments/Pasted image 20220530094218.png]]
- We can *trigger lambda on different events* like user sign up, post authentication etc.
	- ![[attachments/Pasted image 20220530094848.png]]
- **Hosted authentication UI**
	- Cognito has a hosted authentication Ul that you can add to your app to handle sign up and sign-in workflows
	- Using the hosted UI, you have a foundation for integration with social logins, OIDC or SAML
	- Can *customise with a custom logo and custom CSS* in the hosted UI.

> [!note] To change the logo for hosted UI: **Upload the logo to the Amazon Cognito app settings and use that logo on the custom login page**.

### Cognito Identity Pools (Federated Identity)
- Get identities for users so they **obtain temporary AWS credentials**
	- We don't want to create IAM users for these users since there are so many of them and it can be a security risk.
- **For AWS S3 and DynamoDB**.
- Your **identity pool** (e.g identity source) can include:
	- *Public Providers* (Login with Amazon, Facebook, Google, Apple)
	- Users in an Amazon **Cognito user pool**
	- **OpenID Connect** Providers & **SAML** Identity Providers
	- Developer Authenticated Identities (custom login server)
	- Cognito Identity Pools allow for **unauthenticated (guest) access**
- *Users can then access AWS services directly or through API Gateway*
- The **IAM policies** applied to the credentials are defined in Cognito
- They can be customised based on the user_id for *fine grained control*

> [!question]- Example Question
> You have a photo-sharing application where you want your users to log in with Twitter. **Users should also have permission to directly access their own data** in the _Users_ DynamoDB table and access their own photos in an S3 bucket that contains photos for all users. Which of the following you can use?
> 
> Cognito Identity Pools 

> [!question]- A developer is writing a web application that will allow users to save and retrieve images in an Amazon S3 bucket. The users are required to register and log in to access the application. Which combination of AWS Services should the Developer utilise for implementing the user authentication module of the application?
> **Amazon Cognito Identity Pools and User Pools**
> ![[attachments/Pasted image 20230219124739.png]]

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
- **Offline capability** (synchronisation when back online)
- Store data in datasets (up to 1MB), up to 20 datasets to synchronise
- **Push Sync**: silently *notify across all devices* when identity data changes
- **Cognito Stream**: stream data from *Cognito into Kinesis*
- **Cognito Events**: execute *Lambda functions* in response to events 

> [!question]- A company is developing a game for the Android and iOS platforms. The mobile game will securely store user game history and other data locally on the device. *The company would like users to be able to use multiple mobile devices and synchronise data between devices*. Which service can be used to synchronise the data across mobile devices without the need to create a backend application?
> Cognito Sync.
> Use AppSync if option is there.

## Miscellaneous
- **Adaptive authentication cognito**:
	- With adaptive authentication, you can configure your user pool to *block suspicious sign-ins* or add second factor authentication in response to an increased risk level.
- **Developer authenticated identities**:
	- With authenticated identities, you can **register and authenticate users via your own existing authentication process**, while still using Amazon *Cognito to synchronise user data* and access AWS resources.
	- Using developer authenticated identities involves *interaction* between the *end-user* device, your *backend for authentication*, and *Amazon Cognito*. 

> [!question]- A company is planning to launch an online cross-platform game that expects millions of users. The developer wants to *use an in-house authentication system for user identification*. Each *user identifier must be kept consistent across devices and platforms*. How can the developer achieve this?
> 

- To use an Amazon **Cognito user pool with your API Gateway**, you must first create an **authoriser of the `COGNITO_USER_POOLS`** type and then *configure an API method to use that authoriser*.

> [!question]- **Guest users (unauthenticated) in cognito Identity pools**. User Pool doesn't have an option to enable unauthenticated identities.
> A company has an application that provides access to objects in Amazon S3 based on the type of user. The user types are registered user and guest user. The company has 30,000 users. Information is read from an S3 bucket depending on the user type. Which approaches are recommended to provide access to both user types MOST efficiently?
> ---
> - Use Amazon Cognito to provide access **using authenticated and unauthenticated roles**.
> - Amazon *Cognito **identity pools** support both authenticated and unauthenticated identities*.
> - Use the AWS IAM service and let the application assume different roles depending on the type of user.
