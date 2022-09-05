---
created: 2022-08-29 18:09
updated: 2022-09-04 22:41
---
---
**Links**: [[110 Kubernetes Index]]

---
## Extra
- Data is lost if the node dies
	- ![[attachments/Pasted image 20220903225725.png]]
- We are using external storage 
	- ![[attachments/Pasted image 20220903225957.png]]
	- We no longer rely on the container of the host

> [!note]- Deployments share persisted volumes between pods whereas StatefulSets have their own persisted volume between pods.
> ![[attachments/Pasted image 20220904101127.png]]

- In case when pods are deleted, pods are deleted but their persistent volume is kept.
	- ![[attachments/Pasted image 20220904101249.png]]

## Volumes
- We persist data in k8s using volumes.
- There are 3 main components of k8s storage:
	- *Persistent Volume*
	- *Persistent Volume Claim*
	- *Storage Class*

- k8s doesn't give us any data persistence out of the box. We need to configure that.

### Storage Requirements
1. First of all we need a *storage that doesn't depend on the pod's lifecycle*. 
	- So that if the pod dies and a new pod is created it can read the existing data.
2. But we don't know on which node the new pod will restart so *our storage must also be available on all nodes*.
		- ![[attachments/Pasted image 20220904114203.png]]
3. *Storage needs to survive even if the whole cluster crashes*.

- These are the main requirements that our database storage needs to fulfil.

> [!note]- Another use case of persistent storage is the use of a directory.
> ![[attachments/Pasted image 20220904114534.png]]

### Persistent Volume
> [!important]+ Persistent volume is a **cluster resource** just like RAM & CPU
> So persistent volumes have to be already there in the cluster before the pod that depends on it is created.

- It is created using a YAML file.
- It is an *abstract component*.
- It must take storage from actual physical storage like local disk, cloud-storage or nfs volumes.
	- ![[attachments/Pasted image 20220904114846.png]]

> [!question]+ Where does this storage backend come from and who makes it available to the cluster?
> - k8s admin makes it available to the user.
> - This is the tricky part of the data persistence in k8s.
> - *k8s doesn't care about actual storage* it gives us **persistent volume as an interface** to the actual storage.
> - So we have to *decide what type of storage the applications would need* and we would have to create and manage (create backups) them ourselves.

- k8s can be thought of as external plugin to our cluster.
	- ![[attachments/Pasted image 20220904115429.png]]
- We can also have multiple storages configured for our cluster with different applications using different types of storage. One application may use multiple storage options.
	- ![[attachments/Pasted image 20220904115630.png]]

- We *use the physical storage* in `spec` section of persistent volume example
	- ![[attachments/Pasted image 20220904115823.png]]
- Using google cloud as physical storage
	- ![[attachments/Pasted image 20220904115916.png]]
- Using local storage as physical storage
	- ![[attachments/Pasted image 20220904120240.png]]

> [!note]- Depending on the physical storage type `spec` attributes would differ.
> In the official k8s backend we can see a list of supported backends.

> [!caution]- Persistent Volumes are *NOT namespaced*.
> They are accessible to the whole cluster.
> ![[attachments/Pasted image 20220904120707.png]]

#### Local Volumes
- Local volumes violates 2nd & 3rd requirement of [[Kubernetes - Volumes#Storage Requirements|Storage Requirements]] i.e. it is *tied only to the a particular node*.

#### Remote Volumes

#### k8s admin vs user
- k8s admin sets up and maintains the cluster.
	- Like making sure that the cluster has enough resources
- k8s user deploys applications in the cluster

> [!note]- K8s admin is the one that configures the actual storage.
> Meaning making sure that nfs storage or cloud storage is present depending on developer needs.

- *k8s admin is also responsible for creating PV components* from these storage backends depending.

### Persistent Volume Claim
- Once we have Persistent Volume components *we have to configure YAML files to use persistent volume components*.
	- In other words **application has to claim that volume**.
	- This is *done using persistent volume claim*.

- Persistent Volume Claims are also *created using YAML configuration*.
	- ![[attachments/Pasted image 20220904222403.png]]

- After making a claim we have to use it in our pods configuration. Now the pod & all the containers inside the pod will have access to the persistent volume.
	- ![[attachments/Pasted image 20220904222511.png]]

> [!note]+ Level of Volume Abstractions
> ![[attachments/Pasted image 20220904222842.png]]

> [!caution]- *Claims must exist in the same namespace as the pod* using the claim.
> ![[attachments/Pasted image 20220904222735.png]]

- Once a persistent volume is found by the PVC the volume is mounted onto the pod and then to the container.
	- ![[attachments/Pasted image 20220904223017.png]]
- If a pod has more than one container we can choose the containers to which we want to mount the volume. 

#### Why so many abstractions
![[attachments/Pasted image 20220904223257.png]]
- As a developer we don't need to be concerned where the actual storage is.
	- We don't have to go through the hassle of setting up the storage.
	- We make a claim for storage using PVC assuming that the cluster has storage.

### Storage Class

### Special Volume Types
- **ConfigMap** & **Secret** are special volume types.
- Both of them are **local volumes** but *aren't created via PV or PVC* but are *managed by k8s*.
- We mount ConfigMap & Secret in the same way we mount volumes.
	- ![[attachments/Pasted image 20220904224237.png]]



