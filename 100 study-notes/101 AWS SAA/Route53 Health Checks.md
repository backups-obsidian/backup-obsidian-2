---
created: 2022-04-19 16:22
updated: 2022-04-22 20:52
---
---
**Links**: [[101 AWS SAA Index]]

---
## Health Checks
> [!question]- Why use health checks in Route53
> - Suppose we have ALBs in two different regions and we are using Route 53 latency based routing policy to route users to the ALBs.
> - Health checks help us in avoiding routing to ALBs which are down.

- Health checks are **only for public resources.** It is possible to monitor *private resources using CW metrics*.
- *All the health checkers are hosted outside the VPC*. So they cannot access private end points.
- Health checks enable **automated DNS failover**.
- We can do [[Route53 Routing Policies#Active Active Failover|active active]] or [[Route53 Routing Policies#Active Passive Failover|active passive]] failovers using health checks.
- Health checks can be **different types**: 
	- Health checks that monitor an *endpoint*: application, server, other AWS resource 
	- Health checks that monitor *other health checks*: Calculated Health Checks
	- Health checks that monitor *CloudWatch Alarms*: full control e.g., throttles of Dynamo DB, alarms on RDS, custom metrics, (**helpful for private resources**)

### Endpoint 
-   Health checker comes from all the end points. There are **15 global health checkers**.
-   We **can choose which health checkers we want** out of 15.
-   Supports *HTTP, HTTPS, TCP*.
-   We can change the **threshold** for healthy/unhealthy check (default is **3**)
-   **Interval** is **30s** but we can opt for **10s** health check known as **faster health check** but it is **costlier**.
-   If health checkers > **18%** then Route53 considers it **healthy**. Otherwise unhealthy.
-   Health checks can only pass if the status code is **2xx** or **3xx**.
-   If it is a **text based response** it **can check** first **5120 bytes** of the response.

> [!caution] The **security groups** in front of the **ALBs** must **allow** the **IP ranges** of Route53 health checkers from which the **health checks** are coming in.

### Calculated health checks
-   **Combines the results of multiple health checks** into a single health check.
-   **Use case** is to *perform maintenance of website without causing all health checks to fail.*
-   We can use **AND**, **OR** and **NOT**.
- Can monitor up to 256 Child Health Checks
- Specify how many of the health checks need to pass to make the parent pass

### CW metrics
- Generally used for **private hosted zones** or if you want full control over you health checks.

> [!question]- Why do we need CW metrics for private hosted zones?
> Route 53 health checkers are outside the VPC. They can't access private endpoints (private VPC or on-premises resource)
> 
> ---
> You can create a CloudWatch Metric and associate a CloudWatch Alarm, then create a *Health Check that checks the alarm* itself.