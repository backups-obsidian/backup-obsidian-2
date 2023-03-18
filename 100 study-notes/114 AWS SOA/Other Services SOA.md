---
created: 2023-03-06 14:09
updated: 2023-03-18 08:25
---
---
**Links**: [[114 AWS SOA Index]]

---
## Elastic OpenSearch / ElasticSearch (ES)
- *Managed version of ElasticSearch* (open source project)
- Needs to run on servers (*not a serverless offering*)
- Use cases:
	- *Log Analytics*
	- Real Time application monitoring
	- Security Analytics
	- *Full Text Search*
	- ClickStream analytics
	- Indexing
- Common pattern with DynamoDB
	- ![[attachments/Pasted image 20230307140309.png]]

### Access Policy
- *To access ElasticSearch we define access policies*.
- Access policy is similar to S3 resource based policy.
- **IP-based Policies**
	- **Resource-based policies** used to restrict access to an ES domain to IP address(es) or CIDR blocks
	- Allows unsigned requests to an *ES domain* (e.g., curl, Kibana, etc)
- 2 different ways of accessing ES:
	- We can use *EC2 instances in a private subnet* and *sign the requests with sigV4*.
	- We can create *instances in the public subnet* and use it as a *proxy combined with IP based policies* for accessing ES.
	- ![[attachments/Pasted image 20230307140857.png]] 
- When we *create an ES domain*, we specify whether it should have a *public endpoint* or *reside within a private subnet in a VPC*. 
	- Once created, **we CANNOT switch from one ES domain to the other**. 
	- Instead, you *must create a new domain* and either manually reindex or *migrate your data*.

### Kibana Authentication
- Kibana *DOESN'T natively support lAM users and roles*
- You can control access to Kibana using:
	- **HTTP Basic Authentication**:
		- Internal user database stored in an ElasticSearch Index
	- **SAML**:
		- Use existing *3rd party identity providers* to log into Kibana
		- Supports *SAML 2.0* (e.g., Active Directory Federation Services ADFS, Auth0, etc)
	- **Amazon Cognito**:
		- Easy for *MS Active Directory (AD) integration*

### Production Setup
- It's recommended to:
	- Use **3 dedicated Master nodes**
	- Use **at least 2 Data nodes per AZ** (for replication)
	- Deploy the domain **across 3 Availability Zones**
	- Create at least one replica for each index in the cluster

- Diagram:
	- ![[attachments/Pasted image 20230307141311.png]]

## Amplify
- *Just an overview NOT detailed*.
- **Web and mobile applications**. 

![[../102 AWS DVA/Other Services DVA#Amplify]]