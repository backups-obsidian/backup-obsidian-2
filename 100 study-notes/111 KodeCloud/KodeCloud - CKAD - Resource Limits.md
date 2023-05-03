---
created: 2022-09-15 21:09
updated: 2023-05-03 10:42
---
---
**Links**: [[111 KodeCloud Index]]

---
## Resource Limits
- If there are no sufficient resources available on any of the nodes k8s holds back on scheduling the pod.
	- The pod will be in a *pending state*.
	- ![[attachments/Pasted image 20220924124338.png]]

> [!note]- Kubernetes *DOESN'T provide default resource limits out-of-the-box*. This means that unless you explicitly define limits, your containers **can consume unlimited CPU and memory**.
> The default resource request for a container in Kubernetes is 0 CPU and 0 memory, which means the container is allowed to use any available CPU and memory resources on the node where it is scheduled.

- *Resource limits* are enforced at the *container level* but are usually defined as part of the Deployment.
- We can also define a **default limit for pods that don’t specify their own limits**.
	- This is done by creating a`LimitRange` in the relevant namespace.

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
	- It will **override the `LimitRange` specifications**.

- Sample pod definition file with resource request
```yaml
apiVersion: v1
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

- Conversion chart for memory
	- ![[attachments/Pasted image 20220924125306.png]]

> [!important]- *In docker* a *container* **has NO limit to the resources it can consume on a node**.
> It can consume as much resource it requires suffocating the native processes on the node or other containers of the resources.

> [!question]- DOUBT?
> - **By default k8s sets a limit of 1 vCPU on the pods**. 
> - **If NOT specified explicitly then a container will be limited to occupy 1 vCPU only**.
> - Similarly by **default k8s sets a limit** of **512 Mi** on containers.

- Sample pod definition file specifying the new limits
```yaml
apiVersion: v1
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

> [!note]- Limits and requests are set for **each container** in the pod.

> [!question]- What happens when the container tries to get more resources than the limit?
> - In case of CPU k8s throttles the CPU
> - If a pod tries to consume more memory than its limit then it will be terminated.

- The status `OOMKilled` indicates that *it is failing because the pod ran out of MEMORY*.
- To understand what limits will be applied on the container with various combinations of `LimitRange` and container limits specified in the manifest file refer this link: [Configure Default Memory Requests and Limits for a Namespace | Kubernetes](https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/)

## Example 
- This example will help in understanding limit range and proving the that pods without  limits can consume infinite resources.
- Creating a pod without creating a limit range:
	- `k run pod --image=nginx`
	- `k get pod/pod -o yaml | grep -i -A5 Limits`
		- This gives no output.
	- `kubectl apply -f https://k8s.io/examples/admin/resource/memory-defaults.yaml`
		- Creating a memory limit range
	- `k run pod2 --image=nginx`
	- `k get pod/pod2 -o yaml | grep -i -A5 Limits`
		- ![[attachments/Pasted image 20230503104048.png]]
- Since the first pod had no limits this proves that the pods without limits can consume any amount of resources from the node.