---
created: 2023-03-02 09:55
updated: 2023-03-02 11:31
---
---
**Links**: [[114 AWS SOA Index]]

---
## EC2 Burstable Instances
- **T2/T3** family.
- When the machine needs to process something unexpected (a spike in load for example), it can burst, and CPU can be VERY good. 
	- *Burst credits are accumulated over time* while your instance is running.
		- ![[attachments/Pasted image 20230302100507.png]]
	- If the machine bursts, it *utilises burst credits*.
	- If the burst credits are utilised the CPU becomes very bad, the CPU utilisation drops.
		- ![[attachments/Pasted image 20230302100700.png]]
- Burstable instances can be amazing to **handle unexpected traffic** and getting the insurance that it will be handled correctly.
- If your instance **consistently runs low on credit**, you need to move to a *different kind of non-burstable instance*.
	- Choosing and instance with higher vCPU.
- Different instances earn credits at different rates.

### T2/T3 Unlimited
- It is possible to have an **unlimited burst credit balance**.
- You **pay extra money** if you go over your credit balance, *but you don't lose in performance*.
- Be careful, *costs could go high* if you're not monitoring the CPU health of your instances.

> [!note]- We *CANNOT buy CPU credits*. 
> So if we are running out of credits then our options are:
> - Turn on *t\* Unlimited*.
> - Upgrade to *non-t\* type of EC2 instance*.
> - Upgrade the EC2 instance type to t2.medium or higher.