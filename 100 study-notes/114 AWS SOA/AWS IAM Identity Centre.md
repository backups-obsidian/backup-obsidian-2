---
created: 2023-03-07 08:52
updated: 2023-03-13 09:07
---
---
**Links**: [[114 AWS SOA Index]]

---
## IAM Identity Centre
- It is a *successor to AWS SSO*.
- **One login** (single sign-on) for all your
	- *AWS accounts* in AWS Organisations
	- *Business cloud applications* (e.g., Salesforce, Box, Microsoft 365, etc)
	- *SAML2.0*-enabled applications
	- EC2 Windows Instances
- Identity providers
	- *Built-in identity store in IAM Identity Center*.
	- *3rd party*: **Active Directory (AD)**, OneLogin, Okta, etc.
- Use it if you have multiple AWS accounts.
- Diagram:
	- ![[attachments/Pasted image 20230307085644.png]]

### Fine grained permissions & Assignments
- **Multi-Account Permissions**:
	- *Manage access across AWS accounts in your AWS Organisation*.
	- **Permission Sets** - a collection of one or more IAM Policies assigned to users and groups to define AWS access.
		- ![[attachments/Pasted image 20230307085957.png]]
- **Application Assignments**
	- *SSO access to many SAML 2.0 business applications* (Salesforce, Box, Microsoft 365, etc)
	- Provide required URLs, certificates, and metadata.
- **Attribute-Based Access Control (ABAC)**
	- *Fine-grained permissions based on users' attributes stored in IAM Identity Centre Identity Store*.
	- Example: cost centre, title, locale, etc.
	- Use case: Define permissions once, then modify AWS access by changing the attributes.

> [!question]- A company is deploying AWS Single Sign-On (SSO). A SysOps Administrator has created an AWS SSO directory in an AWS Organisations master account and enabled full access. What is the next step to configure the single sign-on functionality?
> **Create permission sets in AWS SSO** and associate the permission sets with Directory Service users or groups.