---
created: 2022-08-28 11:14
updated: 2022-08-28 11:27
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