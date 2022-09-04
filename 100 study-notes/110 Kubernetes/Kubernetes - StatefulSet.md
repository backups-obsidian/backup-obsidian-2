---
created: 2022-08-29 18:09
updated: 2022-09-03 19:54
---
---
**Links**: [[110 Kubernetes Index]]

---
## StatefulSet
- It is a k8s component used specifically for *stateful applications*.
	- Databases are example of stateful applications

> [!note]- *Stateless applications* are deployed using *deployment component* whereas *stateful applications* are deployed using *StatefulSet*.

### Pod Identity
- In StatefulSet **all the replica pods are not identical**. 
- **Every pod has its own identifier**.
	- Unlike deployment where we have random hashes at the end, StatefulSet gets fixed ordered names (`statefulsetname-ordinal`).
		- ![[attachments/Pasted image 20220903193454.png]]
	- So if you create a StatefulSet called `mysql` with 3 replicas you will have pods with the following names
		- ![[attachments/Pasted image 20220903193803.png]]
		- First is the master then comes the slave in the order of startup.
	- Next pod is only created if previous is up & running.
	- Deletion starts from the last pod.
		- ![[attachments/Pasted image 20220903193949.png]]

- Each pod has a *persistent identifier* which it *maintains while rescheduling*. 
	- This means that if a pod of ID-2 dies then a new pod of the same ID will replace it.

### Why it's difficult to scale database applications?
> [!question]- How are database applications scaled?
> There can be **only 1 master database** which takes writes.
> ![[attachments/Pasted image 20220903190618.png]]

- The pods don't share the same physical storage even though they use the same data.
	- ![[attachments/Pasted image 20220903190819.png]]
- They each have their own replica of their storage which they can access. 
	- This means to have the same data as other pods the data must be synchronised.
- The worker pods (slaves) must know about each and every changes made to master so that it can be upto date.
	- ![[attachments/Pasted image 20220903191023.png]]
- When a new pod joins it must take care of replicating the data
	- ![[attachments/Pasted image 20220903191243.png]]
	- It first *clones the data from the previous pod* and then starts synchronisation.

> [!important]+ Since *all the pods have a copy of the data* you can actually get away with only having **temporary data storage**.
> ![[attachments/Pasted image 20220903191747.png]] 
> - But this means that *all data will be lost when all the pods die*.
> - But in case of *persistent storage date will survive even if all the pods die* since **Persistent Volume lifecycle isn't tied to other component's lifecycle**.

### Pod State
- Each pod has its own persistent volume plus the pod state.
	- Pod state has information that whether its a master pod or a slave pod.
	- ![[attachments/Pasted image 20220903192919.png]]

- When a pod dies & gets replaced the persistent pod identifiers makes sure that the storage volume gets reattached to the replacement pod.
	- Since the storage has the state of the pod.
	- ![[attachments/Pasted image 20220903193026.png]]

> [!note]- For the reattachment to work it is important to use remote storage.
> - Since if the pod gets rescheduled from one node to another then the previous storage must be available on other nodes as well.
> - We cannot use local volume storage as it is attached to a particular node.
> - ![[attachments/Pasted image 20220903193221.png]]

### Pod Endpoints
- Each pod has 2 endpoints.
	- ![[attachments/Pasted image 20220903195308.png]]
	- LoadBalancer Service
	- Individual Service Name