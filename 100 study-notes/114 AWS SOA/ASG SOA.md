---
created: 2023-03-03 08:57
updated: 2023-03-03 09:05
---
---
**Links**: [[114 AWS SOA Index]]

---
## Troubleshooting
- x number of instances instance(s) are already running. *Launching EC2 instance failed*.
	- The *Auto Scaling group has reached the limit set by the Maximum Capacity parameter*. 
	- Update your Auto Scaling group by providing a new value for the maximum capacity.
- *Launching EC2 instances is failing*:
	- The *security group does not exist*. SG might have been deleted.
	- The *key pair does not exist*. The key pair might have been deleted.

## CloudWatch Metrics
- Metrics are *collected every 1 minute*
- ASG-level metrics: (opt-in)
	- GroupMinSize, GroupMaxSize, GroupDesiredCapacity
	- GrouplnServicelnstances, GroupPendingInstances, GroupStandbyInstances
	- Group TerminatingInstances, Group TotalInstances
- We should *enable metric collection* to see these metrics

## AWS AutoScaling
> [!note]- **AWS Autoscaling** is the *backbone service* of auto scaling for all scalable resources in AWS
> Example: ASG, ECS, Aurora, DynamoDB (WCU & RCU)

- It also has a separate UI.

### Scaling plans
- **Dynamic scaling**: creates a target tracking scaling policy
	- *Optimise for availability*: 40% of resource utilisation
	- *Balance availability and cost*: 50% of resource utilisation
	- *Optimise for cost*: 70% of resource utilisation
	- Custom => choose own metric and target value

- Predictive scaling: continuously forecast load and schedule scaling ahead