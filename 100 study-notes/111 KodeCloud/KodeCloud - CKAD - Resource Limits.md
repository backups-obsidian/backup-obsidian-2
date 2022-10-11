---
created: 2022-09-15 21:09
updated: 2022-10-10 22:40
---
---
**Links**: [[111 KodeCloud Index]]

---
## Resource Limits
- If there are no sufficient resources available on any of the nodes k8s holds back on scheduling the pod.
	- The pod will be in a *pending state*.
	- ![[attachments/Pasted image 20220924124338.png]]

- *By default* k8s assumes that a pod or a container within a pod **0.5 CPU and 256 Mi** (Megabytes) of memory.
	- This is known as the resource request for the container. 
	- The minimum amount of CPU and Memory requested by the container.
	- For the POD to pick up those defaults you must have *first set those as default values for request and limit by creating a LimitRange in that namespace*.

- LimitRange yaml files
```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: mem-limit-range
spec:
  limits:
  - default:
      memory: 512Mi
    defaultRequest:
      memory: 256Mi
    type: Container
```

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: cpu-limit-range
spec:
  limits:
  - default:
      cpu: 1
    defaultRequest:
      cpu: 0.5
    type: Container
```

- When the scheduler tries to place the pod on a node it uses these numbers to identify the node which has sufficient amount of resources available.
- If we feel that our application will need more than the default resource request, we can specify it in the pod or deployment definition file.
	- We *specify it at the container level*.

- Sample pod definition file with resource request
```yaml
apiVersion: vl
kind: Pod
metadata:
	name: simple-webapp-color
	labels:
		name: simple-webapp-color
spec:
	containers:
	- name: simple-webapp-color
	  image: simple-webapp-color
	  ports:
	  - containerPort: 8080
	  resources:
		requests:
			memory: "1Gi"
			cpu: 1
```

> [!note]- For **CPU** we can specify it as low as **0.1** which can be also expressed as **100 m**.
> Here m stands for milli 
> The lowest we can specify is **1m**.

- **1 count of CPU is equivalent to 1 vCPU**.
	- 1 AWS vCPU
	- 1 GCP Core
	- 1 Azure Core
	- 1 Hyperthread

- We can request a higher number of nodes depending if the nodes have that much to offer.

- Conversion chart for memory
	- ![[attachments/Pasted image 20220924125306.png]]

> [!important]- *In docker* a *container* **has NO limit to the resources it can consume on a node**.
> It can consume as much resource it requires suffocating the native processes on the node or other containers of the resources.

- **By default k8s sets a limit of 1 vCPU on the pods**. 
	- If not specified explicitly then a container will be limited to occupy 1 vCPU only.
- Similarly by **default k8s sets a limit** of **512 Mi** on containers.

- Sample pod definition file specifying the new limits
```yaml
apiVersion: vl
kind: Pod
metadata:
	name: simple-webapp-color
	labels:
		name: simple-webapp-color
spec:
	containers:
	- name : simple-webapp-color
	  image: simple-webapp-color
	  ports:
	  - containerPort: 8080
	  resources:
		requests:
			memory: "1Gi"
			cpu: 1
		limits:
			memory: "2Gi"
			cpu: 2
```

- In the above definition file when a pod is created k8s sets new limit for the container.

> [!note]- Limits and requests are set for *each container* in the pod.

> [!question]- What happens when the container tries to get more resources than the limit?
> - In case of CPU k8s throttles the CPU
> - If a pod tries to consume more memory than its limit then it will be terminated.

- The status `OOMKilled` indicates that *it is failing because the pod ran out of MEMORY*.