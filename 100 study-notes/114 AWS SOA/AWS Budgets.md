---
created: 2023-03-06 14:12
updated: 2023-03-16 08:32
---
---
**Links**: [[114 AWS SOA Index]]

---
## AWS Budgets
- **Create budget** and **send alarms when costs exceeds the budget**.
- *4* types of budgets: **Usage**, **Cost**, **Reservation**, **Savings Plans**
	- So AWS budgets is not just limited to cost.
- We can have *forecasts*.
- For Reserved Instances (RI)
	- Track utilisation
	- Supports EC2, ElastiCache, RDS, Redshift
- Up to 5 SNS notifications per budget
- Can filter by: Service, Linked Account, Tag, Purchase Option, Instance Type, Region, Availability Zone, API Operation, etc...
- Same options as AWS Cost Explorer, but *more granularity*.
- 2 budgets are free, then $0.02/day/budget.

> [!question]- A company is developing some new applications in a development account. The costs associated with the account have been rising management acre concerned. A SysOps Administrator has been tasked with configuring **alerts** that will notify management when the accounts’ costs or usage are **forecasted** to exceed a specified spending amount. Which AWS service should the Administrator use?
> AWS Budgets.
> If you see **alerts in questions go for AWS Budgets over AWS Cost Explorer**.
> ---
> AWS Cost Explorer is incorrect. AWS Cost Explorer does provide forecasting, but AWS Budgets should be used for alerting for specific spending budgets.