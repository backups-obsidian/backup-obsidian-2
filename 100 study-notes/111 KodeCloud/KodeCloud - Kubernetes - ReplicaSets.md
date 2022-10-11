---
created: 2022-10-10 15:34
updated: 2022-10-10 15:34
---
---
**Links**: [[111 KodeCloud Index]]

---
## ReplicaSet
- Replication Controller is the older technology that is being replaced by Replica Set.
	- The major difference between Replication Controller and Replica Set is `selector`

- The Replica Set has 3 major components under `spec`
	- `selector`
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

> [!note]- Since Replica Set has its own naming convention for the pods *we can omit the name field*.

- The main use cases of Replica Set are:
	- Ensuring the required number of pods are always maintained
	- Scaling the application.

> [!important]- The main use case of `selector` label is that Replica Set can also manage pods that were not created as a part of the Replica Set creation.
> For example some pods were created before the creation of Replica Set but match the labels specified in the Replica Set definition file. Then Replica Set will also take these pods into consideration.
> Now suppose if you have a Replica Set with no of replicas set to 3 and have 3 pods running. Now if you try to create and a pod then with the same selector Replica Set will try to delete the new pod since the desired number of replicas is 3.

- We can use `k describe replicaset/<replica-set-name>` to see the *selector* of that Replica Set and view the events.
	- You can use `rs` as a short form for  `replicaset`

> [!question]- How to get the version of a specific resource
> `k explain <resource-name> | grep VERSION`

- Deleting resources: `k delete <resource-type>/<resource-name>`
