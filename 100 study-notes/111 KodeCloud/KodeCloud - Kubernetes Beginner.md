---
created: 2022-09-15 21:09
updated: 2022-09-20 13:47
---
---
**Links**: [[111 KodeCloud Index]]

---
![[attachments/Pasted image 20220919115140.png]]

- Nodes were also known as minions in the past.
- Scheduler - Placement. Where to put containers in a node.
- Controller - Brain. Responds when something goes down.
- Get k8s version: `k version --short`
- `kubeadm` is used to *bootstrap and manage production grade k8s cluster*.
- Pod is the smallest component you can create in k8s.

> [!note]- Multiple containers in a pod can communicate with each other directly by *referring to each other as localhost* since they share the same network space.
> They can also easily share the same storage space.

- Creating and running a pod: `k run <pod-name> --image=<image>`
- Describing things: `k describe <resource-type>/<resource-name>`

> [!caution]- Using `k describe pod/<pod-name>` can give a lot of additional information about the pod 
> - Node to which it belongs, 
> - Image name
> - *Events that occurred in the pod*.
> ![[attachments/Pasted image 20220919131209.png]]
> - We can see the *different containers that are present in the pod* and **their states**.
> - Pod IP
> ![[attachments/Pasted image 20220919131058.png]]

- Any configuration file must have these properties:
	- `apiVersion`
	- `kind`
	- `metadata`
	- `spec`

> [!tip]- Difference between `create` and `apply`
> There is a subtle difference between `kubectl create` and `kubectl apply` commands.
> - The `kubectl create` command creates a new resource. So, *if the command is run again it will throw an error as resource names should be unique in a namespace*.
> - The `kubectl apply` command applies the configuration to a resource. *If the resource is not there then it will be created*. 
> - The `kubectl apply` command can be run the second time even if the configuration hasn't changed.
> - In the `kubectl create`, we specify a certain action, in this case `create` and so it is **imperative**. In the `kubectl apply` command we specify the target state of the system and don't specify a certain action and so **declarative**. We let the system decide what action to take. If the resource is not there it will create it, if the resource is there then it will apply the configuration to the existing resource.
> - *From an execution perspective, there is no difference when a resource is created for the first time between `kubectl create` and `kubectl apply`*.
> ![[attachments/Pasted image 20220919122330.png]]

- Creating a pod using a YAML file
```yaml
apiVersion: vl
kind: Pod
metadata:
	name myapp-pod # name of the pod
	labels:
		app: myapp
		type: front-end
spec:
	containers: # container is an array of objects since we can have multiple containers in a pod
		- name: nginx-container # name of the image
		  image: nginx
```

> [!caution]- Use only spaces and **NOT TABS**.

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

- Deployment vs Replica Set
	- ![[attachments/Pasted image 20220919193045.png]]

- **Components of the deployment definition file are exactly same as the Replica Set definition file except for the kind**.

> [!question]+ What are the additional features provided by Deployment over ReplicaSet considering they have the same definition file.
> - You don't want to upgrade all the instances instantly. You would want *rolling updates*. 
> - *Roll back the changes*.
> - Make multiple changes to your environment. You wouldn't want to apply it immediately. You would like to *pause the environment* make the changes and *resume* so that all the changes are rolled out together.

- The deployment automatically creates a ReplicaSet
- Pod naming convention when created by deployment: `<deployment-name>-<replica-set-hash>-<random-hash>`
- We can see the **status of roll out** using `k rollout status deployment/<deployment-name>`
- Get the **history and revisions** of the rollout: `k rollout history deployment/<deployment-name>`
- *Rolling update is the default deployment strategy*.
- Another deployment strategy is *Recreate* in which all the pods are first destroyed and then the new revision is added.

- Difference between Recreate & Rolling Update: 
	- ![[attachments/Pasted image 20220919202752.png]]
	- See how the ReplicaSet replicas change in Recreate (*reduced to 0*) and Rolling Update (few units at a time)
- When we apply a deployment definition a new rollout is triggered and a new revision is created.

> [!question]- How do upgrades happen in Deployments?
> - When we upgrade our application *a new ReplicaSet is created under the hood and starts deploying the containers there*. 
> - At the same time pods are being taken down in the old ReplicaSet in a rolling update strategy.
> ![[attachments/Pasted image 20220920000658.png]]
> - This can be verified when we list the ReplicaSets. We will see one ReplicaSet with 0 pods and other one with x pods.

- We can easily rollback using `k rollout undo deployment/<deployment-name>`
	- This will destroy the pods in the new ReplicaSet and bring the older ones up in the old ReplicaSet.
	- ![[attachments/Pasted image 20220920000907.png]]
	- We can notice the difference in ReplicaSets before and after the rollback.

- Whenever we apply a deployment file we can record the cause of rollout using the `--record` flag.
	- `k apply -f deployment.yaml --record`
	- Using this would mean that the cause of change is tracked in the revision history.