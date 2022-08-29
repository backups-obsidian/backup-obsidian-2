---
created: 2022-08-28 11:14
updated: 2022-08-28 12:36
---
---
**Link**: [[110 Kubernetes Index]]

---
## Deployments
- It is *not advisable to create separate pods inside the k8s cluster since you cannot scale them*.
- The **best method of creating pods is via deployment** where you can increase or decrease their numbers easily.
- After creating a deployment you can list the pods. 
	- These pods were created automatically by the deployment. 
	- These *pods will now be managed by their particular deployments*.
- **Selectors** are *used to connect pods with deployments* because in k8s pods and deployments are separate objects. 
	- We have to assign pods to deployments. This is *automatically done when a deployment is created*.

- *Between deployment and pod there is another layer* which is automatically managed by k8s called **replica set**. 
	- You can get the replica set using `kubetctl get replicaset`. 
	- Replica set *manages the replicas of the pod*. 
	- You *will never have to create/delete or manage a replica set*. 
	- You will **work only with deployments**.

- Layers of abstraction → everything below deployment will be managed by k8s automatically.
	- ![[attachments/Pasted image 20220828111707.png]]

- Example of *selectors* and *replica sets*
	- ![[attachments/Pasted image 20220828112238.png]]
	- ![[attachments/Pasted image 20220828112326.png]]
	- *Notice the label value is the same as selector value of the deployment.*
	- *The hash we see after the deployment-name 84... is the hash of the replica set* and name after that i.e. *v44.. is the unique id of the pod*.

> [!important]- Pod naming: `deployment_name-replicaset_hash-unique_id_pod`

> [!note]- We cannot connect to these pods from our computer (running minikube) since our computer is external to k8s cluster. 
> - For connecting to these pods we need to go inside the cluster. 
> - Think as if minikube is running in the cloud and since the pods are not exposed to access them we need to ssh into the node which we do using `minikube ssh`
> - ![[attachments/Pasted image 20220828112620.png]]

## Services
- The IP addresses of the pods are dynamic and are assigned to pods when they are created.
	- We should not rely on these IP addresses if we want to connect to these pods.
- We create *services in k8s* if we want to connect to specific deployments using specific IP addresses. 

### Cluster IPs
- When assigned to a deployment **we can connect to the deployment from only inside the cluster i.e. ClusterIP is only accessible from within the cluster**. 
- This will be a *single IP address for the entire deployment*. 
- These are specially useful *if you have a database deployment* and you want other deployments within the cluster to connect to it *without exposing it to the outside world*.

#### Creating a cluster IP for a deployment
- Since we are exposing nginx pods running at default port 80 we have to expose port 80.
	- `kubectl expose deploy deployment-name --port=8181 --target-port=80`
- *kubernetes is a default service*. 
	- ![[attachments/Pasted image 20220828122805.png]]
	- We have single IP address for our deployment which could be used to connect to any of the pods.
- Now even if increase or decrease the number of pods in the deployment we don’t have to worry about the IP addresses. 
	- The service we created acts as a permanent IP and a load balancer for all those pods in the deployment.


### External IPs
- This will **open deployment to outside world**. 
- The most *common solution to implement this is to have a load balancer IP address* since pods belonging to the same IP address can reside in different worker nodes. 
- These load balancers are assigned by cloud providers and managed by cloud controller provider. 
- These are useful if you have to *expose your web application to the outside world*. 
- It is of two types, **NodePort** type and **LoadBalancer** type.

#### NodePort
- `k expose deploy deployment-name --type=NodePort --port=8181 --target-port=80`
	- ![[attachments/Pasted image 20220828123338.png]]
- Use *NodePort only if you don’t have a load balancer or you are doing a local setup*.

#### LoadBalancer
- Using LoadBalancer service type
	- ![[attachments/Pasted image 20220828123442.png]]
	- It will always remain pending for us since we are using minikube. 
	- But if we are using external cloud provider then we will get an external ip. 
	- Load balancer from the cloud provider will be automatically provisioned for us.

## How are pods updated in k8s
- k8s follows a **rolling update**. 
	- So if you update the docker image then new pods will be created first with the new image while previous pods are still running. 
	- Previous pods will be replaced one by one. It is very easy to replace image in all the pods via a single command.
- *k8s works on the desired state*. 
	- So if you have 4 pods in your deployment and you manually try to delete one of the pods then you will see that k8s immediately creates another one since the desired state is of 4 pods.