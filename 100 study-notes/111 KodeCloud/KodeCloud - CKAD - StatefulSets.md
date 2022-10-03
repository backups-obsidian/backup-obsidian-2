---
created: 2022-09-23 17:15
updated: 2022-10-03 15:43
---
---
**Links**: [[111 KodeCloud Index]]

---
## Why StatefulSets?
[[../110 Kubernetes/Kubernetes - StatefulSet | Kubernetes StatefulSet]]

- High level plan for deploying a MySQL cluster
	- ![[attachments/Pasted image 20221002222026.png]]
- We need a static name for master. In Deployments we get random names for pods.

## Difference between Deployments and StatefulSets
- **Pods are created in a sequential order in StatefulSets** whereas in Deployments they are spun up together.
	- This means that using StatefulSets we can setup master first and then the slaves.
- **StatefulSets assign unique ordinal index to each pod starting from 0**.
	- This solves the problem of having a static name for the master.
	- No random names going forward. 
		- *We can be sure that the name of the first pod in a StatefulSet will be `<name-of-statefulset>-0`*.
	   - We can always use that as master. 
- **StatefulSets maintain a stick identity**.
	- We can now point the slave pods to `<name-of-statefulset>-0` for continuous replication.
	- *Even if the master fails and the pod is recreated it would still come up with the same name*.
- **When you scale down a StatefulSet they are scaled down in the reverse order**. 
	- They come up in order, go down in order.
	- Same is true in case of termination.
	- This is the default behaviour of the StatefulSet. But we can override it to not follow an ordered launch but still get the other benefits of StatefulSet like stable and unique network ID.
		- For this `podManagementPolicy` field has to be set to `parallel` in the StatefulSet definition file.

## StatefulSets
- StatefulSet is created in the same way as a Deployment with minor changes.
	- Only the kind changes from `Deployment` to `StatefulSet`.
	- **`serviceName` of a headless service**.

### Headless Service
- We want the reads to be served by all the master and slaves but *writes should only be served by master*.
	- So we want to **point the web server to the master database pod only**.
	- This can't be done by only using the service which is used for exposing the database pods since it acts as a loadbalancer across all pods.
- One way to reach the master pod directly is if we know the IP address of the pod. But this is not ideal since if the pod restarts then the IP will change.
- Each pod can be reached from its DNS address but it is also created from its DNS address and is subject to change upon restart.
	- `10-40-2-8.default.pod.cluster.local`: `<pod-ip>.<namespace>.pod.cluster.local`
- **We need a service that doesn't loadbalance requests but gives us a DNS entry to reach each pod. This is done by a headless service**.
- A headless service is created as a normal service but it doesn't have an IP of its own.
	- It does not perform any load balancing but gives us a DNS entry to each pod with syntax: `<pod-name>.<headless-svc-name>.<namespace>.svc.cluster.local`
	- ![[attachments/Pasted image 20221002225346.png]]
- Using the headless service the master can now point to the master for reads.

- Sample headless service
```yaml
apiVersion: v1
kind: Service
metadata:
	name mysql-h
spec:
	ports:
	- port: 3306
	selector:
		app: mysql
	clusterIP: None
```

> [!caution]+ When a headless service is created DNS service is created for pods only if 2 conditions are met while creating the pod in the pod definition file.
> - `subdomain` field under the `spec` section of pod should be same as the name of the headless service.
> - `hostname` field should also be specified to create A records.
> ![[attachments/Pasted image 20221002225824.png]]
> - `<hostname>.<subdomain>.<namespace>.svc.cluster.local`

- Now when pods are deployed as a Deployment by default if there is no `hostname` or `subdomain` the Deployment won't add a `hostname` or `subdomain`.
	- So the headless service doesn't create A records for the pod.
	- If we specify the `hostname` and `subdomain` like we did in pod definition file then it assigns the same hostname and subdomain for all the pods.
	- This results in creating DNS A records for all the pods but with the same name.
	- We can't address the pods separately.

- This is *yet another way a StatefulSet differs from a Deployment*.
	- When creating a StatefulSet we need not specify a hostname or subdomain.
	- **StatefulSet assigns the right hostname based on the pod name and the right subdomain based on the headless service name**.
	- The StatefulSet gets the headless service name from the `serviceName` parameter in the `spec` section of StatefulSet.

## Storage in StatefulSet
- Static provisioning summary:
	- PVs are claimed by PVC and are then used by pod definition files.
	- *A single PV is mapped to a single PVC which is mapped to a single pod definition file*.
	- ![[attachments/Pasted image 20221003102450.png]]

- Dynamic provisioning summary:
	- We use storage class to take out manual provisioning of PV and automatically provision PV on cloud providers.
	- *PV is created automatically but we still create a PVC manually and associate that to a pod*.

> [!question]- How does dynamic provisioning change for deployments or StatefulSets.
> - For StatefulSets/Deployments when we specify the PVC in the definition file, *all pods* created by that StatefulSets/Deployments *try to use that same volume*.
> - **Multiple instances of the application share and access the same storage**.
> - Not all storage types support this operation (read and write from multiple instances at the same time).
> ![[attachments/Pasted image 20221003103148.png]]

> [!danger]- What are the problems of different pods accessing the same volume?
> - In case of a single PVC for Deployment/StatefulSets if the `accessMode` in PVC is set to RWO (supported by most storage types) then the pods that get spawned in a node different from the node where the volume is mounted won't be able to access it.
> - We should either use separate PVCs for each pod using StatefulSets (cannot be done in deployments) or use a storage type that supports RWX.

- **What if we want separate volumes for each pod**? 
	- Then each pod will require a PVC and since the each PVC is bound to a PV so each PVC will need a PV.
	- This PV can be created from different or same storage class.

> [!question]+ How do you create a PVC for each pod in a StatefulSet?
> - This can be done using a *volume claim template*. It is a PVC but templatised.
> - It *can only be used with StatefulSets*.
> - *It just means that instead of creating a PVC manually and then specifying it in the StatefulSet definition file, we move the entire pvc definition under the section `volumeClaimTemplate` under the StatefulSet specification*.
> - `volumeClaimTemplate` is an array so we can specify multiple templates.
> - **We cannot created different PVCs for different pods in Deployments**.

- **Now when a StatefulSet is created it first creates the a pod, during the creation of the pod a PVC is created. A PVC is associated with a storage class so the StorageClass provisions a volume on GCP and it creates a PV and then associates the PV with a volume and then binds the PVC to a PV**. 
	- Then the second pod is created which creates a PVC then the storage class provisions a new volume, creates a PV and then binds it to the PVC.
	- And so on.
	- ![[attachments/Pasted image 20221003104316.png]]

- What if one of these pods fail and is recreated or scheduled to another node?
	- StatefulSet do not automatically delete the PVC or the associated volume to the pod.
	- **Instead it assures that the pod gets attached to the same PVC it was attached to before. Thus StatefulSet ensure stable storage for pods**.

## References
- **Must Watch**: [Persistent Volumes with NFS and Cloud Storage // Kubernetes Tutorial - YouTube](https://www.youtube.com/watch?v=pumX2Ds5L0c)
- [Kubernetes Persistent Volumes: Examples & Best Practices (loft.sh)](https://loft.sh/blog/kubernetes-persistent-volumes-examples-and-best-practices/)
- [kubernetes - Deployment and PVCs - Stack Overflow](https://stackoverflow.com/questions/50761264/deployment-and-pvcs)