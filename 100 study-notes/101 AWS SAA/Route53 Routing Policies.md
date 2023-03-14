---
created: 2022-04-19 16:22
updated: 2023-03-13 09:22
---
---
**Links**: [[101 AWS SAA Index]]

---
- Routing policies define how Route53 *should respond to DNS queries*. 
	- It doesn't route any traffic it just responds to DNS queries in a specific way depending on the policy.

> [!info] All routing policies have health checks except simple.

## Simple 
- It will route traffic to a **single resource**.
- We can specify **multiple values** in the **same record**.
	- **If multiple values** are returned then a **random one** is chosen by the **client**.
		- ![[attachments/Pasted image 20230307095400.png]]
		- ![[attachments/Pasted image 20230307095332.png]]
- It **cannot** be associated with **health checks**
- When **ALIAS** is enabled it can point to **only 1 AWS resource**.

## Multi value
- It is used when you want to route traffic to **multiple resources**
- Can be associated with health checks (return **only values with positive health checks**)
- Multi value is **NOT a substitute for ELB**.
	- It is *client side load balancing*.
- Upto to **8 healthy records are returned** by each multi value query.
- It is a *better version of simple type since it only returns the IPs which are healthy*.

## Weighted
- Control the **% of request** that go to each resource.
- Each record is assigned a relative weight
- The weights *need not sum to 100*.
- We can associate them with **health checks**.
- Assigning a **weight of 0** means we will **stop sending traffic** to that particular resource.
- Whereas if **all records** have a **weight of 0** then **all the records will be returned equally**.
- Use case would be to *load balance between different regions* or *test the new application version*.
- We also use *weighted routing* for active active failover and active passive failover. 

## Latency based
- **Redirect** to resource which has the **lowest latency**.

> [!important] Useful when latency for users is a priority.

- Latency is based on traffic between users and AWS regions.
- Germany users may be redirected to resource in US if it has the lowest latency.
- It can be combined with **health checks**.

> [!tip]+ If you see terms like *latency*, *high load times*, *deteriorated performance* then always go for latency based routing.
> You can use *geolocation routing to restrict the distribution of content* to only the locations in which you have distribution rights. You *cannot use geolocation routing to reduce latency* even though you might think it makes sense to serve US users from US region.

> [!caution] If your application is *deployed in only a single region* and you want to reduce latency then *Latency based routing will be of no use*.

## Failover
- **Health check is mandatory**.
- We need 2 different records: a *primary record* and a *secondary record*.
- DNS failover to a standby instance
	- ![[attachments/Pasted image 20220422194449.png]]

### Active Active Failover
- Use this failover configuration when you want *all of your resources to be available the majority of the time*. 

### Active Passive Failover
- Use an active-passive failover configuration when you want a primary resource or group of resources to be available the majority of the time and you want a *secondary resource or group of resources to be on standby* in case all the primary resources become unavailable. 

## Geolocation
-  Routing is based on **user location**.
-   There should be a **default record** **if** there is **no match on location**.
-  **Use cases** would be *website localisation (NETFLIX)*, *restrict content distribution*, load balancing etc
-   It can be associated with **health checks**.
-   We can specify **location by** 
	- *country*
	- *continent*
	- **US state**

> [!tip] Mainly for *content restriction*, *distribution rights*.

## Geoproximity
-   Route traffic to resources **based on the geographic location of users and the resources**
- We can shift more traffic to resources based on **bias**.
	- ![[attachments/Pasted image 20220422195232.png]]
- We must use Route 53 **Traffic Flow** (advanced) to use this feature. 
	- We can setup very advanced rules.
	- Traffic Flow supports *versioning*.
	- It will be saved as traffic flow policies.
- It is really helpful if we need to *shift the traffic from one region to another* by changing the bias.

> [!question]- Trick question to confuse between Geolocation and Geoproximity
> A company runs a messaging application in the `ap-northeast-1` and `ap-southeast-2` region. A Solutions Architect needs to create a routing policy wherein a **larger portion of traffic from the Philippines and North India** will be routed to the resource in the `ap-northeast-1` region.
> 
> We are not specifying complete countries but only a portion. This is a perfect use case of *bias* hence *geoproximity*.
> 
> ---
> ![[attachments/Pasted image 20220422195700.png]]

- Use Geoproximity if you want to route *based on the location of resources*.
