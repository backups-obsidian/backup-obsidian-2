---
created: 2023-05-29 14:20
updated: 2023-05-30 11:06
---
---
**Links**: [[111 KodeCloud Index]]

---
## Pod Networking
- **K8s DOESN'T have a solution for pod networking** and expects us to come up with a solution.
- But any networking solution we use must follow these criteria:
	- *Every POD should have an IP Address*.
	- *Every POD should be able to communicate with every other POD in the same node*.
	- *Every POD should be able to communicate with every other POD on other nodes without NAT*.
- Diagram:
	- ![[attachments/Pasted image 20230529143639.png]]

### Implementing Pod Networking requirement using basic networking
- When containers are created k8s creates networking namespaces for them.
- We then *create a bridge network on each node, assign them IP addresses and the bring them UP*.
	- Each bridge network will be in its own subnet.
- Now every time a container is created we:
	- Create a virtual interface `veth` to link the network namespace (container) with the bridge network.
	- We assign IP address to the virtual interface connected to the network namespace and then add a route to the default gateway.
- We have solved the first 2 challenges.
	- Now every pod has an IP address and every pod can communicate with every other pod in the same node.
	- Reference diagram:
		- ![[attachments/Pasted image 20230529144416.png]]
- To enable pods to communicate with pods on other nodes we *add entries to routing table of the node*.
	- For example if `10.244.1.2` wants to ping `10.244.2.2` then we add the following route table to the host: `ip route add 10.244.2.2 via 192.168.1.12`.
		- ![[attachments/Pasted image 20230529144747.png]]
- Instead of adding entries to the route table we can use a router to bring everything together:
	- ![[attachments/Pasted image 20230529145020.png]]

### Multiple Containers inside a Pod
- A pod can consist of one or more containers.
- **From a network standpoint, each container within the pod shares the same networking namespace**. 
	- This gives *each container access to the same network resources, such as the pod's IP address*. 
	- *Containers within the same pod can also communicate with each other over localhost*.
	- Diagram:
		- ![[attachments/Pasted image 20230530103134.png]]
- We need to watch out for port conflicts when we’ve got multiple containers in the same pod.

> [!note]- Although the multiple containers in a pod are in the same network namespace they **run in separate process namespaces**.
> We won't be able to view other container's process PID.
 
## Service Networking
- [[KodeCloud - CKA - Cluster Architecture#Kube proxy | Kube proxy]] manages the services.
- The range of ip address that a service can use is specified while running the kube-apiserver by specifying `--service-cluster-ip-range`.
	- ![[attachments/Pasted image 20230529155442.png]]

> [!caution]- The range of pod networking and service networking *SHOULD NOT overlap*.
> There shouldn't be a case where a pod and IP are assigned the same IP address.

- **Iptables rules are modified by kubeproxy whenever a new service is created**. 
	- ![[attachments/Pasted image 20230529160348.png]]
