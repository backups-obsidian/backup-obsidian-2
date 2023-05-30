---
created: 2023-05-30 15:00
updated: 2023-05-30 15:06
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
- For HA Etcd can be deployed in **stacked topology** or **external etcd topology**.
	- Stacked topology:
		- ![[attachments/Pasted image 20230530150322.png]]
	- External etcd topology (*separate from the master node*):
		- ![[attachments/Pasted image 20230530150305.png]]

> [!note]- Since *etcd is a distributed system* apiserver can talk to any node of etcd without a load balancer.
> This is the reason why we specify a list of etcd servers in the kubeapi configuration file.
> ![[attachments/Pasted image 20230530150539.png]]