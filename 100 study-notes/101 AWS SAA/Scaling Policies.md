---
created: 2022-04-20 19:48
updated: 2023-03-03 08:46
---
---
**Links**: [[101 AWS SAA Index]]

---
## Dynamic Scaling Policies
- It is of 3 types: **target tracking**, **step** and **simple**

> [!note]- If you have multiple scaling policies and multiple of them are met at the same time then
> Amazon EC2 Auto Scaling chooses the *policy that provides the largest capacity*.

### Target Tracking
- It increases or decreases the current capacity of the group **based on a target value** for a specific metric. 
- It is similar to the way that your *thermostat maintains the temperature of your home* – you select a temperature and the thermostat does the rest.

> [!example] I want the average ASG CPU to stay at around 40%

- If you are scaling based on a utilisation metric that increases or decreases proportionally to the number of instances in an Auto Scaling group, then it is recommended that you use target tracking scaling policies. Otherwise, it is better to use step scaling policies instead.

- Alarms are automatically created by target tracking policy.
	- One for scaling in and other for scaling out.

### Step Scaling
- It increases or decreases the current capacity of the group based on a **set of scaling adjustments**, known as *step adjustments*, that vary based on the size of the alarm breach.
- We have instance warmup time in step scaling.

> [!info] Here the important term is **set**. This is what separates it from simple scaling policy.

- You will have to setup *how many units you want to add or remove*.
	- ![[attachments/Pasted image 20220420195330.png]]

### Simple Scaling
- It increases or decreases the current capacity of the group based **on a single scaling adjustment**.
- You will have to setup *how many units you want to add or remove*.

## Scheduled Scaling
- Anticipate a scaling a **based on known usage patterns**. Example: increase the min capacity to 10 at 5 pm on Fridays.
- A scheduled action sets the **minimum**, **maximum**, and **desired sizes** at the time of creating a scheduled action.

> [!info] We use *min* and *max* value *when we want to specify a range*. If you *know* the *number of instances* that will be required then go with the *desired sized option*.

## Forecasted Scaling
- **ML Driven**
- **Predictive scaling** to continuously forecast load and schedule scaling ahead.
- Requires some data initially to start predicting.

## Scaling Cooldowns
- There is a **default scaling cool down (300s)** after every scaling event in order to **stabilise the metrics**.
- *No new instances are **launched** or **terminated** in the cool down period*.

> [!tip] Use a *ready-to-use AMI* to reduce configuration time in order to be serving request fasters and reduce the cooldown period.

## Predefined metrics for EC2 scaling
- **CPUUtilisation**: Average CPU utilisation across your instances
- **RequestCountPerTarget**: to make sure the *number of requests per EC2* instances is stable.
- **Average Network In / Out**: if you're application is *network bound*