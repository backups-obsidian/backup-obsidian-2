---
created: 2022-08-29 18:09
updated: 2022-09-05 23:52
---
---
**Links**: [[110 Kubernetes Index]]

---
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
	- Data is lost if the node dies
		- ![[attachments/Pasted image 20220903225725.png]]

- There are two types of local volumes. 
	- One is `emptydir` which is ephemeral and it *attached to the pod’s lifetime*. 
		- If the pod dies then the data is also lost. 
	- Other is the `hostpath`
		- In this case the volume is stored in the worker node and then mounted onto the pod. 
		- So *if the pod dies and a new pod is created the data will persist*. 
		- `hostpath` is only good for single worker node clusters since *data is available only on one specific node*.

#### Remote Volumes
- Using remote storage for PV
	- ![[attachments/Pasted image 20220903225957.png]]
	- We no longer rely on the container of the host

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

### Why so many abstractions
![[attachments/Pasted image 20220904223257.png]]
- As a developer we **need not to be concerned where the actual storage is**.
	- We *don't have to go through the hassle of setting up the storage*.
	- We make a claim for storage using PVC assuming that the cluster has storage.

### Special Volume Types
- **ConfigMap** & **Secret** are special volume types.
- Both of them are **local volumes** but *aren't created via PV or PVC* but are *managed by k8s*.
- We mount ConfigMap & Secret in the same way we mount volumes.
	- ![[attachments/Pasted image 20220904224237.png]]

> [!note]- We can use multiple volumes of different types simultaneously
> For example if we have an elastic search app then we can have a separate volume for secret, ConfigMap and another one for database.
> ![[attachments/Pasted image 20220904224723.png]]
> ![[attachments/Pasted image 20220904225014.png]]

> [!important]- We first mount the all volumes to the pod and then mount the required volumes to containers.

- Now you would be thinking what is the need of mapping ConfigMap & Secret to volumes since we are just using key value pairs from them. 
- Some times applications need configuration files to set them up. This is where mounting ConfigMap & Secret as volume type is used.

> [!caution]+ Different ways of using ConfigMap & Secret
> ![[attachments/Pasted image 20220905142918.png]]

- **ConfigMap & Secret are Local Volume Types**.

- Demo:
	- [Kubernetes ConfigMap and Secret as Kubernetes Volumes | Demo - YouTube](https://www.youtube.com/watch?v=FAnQTgr04mU)

### Storage Class
- General process of using storage in k8s
	- ![[attachments/Pasted image 20220904225158.png]]
- **PV should be pre provisioned** to be used in applications.
- Now when we are dealing with a lot of applications then depending on the needs a lot of PV have to be created by the admins before deploying the application.
	- This can be time consuming, tedious and error prone

- To make the process efficient we have storage class.
	- **Storage class provisions persistent volume dynamically whenever PVC claims it**. 
- We also create storage class using YAML configuration.
- Storage class creates persistent volume dynamically in the background.
- We define the storage backend using the provisioner attribute. In addition to this we also configure the parameters of the storage backend.
	- ![[attachments/Pasted image 20220904225917.png]]

- Using storage class with PVC
	- ![[attachments/Pasted image 20220904230017.png]]

- PVs are created and deleted automatically when we create or delete PVC while using storage class.

> [!note]+ How do pods use storage from storage class
> ![[attachments/Pasted image 20220904230142.png]]

> [!tip]- Volume connects to persistent volume claim which then connects to either PV (static) or Storage class (dynamic)
> ![[attachments/Pasted image 20220905235237.png]]
> The thing that determines the routing is the `StorageClassName`. If we provide one then we use the Storage class
> ![[attachments/Pasted image 20220905235330.png]]
> If we don't provide one then we get PV 
> ![[attachments/Pasted image 20220905235352.png]]
