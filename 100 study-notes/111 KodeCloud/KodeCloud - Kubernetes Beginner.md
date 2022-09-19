---
created: 2022-09-15 21:09
updated: 2022-09-19 13:12
---
---
**Links**: [[111 KodeCloud Index]]

---
## Basics
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
	name myapp-pod
	labels:
		app: myapp
		type: front-end
spec:
	containers: # container is an array of objects since we can have multiple containers in a pod
		- name: nginx-container
		  image: nginx
```

> [!caution]- It is recommended to use spaces and NOT TABS when writing YAML files.

