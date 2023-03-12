---
created: 2022-05-30 14:40
updated: 2023-03-10 15:43
---
---
**Links**: [[102 AWS DVA Index]]

---
## AppSync
- AppSync is a managed service that **uses GraphQL**
	- *Single endpoint*.
- GraphQL makes it easy for applications to get exactly the data they need.
- This includes **combining data from one or more sources**
	- NoSQL data stores, Relational databases, *HTTP APIs*, etc
	- *Integrates with DynamoDB, Aurora, Elasticsearch* & others
	- *Custom sources with AWS Lambda*
	- ![[attachments/Pasted image 20220530142744.png]]
- Retrieve data in **real-time with WebSocket or MQTT on WebSocket**
- For **mobile apps**: local data access & data synchronisation.
	- Replacing cognito sync
- It all starts with uploading one GraphQL schema

> [!note] Keywords for AppSync *data synchronisation*, *mobile*, *graphql api*, *real time updates*, *WebSocket*, *offline*.

- Difference between cognito sync and AppSync:
	- Cognito sync is *not real time*.

### Security
- There are *four ways* you can authorise applications to interact with your AWS AppSync GraphQL API:
	- `API_KEY`: like we have in API Gateway
	- `AWS_IAM`: IAM *users roles / cross-account access*
	- `OPENID_CONNECT`: OpenID Connect provider / *JSON Web Token*
	- `AMAZON_COGNITO_USER_POOLS`
- For *custom domain & **HTTPS, use CloudFront** in front of AppSync*

## DataSync
![[../101 AWS SAA/DataSync & Backup#DataSync]]

## Amplify
- It is used to **create mobile and web applications**.
- It can be thought of as *ElasticBeanStalk for mobile and web applications*.
- It includes must-have features such as *data storage*, *authentication*, *storage*, and machine-learning, all powered by AWS services
	- ![[attachments/Pasted image 20220530144844.png]]
	- Front-end libraries with ready-to-use components for React.js, Vue, Javascript, iOS, Android, Flutter, etc
-  **Incorporates AWS best practices** to for reliability, security, scalability
- *Build and deploy with the Amplify CLI or Amplify Studio*
- **Components**:
	- *Amplify Studio*: **Visually** build a full-stack app, both front-end UI and a backend. 
	- *Amplify CLI*: Configure an Amplify backend with a *guided CLI* workflow
	- *Amplify Libraries*: Connect your app to *existing AWS Services* (Cognito, S3 and more) 
	- *Amplify Hosting*: A Host secure, reliable, fast web apps or websites via the AWS content delivery network.

> [!important] Amplify *deployments are powered by CloudFormation*

### Important features
- *Authentication*: `amplify add auth`
	- Leverages Amazon **Cognito**
	- You get all the features provided by cognito
- *Data store*: `amplify add api`
	- Leverages **AppSync** and **DynamoDB**
	- Work with local data and have *automatic synchronisation to the cloud* without complex code
	- Powered by *GraphQL*
	- *Offline and real-time capabilities*
	- Visual data modelling with the help of Amplify Studio
- *Hosting*: `amplify add hosting`
	- Competes with services *like netlify and vercel*.
	- CI/CD
	- Custom domain name
	- ![[attachments/Pasted image 20220530145408.png]]

## ACM (AWS Certificate Manager)
- Let's you easily **provision, manage, and deploy SSL/TLS Certificates**
- Used to provide in-flight encryption for websites (HTTPS)
- Supports both *public and private TLS certificates*
- *Automatic TLS certificate renewal*
- Integrations with (load TLS certificates on)
	- *Elastic Load Balancers*
	- *CloudFront Distributions*
	- APIs on *API Gateway*

### Difference between ACM and IAM for certificates
- *AWS Certificate Manager* (ACM) is the *preferred tool* to provision, manage, and deploy server certificates.
- In a **supported Region**, you can use ACM to manage server certificates from the console or programmatically.
- *IAM is used as a certificate manager* only when you must support HTTPS connections in a **Region that is not supported by ACM**.
- *IAM* supports *all regions*.
 
> [!note]- *IAM* supports deploying server certificates in all Regions, but you must **obtain your certificate from an external provider** for use with AWS. 
> You cannot upload an ACM certificate to IAM.

## CloudMap
- A fully managed **resource discovery service**
	- ![[attachments/Pasted image 20220530185830.png]]
	- Automatically update from v1 to v2 without any manual code change
- Creates a *map* of the backend services/resources that your applications depend on
- You register your application components, their locations, attributes, and health status with AWS Cloud Map
- *Integrated health checking* (stop sending traffic to unhealthy endpoints)
- Your applications can query AWS Cloud Map using AWS SDK, API, or DNS

## FIS (Fault Injection Simulator)
- A fully managed service for **running fault injection experiments on AWS** workloads 
	- ![[attachments/Pasted image 20220530190205.png]]
- Based on **chaos engineering**. 
- Helps you *uncover hidden bugs and performance bottlenecks*
- Use *pre-built templates* that generate the desired disruptions