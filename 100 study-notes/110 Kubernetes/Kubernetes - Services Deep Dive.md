---
created: 2022-08-29 18:09
updated: 2022-08-29 18:45
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
	
## LoadBalancer Service