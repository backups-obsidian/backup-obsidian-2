---
created: 2023-03-06 14:12
updated: 2023-03-06 14:17
---
---
**Links**: [[114 AWS SOA Index]]

---
## AWS Budgets
- **Create budget** and **send alarms when costs exceeds the budget**.
- *4* types of budgets: Usage, Cost, Reservation, Savings Plans
- For Reserved Instances (RI)
	- Track utilisation
	- Supports EC2, ElastiCache, RDS, Redshift
- Up to 5 SNS notifications per budget
- Can filter by: Service, Linked Account, Tag, Purchase Option, Instance Type, Region, Availability Zone, API Operation, etc...
- Same options as AWS Cost Explorer, but *more granularity*.
- 2 budgets are free, then $0.02/day/budget.