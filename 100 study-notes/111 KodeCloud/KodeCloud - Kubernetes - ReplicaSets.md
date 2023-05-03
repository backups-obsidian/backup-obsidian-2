---
created: 2022-10-10 15:34
updated: 2023-05-03 08:19
---
---
**Links**: [[111 KodeCloud Index]]

---
## ReplicaSet
- Replication Controller is the older technology that is being replaced by Replica Set.
	- The major difference between Replication Controller and Replica Set is `selector`

- The Replica Set has 3 major components **under `spec`**
	- `selector`: this decides what pods are managed by the replica set.
	- `template`: pod template
		- ![[attachments/Pasted image 20220919133521.png]]
	- `replicas` 

- Sample Replica Set YAML file:
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: myapp-replicaset
  labels:
    app: myapp
spec:
  selector:
    matchLabels:
      env: production
  replicas: 3
  template:
    metadata:
	  # This name will not be used for naming the pods. 
	  # Name will be <replicaset-name>-<random-hash>
	  # In this case it will be myapp-replicaset-<random-hash>
      name: nginx-2 
      labels:
        env: production
    spec:
     containers:
        - name: nginx
          image: nginx
```

- In the above example note that **`labels` of the pod is same as that of the `matchLabels` of the replica set**.
	- They *must be the same for pods to be managed by this replica set*.

> [!note]- Since Replica Set has its own naming convention for the pods *we can omit the name field*.
> So this means in the above example yaml file `name: nginx-2` can be omitted.

- The main use cases of Replica Set are:
	- Ensuring the required number of pods are always maintained
	- Scaling the application.

> [!important]- The main use case of `selector` label is that Replica Set can also manage pods that were not created as a part of the Replica Set creation.
> - For example some pods were created before the creation of Replica Set but match the labels specified in the Replica Set definition file. Then Replica Set will also take these pods into consideration.
> - Now suppose if you have a Replica Set with no of replicas set to 3 and have 3 pods running. Now if you try to create and a pod then with the same selector Replica Set will try to delete the new pod since the desired number of replicas is 3.

- We can use `k describe replicaset/<replica-set-name>` to see the *selector* of that Replica Set and view the events.
	- You can use `rs` as a short form for  `replicaset`

> [!question]- How to get the `apiVersion` of a specific resource to be used in a yaml file?
> `k explain <resource-name> | grep VERSION`
>
> ![[attachments/Pasted image 20230503081751.png]]
> Hence for replica set its version is `apps/v1`
> ---
> ![[attachments/Pasted image 20230503081815.png]]
> For pods it is just `v1`

- Deleting resources: `k delete <resource-type>/<resource-name>`
- Kubernetes supports *self-healing applications* through ReplicaSets and Replication Controllers. 
	- The replication controller helps in ensuring that a POD is re-created automatically when the application within the POD crashes.