---
created: 2022-08-29 18:09
updated: 2022-08-29 19:03
---
---
**Links**: [[110 Kubernetes Index]]

---
There are **4** types of services: **NodePort**, **Cluster IP**, **Headless** and **LoadBalancer**

## IP Addresses in nodes
- All the worker nodes get a range of internal IP addresses.
	- ![[attachments/Pasted image 20220829181115.png]]
		- In this when we deploy a pod it gets an IP address of `10.2.2.5` which means it belongs to Node 2.
	- `kubectl get pod -o wide`
		- ![[attachments/Pasted image 20220829181357.png]]

## Communications inside the cluster (ClusterIP)
- Architecture
	- ![[attachments/Pasted image 20220829181514.png]]

- Basic pod structure
	- Let us suppose you have *2 containers running in one pod* and they have exposed different ports. 
	- We can assume that extra container is a log collector.
	- ![[attachments/Pasted image 20220829181644.png]]
- Ingress yaml file
	- ![[attachments/Pasted image 20220829181738.png]]
- Service yaml file
	- ![[attachments/Pasted image 20220829181838.png]]
- Service knows which pod to send the request using the selector
	- ![[attachments/Pasted image 20220829181922.png]]
- Service should match all the selectors and not just one
	- ![[attachments/Pasted image 20220829182048.png]]
- If the pods have multiple open ports how does the service know which port to forward the request to?
	- This is decided by the *target port attribute* in the service
	- ![[attachments/Pasted image 20220829182144.png]]
- **So selectors and the target port tell the service where to send the request**.
	- ![[attachments/Pasted image 20220829182241.png]]
- When a service is created k8s automatically creates an *endpoint object* which has the same name as service and keeps track of which pods are members of that service.
	- ![[attachments/Pasted image 20220829182340.png]]
- Port vs targetPort in service file
	- ![[attachments/Pasted image 20220829182933.png]] 

- Let us add mongodb pods to the architecture also let us assume that these pods have another container which sends metrics. So our architecture will look something like this
	- ![[attachments/Pasted image 20220829183053.png]]
- Now this means our mongodb service has to handle 2 different ports. 
	- This is an example of *multiport service*.
- *If you have multiple ports exposed in a service then you have to name them*. In case of only one port you can choose not to.
	- ![[attachments/Pasted image 20220829183217.png]]

## Headless Services
- Why?
	- Suppose that you need to talk to a specific pod. 
	- This is not possible when using a ClusterIP service since it asks as a load balancer. 
	- Or there can be a scenario where the a pod wants to directly talk to a specific pod, this is also not possible via ClusterIP service. For these use cases we have the headless service.

> [!question]- Now what could be the scenario that you have to talk to a specific pod directly?
> - Let us take an example of a stateful application where only the master pod is allowed to write to the database and the worker pods only read from the database.
> - Now the worker pods must connect to the master after they have written to the database to synchronise the data.
> - If a new pod is created then it should connect to the most recently updated pod to get the data.
> ![[attachments/Pasted image 20220829183449.png]]

> [!question]- Now how can we figure out the IP address of a specific pod?
> ![[attachments/Pasted image 20220829183700.png]]
> - We can make an api call to the k8s api server but then that makes our app too tied with the k8s api.
> - Other option is to do a dns look up by using the pod name but this returns the ClusterIP of the service managing that pod.
> - *We can tell k8s that we don’t need the cluster IP of the service by setting the ClusterIP field to None while creating a service*.
> 
> ![[attachments/Pasted image 20220829183753.png]]
> - This is how we **create a headless service**.

- When we create a headless service no IP address is assigned
	- ![[attachments/Pasted image 20220829183904.png]]

- **We will always have headless service along with the ClusterIP service**.
	- Other pods can talk to the database using the ClusterIP service and if we need to talk to any specific pod then we can use the headless service.
	- ![[attachments/Pasted image 20220829183922.png]]

## NodePort Service
- NodePort makes the external traffic accessible through static ports on each worker node.
	- ![[attachments/Pasted image 20220829184455.png]]
- Instead of ingress browser request will directly come to the worker node at the port that the service specification defines.
	- ![[attachments/Pasted image 20220829184657.png]]
	- nodePort value can only be within the given range.
- This means nodePort is accessible at `node_ip_address:nodePort` to the browser.

> [!note]- If you observer carefully you will find that we define port and targetPort in the yaml file of the NodePort service. This is because a **ClusterIP service is automatically created when we create a NodePort service**.
> ![[attachments/Pasted image 20220829184741.png]]
> This why we have a ClusterIP for a NodePort service
> 
> ![[attachments/Pasted image 20220829185216.png]]	

- The *ClusterIP service created by NodePort spans all the worker nodes*.
	- So if you have 3 pod replicas on 3 different nodes then the service will be able to handle the request coming on any of the worker nodes and forward it to the pod replicas on other worker nodes.
	- ![[attachments/Pasted image 20220829185327.png]]

> [!caution]- When you create a NodePort service it **creates a hole in all the nodes**
> - This means you can access the service using the IP address of any of the nodes.
> - Let's suppose that you have a NodePort service named `mynodeportservice`.
> - Clients inside the cluster can use the ClusterIP created while creating the NodePort service.
> - External clients that are outside the cluster can hit that ClusterIP service that exists inside the cluster. Let's say that our 3 K8s host nodes have IPs `10.10.10.1`, `10.10.10.2`, `10.10.10.3`, the Kubernetes service is listening on port 80, and the Nodeport picked at random was 31852.
> - A client that exists outside of the cluster could visit `10.10.10.1:31852`, `10.10.10.2:31852`, or `10.10.10.3:31852` (as **NodePort is listened for by every Kubernetes Host Node**) Kubeproxy will forward the request to `mynodeportservice's` port 80.
> - Example video → [https://www.youtube.com/watch?v=MO8N79lQSWU&t=1h12m10s](https://www.youtube.com/watch?v=MO8N79lQSWU&t=1h12m10s)

- If for some reason you are not able to access pods in node 1 by hitting the ip of node 2 while having a NodePort service then you should check your IP forwarding on your node machines. This should never happen while using a cloud provider but may happen when you are setting up your own k8s cluster.

- *NodePort services are not secure* if the nodes are accessible to the internet since we are opening ports on the nodes. 

## LoadBalancer Service
- ![[attachments/Pasted image 20220829185916.png]]
- **Whenever we create a LoadBalancer service NodePort and ClusterIP service are automatically created**.
	- ![[attachments/Pasted image 20220829185939.png]]
- When we use a *LoadBalancer service it becomes the entry point*. **It then directs the traffic to nodePort on the worker node which then directs it to the ClusterIP service**.
	- ![[attachments/Pasted image 20220829190057.png]]

> [!important]- *LoadBalancer Service is an extension of NodePort Service*. *NodePort Service is an extension of ClusterIP Service*.

> [!danger]- **NodePort service is never used in production**, it is generally used for some kind of testing. For production environments we will generally have ingress or LoadBalancer service

- Good Read: [containers - What's the difference between ClusterIP, NodePort and LoadBalancer service types in Kubernetes? - Stack Overflow](https://stackoverflow.com/questions/41509439/whats-the-difference-between-clusterip-nodeport-and-loadbalancer-service-types)