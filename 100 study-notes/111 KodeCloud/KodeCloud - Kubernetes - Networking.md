---
created: 2023-05-16 07:48
updated: 2023-05-16 07:48
---
---
**Links**: [[111 KodeCloud Index]]

---
## Networking
> [!tip]- In Docker an IP address is assigned to a container whereas in k8s **IP address is always assigned to a pod**.

- When k8s is initially configured an *internal private network* is configured with the address range of `10.244.0.0` and all the pods are attached to it.
	- When we deploy multiple pods they are assigned a specific IP address from this address.
	- The pods can communicate each other through this IP. 
		- Not the ideal way of establishing communication since the IP addresses can change when pods are recreated.
	- ![[attachments/Pasted image 20220921215907.png]]

- How does networking work in a cluster (multiple nodes)?
- From the above example if we have 2 nodes which are not a part of a cluster they will have the same IP address range.
	- ![[attachments/Pasted image 20220921220137.png]]
	- This isn't going to work
- When a k8s cluster is setup it doesn't automatically setup a network to handle networking.
- **K8s expects us to setup networking to meet certain requirements**.
	- Kubernetes dictates the networking requirements and it is the responsibility of **CNI** *(plugins)* to ensure that these requirements are met.
- These *requirements* are: 
	- *All containers/PODs can communicate to one another* **WITHOUT NAT**.
	- *All nodes can communicate with all containers and vice-versa* **WITHOUT NAT**.

- Some *CNI plugins* are:
	- ![[attachments/Pasted image 20220921220612.png]]

- Once setup they are responsible for managing the networking.
	- It assigns different network address for each network in the node thereby creating a virtual  network.
	- Notice that *different nodes have different network ranges*.
	- ![[attachments/Pasted image 20220921220832.png]]

- Different ways of accessing the pod inside k8s cluster
	- SSH into the worker node
		- ![[attachments/Pasted image 20220921221154.png]]
	- Using a node port service:
		- ![[attachments/Pasted image 20220921221229.png]]
