---
created: 2022-09-15 21:09
updated: 2023-05-08 16:22
---
---
**Links**: [[111 KodeCloud Index]]

---
## Volumes
- We first define a `volumes` field parallel to the `containers` field. 
	- Here we define all the volumes that will be used in the containers and give them names.

```yaml
volumes:
- name: data-volume
  hostPath: 
    path: /data # location on the host
	type: DirectoryOrCreate 
```
- *Any files created in the volume would be stored in the directory `/data` on the host*.
- **Once the volume is created to access it from the container we mount the volume to a directory inside the container**.
	- We use the `volumeMounts` field in each container to mount the `/data` volume to the directory `/opt` within the container

```yaml
volumeMounts:
- mountPath: /opt
  name: data-volume
```

- Very simple yaml file to mount a directory from host (node) to pod.
	- ![[attachments/Pasted image 20221002160808.png]]
	- *The above yaml files states that the contents inside the `/opt` directory of container should be mounted to a volume named `data-volume` whose storage location is `/data` on the host node*.

> [!caution]- `hostPath` mounting is **similar to bind volume mounting in docker containers**.
> - This means that contents of the directory of the `hostPath -> path` (present on the node) **will always override the contents** of  `volumeMounts -> mountPath`.
> - This means that suppose `hostPath -> path` is `/test` (empty folder) and `volumeMounts -> mountPath` is `/usr` then the container won't start since all the important files in the `/usr` directory inside the container is being over written  by an empty directory (`/test`) on the host system.
> - ![[attachments/Pasted image 20230508150758.png]]

- We have *different volume storage options*.
	- We used the **`hostPath` option to configure a directory on the host as a storage space for the volume**.
		- This works fine for a single node but it is not recommended for a multi node cluster.
	- k8s supports different types of storage solutions like NFS, public cloud solutions, etc.
	- For example to use AWS EBS as the storage volume we replace the `hostPath` field with `awsElasticBlockStore` field. 
		- The volume storage will now be on AWS EBS.

```yaml
volumes:
- name: data-volume
  awsElasticBlockstore:
    volumeID: <volume-id>
    fsType: ext4
```

## Persistent Volumes
- In the above examples we created volumes in the pod definition file. So all the configuration information for the volume goes within the pod definition file.
- When we have a large environment with a lot of pods the users would have to configure storage for each pod.
	- The users would have to configure storage on all pod definition files.
	- Every time a change is made the user would have to make the changes on all the pod definition files.

- *Instead we would like to manage storage more centrally*.
	- We would like it to be configured in a way where the administrator can create a large pool of storage and then have users carve our pieces from it as required.

> [!note]+ A *persistent volume (PV) is a cluster wide pool of storage volumes* configured by an administrator to be used by users while deploying the application.
> Users can select storage from this pool using persistent volume claim (PVC).
> ![[attachments/Pasted image 20221002162448.png]]

- **Just like the nodes in a cluster, PV is a resource in the cluster**.

- Simple persistent volume yaml file
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
	name: pv-vol1
spec:
	accessModes:
	- ReadWriteOnce
	capacity:
		storage: 1Gi
	# not recommended in production
	hostPath:
		path: /tmp/data
```

- Access modes define how a volume should be mounted on the host (node). 
- *Access Modes of your PersistentVolume affect wether all your pods can access the volume concurrently from different Nodes or not*.
	- `ReadOnlyMany (ROX)`
	- `ReadWriteMany (RWX)`
	- `ReadWriteOnce (RWO)`: The volume can be mounted as read-write by a *single node*. ReadWriteOnce access mode *still can allow multiple pods to access the volume when the pods are running on the **same** node*.

> [!note]- All these access modes may not be supported by all the storage classes.

- We can replace the `hostPath` option with `awsElasticBlockStore` or any other supported storage types.

- Listing the persistent volumes: `k get persistentvolume` or `k get pv`

> [!caution]- PVs are not bound to any name space so `k get pv` will list all the PVs.

## Persistent Volume Claims
- Once the PVCs are created k8s binds them to the PV.
- Every PVC is bound to a single PV.
	- During the binding *k8s tries to find a PV that has sufficient capacity as requested by the PVC and any other request properties like **access modes**, volume mode, storage class etc*.
	- If there are multiple possible matches for a single claim and we would specifically like to use a specific volume we can use selectors to bind to the right volume.

> [!note]+ A *smaller claim may get bound to a larger volume* if all the other criteria matches and there are no other options.
> There is a 1 to 1 relationship between claims and volumes so no other claims can utilise the remaining capacity in the volume.
> ![[attachments/Pasted image 20221002164442.png]]
> ![[attachments/Pasted image 20221002164324.png]]
> In the above image we see that we only needed the 500Mi but we got 1Gi

- *If there are no volumes available then PVC will remain in a pending state* until newer volumes are made available to the cluster.

- Simple PVC

```yaml
kind: PersistentVolumeClaim 
apiVersion: v1 
metadata: 
	name: claim-log-1 
spec: 
	accessModes: 
	- ReadWriteOnce 
	resources: 
	  requests: 
	    storage: 50Mi
```

- View the claims: `k get persistentvolumeclaim` or `k get pvc`

> [!important] Once you create a PVC use it in a POD/Deployment definition file by specifying the PVC Claim name under persistentVolumeClaim section in the volumes section.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/var/www/html"
        name: mypd
  volumes:
    - name: mypd
      persistentVolumeClaim:
        claimName: myclaim
```
- The `volumeMounts` part in the container remains the same. 
	- We specify the `mountPath` and the `name` of the volume.
- In the `volumes` section we use the `persistentVolumeClaim` option instead of using the `hostPath` option.
	- We use the `claimName` to use a specific claim.

- *Pods access storage by using the claim as a volume*. 
- **Claims must exist in the same namespace as the Pod using the claim**. 
- The cluster finds the claim in the Pod's namespace and uses it to get the PersistentVolume backing the claim. 
- The volume is then mounted to the host and into the Pod.

> [!important]- If there is an access mode mismatch then a PVC cannot be bound to a PV.

### Deleting PVCs
- We can choose what happens to the underlying PV when PVCs are deleted.
- By **default** it is set to **Retain**.
	- The PV will remain until it is manually deleted by the administrator.
	- **It is not available for reuse by any other claims**.
	- In short PV is not deleted but PV is not available.
- It can be deleted automatically using the *Delete* option.
	- As soon as the PVC is deleted PV will also be deleted.
	- This frees up storage on the end storage device.
- In *Recycle* option the data on the volume is scrapped and it is then made available to other claims.

> [!note]- If we are *deleting a PVC* and it is being *used by a pod* then it will be stuck in **terminating state**.
> **To delete the PVC you must terminate the pod**.

## Summary PV, PVC & Pods
![[attachments/Pasted image 20221002202326.png]]


## Storage Classes
- Before PV is created we should have created the storage on some storage platform we are using.
	- This has to be done every time before we create a PV.
	- This is known as *static provisioning*.

- *With storage classes we can define a provisioner such as google storage that can **automatically provision storage** on google cloud and attach that to pods when claim is made*.
	- This is known as *dynamic provisioning* of volumes.
- We do this by creating a storage class object.

- Simple storage class definition file
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
	name: google-storage
provisioner: kubernetes.io/gce-pd
```

> [!important]- **With storage classes we no longer need to create a PV** since any associated storage is going to be created automatically by the storage class.
> A PV is created but we don't create it and it happens automatically.

- For PVC to use a storage class we specify it in the PVC definition. This is how PVC knows which storage class to use.
	- ![[attachments/Pasted image 20221002203346.png]]

- We have a lot of different provisioners for storage classes.
- It is known as a storage class since we can create different classes of service.
	- For example a silver class with normal disk, a gold class with ssd and a platinum class with ssd and replication
	- Next time we create a PVC we can simply specify the class of storage needed for the volumes.
	- ![[attachments/Pasted image 20221002203656.png]]

- List all the storage classes: `k get sc`

## Difference between Volumes and Local PV
- A `hostPath` volume *mounts a file or directory from the host node's filesystem into your Pod*. This is not something most pods will need.
	- So, if you have a multi-node cluster, the *pod is restarted for some reasons and assigned to another node*, the new node won't have the old data on the same path. 
	- That's why we have seen, that hostPath volumes **work well only on single-node clusters**.
- Here, the Kubernetes `local persistent volumes` help us to overcome the restriction and we **can work in a multi-node environment** with no problems. 
	- **It remembers which node was used for provisioning the volume**, thus making sure that a restarting POD always will find the data storage in the state it had left it before the reboot.
- **Once a node has died, the data of both `hostpath` and `local persitent` volumes of that node are lost.**
	- So the main advantage of local persistent volumes over hostpath is that they schedule pods to the right nodes in a multinode cluster.
	- *If we are working with a single node k8s cluster then its best to use hostpath over local persistent volume*.

> [!important] If we use `hostPath` with glusterfs mount then no need to care about pod rescheduling, wherever pod get reschedule it will get updated data due to glusterfs replication.

- *Both use local disks of the machine*.

> [!caution]- Although `hostPath` is easy it should not be used in production.

- **To use local persistent volume, we must first define the storage class**:
```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: local-storage
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer
```

- *Local persistent volumes DO NOT currently support dynamic provisioning* (so we will have to create a PV for local volume), however a StorageClass should still be created to delay volume binding until Pod scheduling. This is specified by the `WaitForFirstConsumer` volume binding mode.
	- If we don't create a PV then the pod will remain in a pending state.
		- ![[attachments/Pasted image 20230508161859.png]]

## Difference between emptydir & hostpath
- An `emptyDir` volume is first created when a Pod is assigned to a Node, and **exists as long as that Pod is running on that node**.
	- Volume data of hostPath is persisted in the file system of node. Even if POD is deleted, volume data is still stored in Node.
- *`emptyDir` space can be shared by multiple containers in a pod if needed*.

> [!note] When a Pod is restarted or removed, the data in the emptyDir is lost forever.

- Some *use cases* for an emptyDir are:
	- scratch space, for a sort algorithm for example
	- when a long computation needs to be done in memory
	- **as a cache**
	- **Sharing a storage space between containers**.

- emptyDir example
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - image: my-app-image
    name: my-app
    volumeMounts:
    - mountPath: /cache
      name: cache-volume
  volumes:
    - name: cache-volume
      emptyDir: {}
```

> [!question]+ Is `emptyDir` different from no volume at all?
> Yes it is different
> - One of the main features of the `emptyDir` is that **it can be mounted on multiple containers inside the same Pod**.  This cannot be done by using no volumes.
> - `emptyDirs` are **preserved when the container inside the pod are restarted but not when the pod is re-scheduled**. No volumes are not preserved when the container crashes.
> - So if you just want to *preserve the data from container crashes* and not pod rescheduling then you should use emptyDir.

- Sample yaml of 3 containers sharing the same emptyDir volume
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myvolumes-pod
spec:
  containers:
  - image: alpine
    imagePullPolicy: IfNotPresent
    name: myvolumes-container-1
    command: ['sh', '-c', 'echo The Bench Container 1 is Running ; sleep 3600']
    volumeMounts:
    - mountPath: /demo1
      name: demo-volume

  - image: alpine
    imagePullPolicy: IfNotPresent
    name: myvolumes-container-2
    command: ['sh', '-c', 'echo The Bench Container 2 is Running ; sleep 3600']
    volumeMounts:
    - mountPath: /demo2
      name: demo-volume

  - image: alpine
    imagePullPolicy: IfNotPresent
    name: myvolumes-container-3
    command: ['sh', '-c', 'echo The Bench Container 3 is Running ; sleep 3600']
    volumeMounts:
    - mountPath: /demo3
      name: demo-volume

  volumes:
  - name: demo-volume
    emptyDir: {}
```
- Note all 3 containers refer to the same **name: demo-volume**
	- All 3 containers mount the **emptyDir** at *different mount points*.

> [!caution]- Like `hostPath` **`emptydir` also behaves like a bind volume mount**.
> Whichever folder it is being mounted to, it will erase all the contents inside it in all the containers.
> ![[attachments/Pasted image 20230508154617.png]]
> The above manifest would erase all the contents of `/etc` in the second container. 

### References 
- [Kubernetes Volume Basics: emptyDir and PersistentVolume - Alibaba Cloud Community](https://www.alibabacloud.com/blog/kubernetes-volume-basics-emptydir-and-persistentvolume_594834)