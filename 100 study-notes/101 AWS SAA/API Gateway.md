---
created: 2022-04-19 19:08
updated: 2022-05-27 09:39
---
---
**Links**: [[101 AWS SAA Index]]

---
## Introduction
- We use an **API Gateway** for creating **REST APIs**. The client will talk to the API gateway and then the API gateway will proxy the request to the lambda.
- We use **API gateway** as it provides us with a **lot of features**. These features are not present when you use an ALB.
- API Gateway creates RESTful APIs that:
	- Are **HTTP-based**.
	- Enable *stateless client-server communication*.
	- *Implement standard HTTP methods* such as GET, POST, PUT, PATCH, and DELETE.

- API Gateway creates WebSocket APIs that:
	- Adhere to the WebSocket protocol, which enables stateful, full-duplex communication between client and server. Route incoming messages based on message content.

- So API Gateway *supports stateless RESTful APIs* as well as **stateful WebSocket APIs**

## Features
- **API Gateway + Lambda** = **No infrastructure** to manage.
- You pay only for the *API calls you receive* and the *amount of data transferred out*.
- We can **handle API versioning** (v1,v2..) without breaking the clients.
- Handle **different environments** (test, dev, prod)
- We can **handle security** (*authentication and authorisation*) using API Gateway.

-  We can **cache API responses**. 
	- API caching is enabled for a stage (like prod, test etc) and *not a method*.

- **Transform and validate requests and responses**

### Throttling
- We can have *per client throttling limits* using the API keys. 
	- Like having *different throttling limits for the basic and premium users*.

- We *can also have per method throttling limits*. 

- We can create **API keys** and do *request throttling to protect backend systems*.
	- Any request over the limit will receive a *429 HTTP* (Too many requests) response.
	- The requests are failed.

## API Gateway Integrations
- *Lambda* functions: for full serverless applications.
- *HTTP*: 
	- Expose HTTP endpoints in the backend. Example: *internal HTTP API on premise*
	- To add rate limiting, caching, user authentications, API keys, etc...
- *AWS Services*:
	- Expose **any AWS API** through the API Gateway
	- To add authentication, deploy publicly, rate control etc

## API Gateway Endpoint Types
- **Edge-Optimized** (**default**): 
	- For *global clients* 
	- *Requests are routed through the CloudFront Edge locations* (improves latency)
	- The API Gateway still lives in only one region

- Regional:
	- For *clients within the same region*
	- *Could manually combine* with CloudFront (more control over the caching strategies and the distribution)

- Private:
	- Can *only be accessed from your VPC* using an interface VPC endpoint (ENI)
	- Use a resource policy to define access

## Security
### IAM
- Create an IAM policy authorisation and attach to User / Role
- **Great for users / roles *already within* your AWS account**
	- ![[attachments/Pasted image 20220427095642.png]]
- *Authentication = IAM* | *Authorisation = IAM Policy*
- Good to provide access within AWS (EC2, Lambda, lAM users...)
- Handles authentication + authorisation
- Leverages **Sig v4** capability where IAM credential are in headers

### Lambda/Custom Authoriser
- **Token-based authoriser** (bearer token) ex JWT (SON Web Token) or Oauth
- *Authentication = External* | *Authorisation = Lambda function*
- Uses **AWS Lambda** to **validate the token** in header being passed
	- A *request parameter-based* Lambda authoriser (headers, query string, stage var)
	- Retrieves the token from an external provider, uses the lambda authoriser to validate the token with the external provider and then generate the IAM policy.
	- ![[attachments/Pasted image 20220527093730.png]]

- Helps to use OAuth / SAML / **3rd party type of authentication**
- Lambda **must return an IAM policy for the user**. Very flexible in what IAM policy is returned.

### Cognito User Pools
- **Cognito fully manages user lifecycle**
- *First the call is to cognito* user pools to authenticate and get the token. Once the token is returned it is sent along with the REST API request.
	- Cognito has a direct integration with API Gateway so the token sent with the REST request is evaluated.
	- ![[attachments/Pasted image 20220427095926.png]]

- **No custom implementation required**
- *Authentication = Cognito User Pools* | *Authorisation = API Gateway Methods*
	- Cognito **only** helps with **authentication**, **not authorisation**.
