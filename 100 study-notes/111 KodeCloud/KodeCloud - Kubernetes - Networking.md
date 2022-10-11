---
created: 2022-10-10 15:35
updated: 2022-10-10 15:35
---
---
**Links**: [[111 KodeCloud Index]]

---
## Networking
> [!tip]- In Docker an IP address is assigned to a container whereas in k8s IP address is always assigned to a pod.

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
- These *requirements* are: 
	- *All containers/PODs can communicate to one another WITHOUT NAT*.
	- *All nodes can communicate with all containers and vice-versa WITHOUT NAT*.

- We don't have to setup it from scratch and have help from different sources. This would be needed if we are setting up the cluster from scratch.
	- ![[attachments/Pasted image 20220921220612.png]]

- Once setup they are responsible for managing the networking.
	- It assigns different network address for each network in the node thereby creating a virtual  network.
	- ![[attachments/Pasted image 20220921220832.png]]

- Different ways of accessing the pod inside k8s cluster
	- ![[attachments/Pasted image 20220921221154.png]]
	- ![[attachments/Pasted image 20220921221229.png]] : Node port service

- Different service types
	- ![[attachments/Pasted image 20220921221406.png]]

> [!tip]+ TargetPort is the port of the pod to which the service forwards the request.
> In short TargetPort is the target for the service.
> *All the terms are from the view point of the service*.
> ![[attachments/Pasted image 20220921222726.png]]

- In a service definition file the only mandatory field is port. 
	- If you don't provide NodePort it is assigned by k8s
	- If you don't provide TargetPort it is assumed to be same as Port.
	- *Ports is an array and we can have multiple such port mappings*.

- Sample service file
```yaml
apiVersion: vl
kind: Service
metadata:
	name: myapp-service
	# we can also provide labels but they are not necessary
spec:
	type: NodePort
	ports:
	 - targetPort: 80
	   port: 80
	   nodePort: 30008
	# no match labels key inside selector like we had in ReplicaSet
	selector:
		app: myapp
```

- Service acts as a *load balancer with a random algorithm* with a SessionAffinity set to Yes.
- What happens when pods are distributed across nodes?
	- **We can use IP of any of the nodes**.
	- ![[attachments/Pasted image 20220921223309.png]]

- Use of ClusterIP
	- ![[attachments/Pasted image 20220921223928.png]]
	- Each layer can scale and move without impacting communication between various services.

- **Each service gets an IP and name assigned to it and that is the name that should be used by other pods to access the service**.
	- Application code which needs to communicate with other pods in a different service should also use this name.
	- ![[attachments/Pasted image 20220921225516.png]]
	- Instead of hardcoding we should be using environment variables.
- *ClusterIP is the default type*.

- Disadvantage of NodePort
	- Even if we have pods on only 2 out of 4 nodes they are accessible from the IP addresses of all the nodes.
	- ![[attachments/Pasted image 20220921224350.png]]

> [!note]- We can bootstrap a k8s cluster from scratch using `kubeadm`