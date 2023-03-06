---
created: 2023-03-06 12:28
updated: 2023-03-06 13:00
---
---
**Links**: [[114 AWS SOA Index]]

---
## Service Catalog
- *Users* that are *new* to AWS have too many options, and *may create **stacks** that are not compliant with the rest of the organisation*.
- Some users just want a quick self-service portal to launch a set of authorised products **pre-defined by admins**.
	- Includes: virtual machines, databases, storage options, etc
- We *allow users to pick and choose from a CloudFormation template*. 
- **Product**: *CloudFormation templates*.
- **Portfolio**: *Collection of products*.

### Sharing Catalogs
- Share portfolios with individual AWS accounts or AWS Organisations
- Sharing options:
	- *Share a reference* of the portfolio, then *import* the shared portfolio in the recipient account (**stays in-sync with the original portfolio**)
	- *Deploy a copy* of the portfolio into the recipient account (**must re-deploy any updates**)
- Ability to add products from the imported portfolio to the local portfolio.

### TagOptions Library
- Easily *manage tags on provisioned products*
- TagOption:
	- Key-value pair managed in AWS Service Catalog
	- Used to create an AWS Tag
- Can be associated with Portfolios and Products
- Use cases: *proper resources tagging*, defined allowed tags, etc.
- Can be shared with other AWS accounts and AWS Organisations.