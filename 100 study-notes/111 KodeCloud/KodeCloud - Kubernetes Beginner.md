---
created: 2022-09-15 21:09
updated: 2022-09-22 01:12
---
---
**Links**: [[111 KodeCloud Index]]

---
## Basics/Pods
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

## Deployments/Rollouts
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

- *Scenario*: In the deployment you change the image to a non existent image
	- When you apply this configuration you will have some working pods (from the older ReplicaSet) and some non working pods (from the new ReplicaSet)
	- ![[attachments/Pasted image 20220920233119.png]]
	- In the above example image we see that only one pod was terminated (from the older ReplicaSet) and it failed to create 3 new pods since it couldn't pull the image.
	- You can notice the different ReplicaSet by comparing the hash.
	- **Even though we have specified the wrong image, the application is not impacted** since we have 5 working pods.
	- This is because of the default deployment strategy of rolling deployment.
	- Kubernetes will only delete the older pods once all the new pods are up and running.

## Networking
> [!tip]- In Docker an IP address is assigned to a container whereas in k8s IP address is always assigned to a pod.

- When k8s is initially configured an *internal private network* is configured with the address range of `10.244.0.0` and all the pods are attached to it.
	- When we deploy multiple pods they are assigned a specific IP address from this address.
	- The pods can communicate each other through this IP. 
		- Not the ideal way of establishing communication since the IP addresses can change when pods are recreated.
	- ![[attachments/Pasted image 20220921215907.png]]

- How does networking work in a cluster (multiple nodes)?
- From the above example if we have 2 nodes which are not a part of a cluster they will have the same IP address range.
	- ![[attachments/Pasted image 20220921220137.png]]
	- This isn't going to work
- When a k8s cluster is setup it doesn't automatically setup a network to handle networking.
- **K8s expects us to setup networking to meet certain requirements**.
- These *requirements* are: 
	- *All containers/PODs can communicate to one another WITHOUT NAT*.
	- *All nodes can communicate with all containers and vice-versa WITHOUT NAT*.

- We don't have to setup it from scratch and have help from different sources. This would be needed if we are setting up the cluster from scratch.
	- ![[attachments/Pasted image 20220921220612.png]]

- Once setup they are responsible for managing the networking.
	- It assigns different network address for each network in the node thereby creating a virtual  network.
	- ![[attachments/Pasted image 20220921220832.png]]

- Different ways of accessing the pod inside k8s cluster
	- ![[attachments/Pasted image 20220921221154.png]]
	- ![[attachments/Pasted image 20220921221229.png]] : Node port service

- Different service types
	- ![[attachments/Pasted image 20220921221406.png]]

> [!tip]+ TargetPort is the port of the pod to which the service forwards the request.
> In short TargetPort is the target for the service.
> *All the terms are from the view point of the service*.
> ![[attachments/Pasted image 20220921222726.png]]

- In a service definition file the only mandatory field is port. 
	- If you don't provide NodePort it is assigned by k8s
	- If you don't provide TargetPort it is assumed to be same as Port.
	- *Ports is an array and we can have multiple such port mappings*.

- Sample service file
```yaml
apiVersion: vl
kind: Service
metadata:
	name: myapp-service
	# we can also provide labels but they are not necessary
spec:
	type: NodePort
	ports:
	 - targetPort: 80
	   port: 80
	   nodePort: 30008
	# no match labels key inside selector like we had in ReplicaSet
	selector:
		app: myapp
```

- Service acts as a *load balancer with a random algorithm* with a SessionAffinity set to Yes.
- What happens when pods are distributed across nodes?
	- **We can use IP of any of the nodes**.
	- ![[attachments/Pasted image 20220921223309.png]]

- Use of ClusterIP
	- ![[attachments/Pasted image 20220921223928.png]]
	- Each layer can scale and move without impacting communication between various services.

- **Each service gets an IP and name assigned to it and that is the name that should be used by other pods to access the service**.
	- Application code which needs to communicate with other pods in a different service should also use this name.
	- ![[attachments/Pasted image 20220921225516.png]]
	- Instead of hardcoding we should be using environment variables.
- *ClusterIP is the default type*.

- Disadvantage of NodePort
	- Even if we have pods on only 2 out of 4 nodes they are accessible from the IP addresses of all the nodes.
	- ![[attachments/Pasted image 20220921224350.png]]

> [!note]- We can bootstrap a k8s cluster from scratch using `kubeadm`