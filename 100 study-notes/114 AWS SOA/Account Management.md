---
created: 2023-03-06 11:03
updated: 2023-03-06 14:42
---
---
**Links**: [[114 AWS SOA Index]]

---
## AWS Status - Service Health Dashboard
- Shows **all regions, all services health**
- Shows *historical* information for each day.
- Has an RSS feed you can subscribe to.

## Personal Heath Dashboard
- *Global service*.
	- ![[attachments/Pasted image 20230306110440.png]]
- Show *how AWS outages directly impact you*.
- Show the **impact on your resources**.
- List issues and actions you can do to remediate them.
- Will show **maintenance events** from AWS.
- *Programmatically accessible* through the **AWS Health API**
- **Aggregations** across multiple accounts of an **AWS Organisation**.
- **Health event notifications**:
	- Use **EventBridge** (CloudWatch Events) to react to changes for AWS Health events in your AWS account
	- Example: receive email notifications when EC2 instances in your AWS account are scheduled for updates.
		- ![[attachments/Pasted image 20230306110702.png]]
- Use cases: send notifications, capture event information, take corrective action, etc.

## Billing Alarms
- Billing Alarms:
	- Billing data metric is **stored in CloudWatch us-east-1**.
		- ![[attachments/Pasted image 20230306140734.png]] 
	- Billing data are for *overall worldwide AWS costs*

> [!note]- We can also create billing alarms using [[AWS Budgets]].

## Cost Allocation Tags
- Use cost allocation tags to **track your AWS costs on a detailed level**. 
- AWS generated tags:
	- Automatically applied to the resource you create
	- Starts with Prefix `aws:` (e.g. **`aws:createdBy`**)
- User-defined tags
	- Defined by the user
	- For this to work we need to tag our resources properly
	- Starts with Prefix `user:`
		- ![[attachments/Pasted image 20230306142408.png]]

> [!question]- For accounting reasons, you need to *separate costs into categories* in AWS, such as *Environment*. How do you achieve this?
> Use Cost Allocation Tags.

## Cost & Usage Reports
- The AWS Cost & Usage Report **contains the most comprehensive** set of AWS cost and usage data available.
- Includes *additional metadata* about AWS services, pricing, and reservations (e.g., Amazon EC2 Reserved Instances (RIs))
- The AWS Cost & Usage Report lists AWS usage for each:
	- Service category used by an account
	- In hourly or daily line items
	- Any tags that you have activated for cost allocation purposes
- Can be configured for **daily exports to S3**.
- Can be *integrated with Athena*, Redshift or QuickSight.

## Cost Explorer
![[../101 AWS SAA/Other Services#Cost Explorer]]