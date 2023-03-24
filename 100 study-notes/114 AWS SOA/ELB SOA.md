---
created: 2023-03-03 08:18
updated: 2023-03-22 08:24
---
---
**Links**: [[114 AWS SOA Index]]

---
## Target group settings
- `deregisteration_delay.timeout_seconds`: time the load balancer waits before deregistering a target
- `slow_start.duration_seconds`: duration of cold start
- `load_balancing.algorithm.type`: how the load balancer selects targets when routing requests (*Round Robin, Least Outstanding Requests*)
- `stickiness.enabled`
- `stickiness.type`: *application-based* or *duration-based cookie*
- `stickiness.app_cookie.cookie_name`: name of the application cookie
- `stickiness.app_cookie.duration_seconds`: *application-based* cookie expiration period
- `stickiness.lb_cookie.duration_seconds`: *duration-based* cookie expiration period

### Slow start mode
- By *default*, a target receives its **full share of requests** once it's registered with the target group.
- Slow Start Mode **gives healthy targets time to warm-up** before the load balancer sends them a full share of requests.
- The **load balancer linearly increases the number of requests** that it sends to the target
- A target exits Slow Start Mode when:
	- The duration period elapses
	- The target becomes unhealthy
- To **disable**, set *Slow start duration* value to **O**

### Request routing algorithms
- **Least Outstanding requests**:
	- The *next instance* to receive the request is the **instance that has the lowest number of pending/unfinished requests**.
	- Works with *Application Load Balancer* and Classic Load Balancer (HTTP/HTTPS)
	- *CANNOT be combined with slow start*.
- **Round Robin**:
	- **Equally choose the targets** from the target group.
	- Works with *Application Load Balancer* and Classic Load Balancer (TCP)
- **Flow Hash**: 
	- Only for **NLB**.
	- Selects a target based on the *protocol*, source/destination *IP* address, source/destination *port*, and *TCP sequence number*.
		 - ![[attachments/Pasted image 20230303082635.png]]
	- Each TCP/UDP connection is *routed to a single target for the life of the connection*.

## Rules
- *Rules are setup on the load balancer* and we can have *many rules*.
- Rules are **processed in order** and *default rule is processed at the last*.
- Supported Actions (*forward*, *redirect*, *fixed-response*)
- Rule *conditions* (same as different types of routing in ALB): host-header, http-request-method, path-pattern, source-ip, http-header, query-string
- For a **single rule we can have multiple target groups** and we can specify the *weighting of each group*.
	- Example: multiple versions of your app, *blue/green deployment*

## Miscellaneous
- In certain scenarios, such as when flash traffic is expected, or in the case where a load test cannot be configured to gradually increase traffic, **AWS recommends that you contact AWS to have your load balancer "pre-warmed"**. 
	- AWS will then configure the load balancer to have the appropriate level of capacity based on the traffic that you expect.