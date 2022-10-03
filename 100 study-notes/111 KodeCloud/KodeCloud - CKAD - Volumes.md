---
created: 2022-09-15 21:09
updated: 2022-10-02 21:03
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

- Simple persistent volume yaml file
```yaml
apiVersion: vl
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

- Access modes define how a volume should be mounted on the host. Supported values are:
	- `ReadOnlyMany`
	- `ReadWriteMany`
	- `ReadWriteOnce`
- We can replace the `hostPath` option with `awsElasticBlockStore` or any other supported storage types.

- Listing the persistent volumes: `k get persistentvolume` or `k get pv`

## Persistent Volume Claims
- Once the PVCs are created k8s binds them to the PV.
- Every PVC is bound to a single PV.
	- During the binding *k8s tries to find a PV that has sufficient capacity as requested by the PVC and any other request properties like **access modes**, volume mode, storage class etc*.
	- If there are multiple possible matches for a single claim and we would specifically like to use a specific volume we can use selectors to bind to the right volume.

> [!note]+ A smaller claim may get bound to a larger volume if all the other criteria matches and there are no other options.
> There is a 1 to 1 relationship between claims and volumes so no other claims can utilise the remaining capacity in the volume.
> ![[attachments/Pasted image 20221002164442.png]]
> ![[attachments/Pasted image 20221002164324.png]]

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

> [!important]- Once you create a PVC use it in a POD/Deployment definition file by specifying the PVC Claim name under persistentVolumeClaim section in the volumes section.

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

- Local volumes do not currently support dynamic provisioning (so we will have to create a PV for local volume), however a StorageClass should still be created to delay volume binding until Pod scheduling. This is specified by the `WaitForFirstConsumer` volume binding mode.

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: local-storage
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer
```