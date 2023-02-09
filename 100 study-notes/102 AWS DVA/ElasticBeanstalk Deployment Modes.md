---
created: 2022-05-16 11:56
updated: 2023-02-09 15:02
---
---
**Links**: [[102 AWS DVA Index]]

---
## Deployment Modes
- *Single instance deployment* is great for *development environment* whereas *HA* with load balancer is great for *prod environments*.
	- ![[attachments/Pasted image 20220516115626.png]]
- For deployments we *upload zip files*.

### All at once
- Everything is *deployed in one go*.
	- ![[attachments/Pasted image 20220516120051.png]]
	- Grey means they were stopped.
- It is the **fastest**.
- In this update application *won't be running* during the update. This means application has **downtime**.
- Great for *quick iterations in development environment*.
- **No additional cost**. 

> [!caution] This is the only deployment which has downtime.

### Rolling
- In this update application will be running **below capacity** during the update.
	- ![[attachments/Pasted image 20220516120345.png]]
- We *can set the bucket size*.
	- If you set a *small bucket size* and you have a lot of instances then it will be a very *long deployment*.
- During *some point* in the update the application will be **running both versions simultaneously**.
- **No additional cost**.

### Rolling with additional batches
- In this update application will be running **at capacity** during the update.
	- ![[attachments/Pasted image 20220516120807.png]]
- In this also we *can set the bucket size*.
- During *some point* in the update the application will be **running both versions simultaneously**.
- **Small additional cost**.
- Additional batch is removed at the end of the deployment.
- *Longer deployment*.
- **Good for production**.

### Immutable
- New Code is deployed to *new instances* on a temporary ASG.
	- At first in the new ASG only one instance is launched to see if it works and passes the health check. If all the health checks are passed then it launches the other instances.
	- ![[attachments/Pasted image 20220516121152.png]]

- **Zero downtime**.
- **High cost, double capacity**.
- *Longest deployment*.
- **Quick rollback in case of failures** (just terminate new ASG)
- **Great for production**.
- **New ASG is created**.

### Blue/Green
- **Not a direct feature** of Elastic Beanstalk
- **Zero downtime** and release facility.
- *Create a new "stage" environment* and deploy v2 there.
	- ![[attachments/Pasted image 20220516121842.png]]
- The new environment (green) can be validated independently and roll back if there are issues.
- *Route 53* can be setup using *weighted policies* to redirect a little bit of traffic to the stage environment.
- Using Beanstalk, **swap URLs** when done with the environment test.

> [!note] All in all it is a **very manual process**.

> [!caution] This is the only deployment with DNS change.

### Canary Testing
- New application version is deployed to a *temporary ASG with the same capacity*.
	- ![[attachments/Pasted image 20220516122253.png]]
- A *small % of traffic is sent to the temporary ASG* for a configurable amount of time
- Deployment health is monitored
- If there's a deployment failure, this triggers an **automated rollback (very quick)**
- **No application downtime**
- New instances are migrated from the temporary to the original ASG Old application version is then terminated.

> [!tip]- Difference between Canary testing and Blue/Green deployment strategies.
> - Fully automated
> - Uses ALBs for weighted routing instead of Route53

## Summary
![[attachments/Pasted image 20220516122709.png]]

> [!note]- Deployment modes of **immutable** and **traffic splitting** (blue/green or canary) are very expensive.