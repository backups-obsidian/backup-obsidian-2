---
created: 2023-05-30 15:00
updated: 2023-05-31 11:59
---
---
**Links**: [[111 KodeCloud Index]]

---
## High Availability
- For high availability we need multiple master nodes.
- Multiple master nodes means there will be multiple API servers.
	- *API servers can work in active active configuration*.
	- We would need a load balancer to balance the requests sent to the API server.
	- Diagram:
		- ![[attachments/Pasted image 20230530145709.png]]
- For the *controller manager and scheduler they CANNOT run in active active configuration* i.e. multiple instances must not run in parallel.
	- They run in an *active standby mode*.
	- To *achieve the active and standby node leader election process is used*.