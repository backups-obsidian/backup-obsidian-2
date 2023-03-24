---
created: 2023-03-03 08:57
updated: 2023-03-22 08:20
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
	- GroupTerminatingInstances, GroupTotalInstances
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

## Questions
> [!question]- You want a small website on EC2 instances under an ASG that has a target size varying between 2 and 10 instances. Your ASG has a policy to scale out when your target CPU Utilization is above 75%. It has been over 3 hours that the CPU Utilization of your ASG is 90% and still, *no scaling out actions have taken place*. What are the most likely reasons for this?
> - Your ASG is at *maximum capacity* already.
> - Your ASG *Launch process has been suspended*. 
> ---
> - For Amazon EC2 Auto Scaling, there are *two primary process types*: **Launch** and **Terminate**. The Launch process adds a new Amazon EC2 instance to an Auto Scaling group, increasing its capacity. The Terminate process removes an Amazon EC2 instance from the group, decreasing its capacity.
> - *If the Launch process is suspended, then your Auto Scaling group does not scale out for alarms or any scheduled actions that occur*.

