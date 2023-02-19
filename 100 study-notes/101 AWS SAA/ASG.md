---
created: 2022-04-20 19:15
updated: 2023-02-19 09:57
---
---
**Links**: [[101 AWS SAA Index]]

---
## ASG
- Scale in removing, scale out adding.
- We can specify the **minimum**, **desired** and **maximum** number of EC2 instances running in an ASG. If desired == maximum then alarms won't add any new EC2 instances.
- **Desired capacity** can be used to **manually scale** an ASG. 
	- Minimum and maximum capacity only specify the limits.
- We can *automatically register new instances to the load balancer*. ASGs and Load Balancers work hand in hand.
- To launch new instances we provide [[Launch Configuration-Template]]. In this launch configuration/template we provide details like AMI type, volume size etc.
- ASGs are free to use.
- It works with both ALBs and NLBs.

> [!caution] A role applied to ASG will be applied to all the EC2 instances in the ASG.

> [!caution] If you have an EC2 Auto Scaling group (ASG) with running instances and you choose to **delete the ASG**, the *instances will be terminated and the ASG will be deleted*.

- Having instances under an ASG means that if for some reason they get terminated then ASG will automatically create new ones for replacement.

> [!info]- If EC2 instances are *marked unhealthy* by the load balancer then they are **terminated** by ASG and then relaunched.
> Instances are not restarted but terminated.

- We **can add running instances to an ASG**.

> [!important]- You *can edit ASGs* after they have been created but you *cannot edit launch configurations*. You *can edit launch templates*.
> So if you want your instances to use a new AMI and they are using launch configuration then you can just create a new launch configuration and update the ASG.

- While creating an ASG we have to specify the subnets. Make sure you **specify all the subnets**. Otherwise EC2 instances will only be balanced in the subnets provided and it won't be HA.

> [!question]- What happens if a scaling activity causes me to reach my Amazon EC2 limit of instances?
> Amazon EC2 Auto Scaling cannot scale past the Amazon EC2 limit of instances that you can run

> [!question]- If I have data installed in an EC2 Auto Scaling group, and a new instance is dynamically created later, *is the data copied over to the new instances*?
> Data is *not automatically copied* from existing instances to new instances. You can use lifecycle hooks to copy the data.

## Health Checks
- If *using an ELB* it is best to **enable ELB health checks** as otherwise *EC2 status checks may show an instance as being healthy that the ELB has determined is unhealthy*. In this case the *instance will be removed from service by the ELB but will not be terminated by Auto Scaling*.
	- This can happen when the *Auto Scaling group is using EC2 based health check* and the *Application Load Balancer is using ALB based health check*.

-   By **default** uses **EC2 status checks**.
-   **Can also use ELB health checks and custom health checks**.
-   **ELB health checks are in addition to the EC2 status checks**.
-   If any health check returns an **unhealthy** status the **instance will be terminated**.
-   With ELB an instance is marked as *unhealthy* if ELB reports it as *OutOfService*
-   A *healthy* instance enters the *InService* state.
-   If connection draining is enabled, Auto Scaling waits for in-flight requests to complete or timeout before terminating instances.
-   **The health check grace period allows a period of time for a new instance to warm up before performing a health check (300 seconds by default**).

## Auto Scaling Alarms
- These alarms trigger scaling.
- We *can scale ASGs based on CloudWatch alarms*. We need 2 alarms, one for scaling in and other for scaling out. 
- A monitoring unit for CloudWatch alarms can be CPU usage. *Metrics are the **average** of all the ASG instances*.
- We can define *better auto scaling rules* that are **directly managed by EC2**. These rules are easier to set than the CloudWatch alarms. Some of these rules include average CPU usage, network in/out etc.
- We can also auto scale on a **custom metric** (ex no of connected users). We send the parameter of our custom metric to cloud watch alarms via the **PutMetric API**. Now we can create cloud watch alarms with this metric to scale in or out. So *ASG is not just limited to the metrics AWS exposes*.

## Rebalancing and Scaling
- Actions like explicitly terminating an instance can lead to an *ASG becoming unbalanced across an ASG*.
- Amazon EC2 Auto Scaling compensates by rebalancing the Availability Zones.

> [!note] When rebalancing, Amazon EC2 Auto Scaling **launches new instances before terminating the old ones**, so that rebalancing does not compromise the performance or availability of your application.

- **Scaling activity** of Auto Scaling *works in a different sequence compared to the rebalancing activity*. 

> [!note]+ Auto Scaling creates a new scaling activity for terminating the unhealthy instance and then terminates it. Later, another scaling activity launches a new instance to replace the terminated instance.
> **First Termination then new instances**.

### Impaired Status
- If any health check returns an **unhealthy status** the instance will be **terminated**. 
- For the **impaired status**, the *ASG will wait a few minutes to see if the instance recovers before taking action*. 
- If the “impaired” status *persists, termination occurs*. 
 
> [!note]- Unlike AZ rebalancing and *just like scaling*, **termination of unhealthy instances happens first**, then Auto Scaling attempts to launch new instances to replace terminated instances.

## Suspending Scaling 
- You can **suspend and then resume** one or more of the **scaling processes** for your Auto Scaling group. This is done using `SuspendProcesses` and `ResumeProcesses` API. 
- This can be useful when you want to *investigate a configuration problem or other issue* with your web application and then *make changes to your application, without invoking the scaling processes*. 

## Standby State
- You can manually move an **instance** from an ASG and put it in the **standby state**. In general it is in *InService* state.
- *Instances in standby state* are still managed by Auto Scaling, are charged as normal, and do not count towards available EC2 instance for workload/application use i.e. they **do not handle application traffic**. 
- Auto scaling **does not perform health checks on instances in the standby state**. 
- *Standby state* can be used for *performing updates/changes/troubleshooting etc without health checks being performed or replacement instances being launched*.
- Suspending scaling and standby state go hand in hand for *troubleshooting an ASG*.