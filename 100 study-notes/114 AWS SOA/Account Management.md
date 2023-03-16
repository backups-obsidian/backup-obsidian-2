---
created: 2023-03-06 11:03
updated: 2023-03-16 08:40
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

> [!question]- A media company runs its business on Amazon EC2 instances backed by Amazon S3 storage. The company is apprehensive about the consistent increase in costs incurred from S3 buckets. The company wants to make some decisions regarding data retention, storage, and deletion based on S3 usage and cost reports. As a SysOps Administrator, you have been hired to develop *a solution to track the costs incurred by each S3 bucket in the AWS account*. How will you configure this requirement?
> Add a common tag to each bucket. Activate the tag as a *cost allocation tag*. Use the AWS Cost Explorer to create a cost report for the tag.

> [!question]- A manager in a company needs to see a breakdown of costs in an AWS account on a *project by project basis*. *The manager would like to view this information in AWS Cost Explorer*. Which combination of configuration updates should be applied?
> - Activate cost allocation tags
> - Create and apply resource tags: In addition to activating cost allocation tags, the actual resource tags must be created and applied to the relevant resources.

## Cost & Usage Reports
- The AWS Cost & Usage Report **contains the most comprehensive** set of AWS cost and usage data available.
- Includes *additional metadata* about AWS services, pricing, and **reservations** (e.g., Amazon EC2 Reserved Instances (**RIs**))
- The AWS Cost & Usage Report lists AWS usage for each:
	- Service category used by an account
	- In hourly or daily line items
	- Any tags that you have activated for cost allocation purposes
- Can be configured for **daily exports to S3**.
- Can be *integrated with Athena*, Redshift or QuickSight.
- We can **calculate savings** using Cost & Usage reports.

> [!question]- A company needs to *track* the *allocation of Reserved instance discounts in the company’s consolidated bill*. Which AWS tool can be used to find this information?
> AWS Cost and Usage report
> ---
>  "AWS Budgets" is incorrect. This tool is used for alerting on spending limits, it does not *provide this level of reporting*.

## Cost Explorer
![[../101 AWS SAA/Other Services#Cost Explorer]]

> [!question]- A company uses AWS Organisations with consolidated billing for several AWS accounts. The CEO is concerned about rising costs and has asked a SysOps Administrator to determine what is causing the increase. What is the MOST comprehensive **tool** that will accomplish this task?
> Cost Explorer.
> ---
> Cost Allocation Tags are **NOT a tool**.