---
created: 2022-05-16 16:54
updated: 2023-02-19 09:50
---
---
**Links**: [[102 AWS DVA Index]]

---
- The main reason for using API Gateway over ALB for invoking Lambda's from HTTP endpoints are the features (like caching and all) offered by the API Gateway.
- **All** of the APIs created with Amazon API Gateway expose **HTTPS** endpoints **only**. 
	- *Amazon API Gateway DOES NOT support unencrypted (HTTP) endpoints*.

## Deployment Stages
- *Making changes in the API Gateway does not mean they're effective*. 
	- We need to **deploy or redeploy them (to a new or existing stage)**.
- **Staging refers to the process of deploying the API**.
- *API stages are included in the URL* that you use to invoke the API.
- **Changes are deployed to Stages** (as many as you want). 
	- Use the naming you like for stages (dev, test, prod)
	- Each *stage has its own configuration parameters*
	- *Stages can be rolled back* as a history of deployments is kept
- We can have *multiple stages* deployed together. 
	- A common use case is to have 2 stages v1 and v2 to *maintain compatibility*. 
	- We can migrate the clients from v1 to v2 gradually and when no more clients are using v1 we can shut it down.
	- ![[attachments/Pasted image 20220526151955.png]]
- Each stage has its own settings
	- ![[attachments/Pasted image 20220526153838.png]]

### Stage Variables
- Stage variables are like **environment variables for API Gateway**
- Use them to change **often changing configuration values**
- They can be used in:
	- *Lambda function ARN*
	- *HTTP Endpoint*
	- *DynamoDB table*
	- Parameter mapping templates
- Use cases:
	- **Configure HTTP endpoints your stages talk to (dev, test, prod...)**
	- Pass configuration parameters to AWS Lambda through mapping templates
- Stage variables are passed to the **context object in AWS Lambda**

#### Stage Variables & Aliases
- We create a *stage variable to indicate the corresponding Lambda alias*
- Our API gateway will automatically invoke the right Lambda function!
	- ![[attachments/Pasted image 20220526152359.png]]
	- ![[attachments/Pasted image 20220526152601.png]]
	- ![[attachments/Pasted image 20220526153218.png]]

## Canary Deployments
- Possibility to enable canary deployments **for any stage (usually prod)**
	- ![[attachments/Pasted image 20220526154203.png]]
- Choose the *% of traffic* the canary channel receives 
	- ![[attachments/Pasted image 20220526154031.png]]
- Metrics & Logs are separate (for better monitoring)
- Possibility to override stage variables for canary
- This is **blue/green deployment** with AWS Lambda & API Gateway

## Swagger / Open API spec
- We can *import existing* Swagger / OpenAPI 3.0 spec to API Gateway
- We can also *export current API* as Swagger / OpenAPI spec
- *JSON or YAML file*
- Once imported API will be created from the JSON/YAML document.

## Usage Plans & API Keys
- We can use **API Gateway usage plans** if we want to *make an API available as an offering ($) to your customers*.
- **Usage Plan**:
	- *Who can access* one or more deployed API stages and methods
	- *How much and how fast* they can access them
	- Uses *API keys to identify API clients and meter access*
	- *Configure throttling limits and quota* limits that are enforced on individual client
- **API Keys**:
	- Alphanumeric string values to distribute to your customers
	- Can use with *usage plans to control access*
	- **Throttling limits are applied to the API keys**
	- Quotas limits is the overall number of maximum requests
- **Configuring a usage plan**: 
	- Create one or more APIs, *configure the methods to require an API key*, and deploy the APIs to stages.
	- *Generate or import API keys* to distribute to application developers (your customers) who will be using your API.
	- *Create the usage plan* with the desired throttle and quota limits.
	- *Associate API stages and API keys with the usage plan*.

> [!tip] Usage plan order: Create API -> Create Key -> Create Usage Plan -> Associate Usage Plan

> [!note] Callers of the API must supply an assigned API key In the `x-api-key` header in requests to the API

> [!question]- A company maintains a REST API service using Amazon API Gateway with native API key validation. The company recently launched a new registration page, which allows users to sign up for the service. The registration page creates a new API key using *CreateApiKey* and sends the new key to the user. When the user attempts to call the API using this key, the user receives a *403 Forbidden error*. Existing users are unaffected and can still call the API. What code updates will grant these new users’ access to the API?
> The *createUsagePlanKey* method must be called to associate the newly created API key with the correct usage plan.

## HTTP API vs REST API
- HTTP APIs
	- **low-latency**, **cost-effective** AWS Lambda proxy, HTTP proxy APIs and private integration (no data mapping). **Only proxies**.
	- Support *OIDC* and *OAuth 2.0*
	- Authorisation, and built-in support for CORS 
	- No usage plans and API keys
- REST APIs
	- All features (**except Native OpenID Connect / OAuth 2.0**)

> [!note]- For exam perspective just remember: **HTTP API bad**
> - HTTP API is a *low cost alternative* 
> - It *only supports proxy integrations* 
> - There is no such thing as usage plan and API keys.

## CORS
- CORS must be **enabled when you receive API calls from another domain**.
	- ![[attachments/Pasted image 20220527091836.png]]
- The *OPTIONS pre-flight* request must contain the following headers:
	- `Access-Control-Allow-Methods`
	- `Access-Control-Allow-Headers`
	- `Access-Control-Allow-Origin`
- CORS can be enabled through the console
- If you using a *lambda proxy or a HTTP proxy* you will need to have `Access-Control-Allow-Origin` header in your backend code since it is the one responding when there is a preflights request.
	- ![[attachments/Pasted image 20220527092332.png]]

> [!tip]- CORS is used to *restrict access* to your APIs from **other domains**
> You team maintains a public API Gateway that is *accessed by clients from another domain*. Usage has been consistent for the last few months but recently it has more than doubled. As a result, your costs have gone up and would like to prevent other unauthorised domains from accessing your API. 
> Restrict access using CORS.

## WebSocket API
- It enables *stateful application* use cases.
- WebSocket APIs are often used in **real time applications** such as chat applications, collaboration platforms, multiplayer games, and financial trading platforms.
	- ![[attachments/Pasted image 20220527094642.png]]

- *Connecting to the API*
	- ConnectionId is stored in DynamoDB along with meta data 
	- When the client disconnects ConnectionId will be deleted from DynamoDB
	- ![[attachments/Pasted image 20220527094727.png]]
- *Client to Server Messaging*
	- User information and other meta data is retrieved from DynamoDB using the ConnectionId
	- ![[attachments/Pasted image 20220527094922.png]]
- *Server to Client Messaging*
	- ![[attachments/Pasted image 20220527094958.png]]

### Routing 
- Incoming JSON messages are **routed to different backend**
- If no routes => sent to `$default`
- You request a **route selection expression** to select the field on JSON to route from
	- See the routing expression
	- ![[attachments/Pasted image 20220527095410.png]]

> [!note] For exam perspective remember routing is there to *route to a specific backend* based on the routing expression

> [!question]- A Developer is building a WebSocket API using Amazon API Gateway. The payload sent to this API is JSON that includes an action key which can have multiple values. The Developer must integrate with different routes based on the value of the action key of the incoming JSON payload.
> Set the value of the route selection expression to `$request.body.action`.

## Architecture Example
- Create a **single interface for all the microservices** in your company
	- ![[attachments/Pasted image 20220527100357.png]]
- Use API endpoints with various resources
- Apply a simple domain name and SSL certificates
- Can apply forwarding and transformation rules at the API Gateway level