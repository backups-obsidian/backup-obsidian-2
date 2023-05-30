---
---
---
**Links**: [[111 KodeCloud Index]]

---
## High Availability
- For high availability we need multiple master nodes.
- Multiple master nodes means there will be multiple API servers.
	- API servers can work in active active configuration.
	- We would need a load balancer to balance the requests sent to the API server.
	- Diagram:
		- ![[attachments/Pasted image 20230530145709.png]]
- 