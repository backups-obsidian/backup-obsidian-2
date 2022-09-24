---
created: 2022-09-23 17:15
updated: 2022-09-24 19:27
---
---
**Links**: [[111 KodeCloud Index]]

---
## Node Selectors
- Let us assume that we have 2 small nodes and 1 large node.
- We would like to send the demanding pods to the larger node.
	- We can use node selectors for this.
	- ![[attachments/Pasted image 20220924165323.png]]
- *Prior to using node selectors in pod definition file the nodes must be labelled*.
- Labelling a node: `k label nodes <node-name> <label-key>=<label-value>`

- Sample Pod definition file with node selector:
```yaml
apiVersion:
kind: Pod
metadata:
	name: myapp-pod
spec:
	containers:
	- name: data-processor
	  image: data-processor
	nodeSelector:
	  size: Large
```

- Node selectors server the purpose but they *have limitations*
	- We cannot provide advanced expression like place the pod on a large or medium node Or place the pods on any nodes that are not small.
	- We cannot achieve this using node selectors.

## Node Affinity
- The primary use of Node affinity is to ensure that pods are hosted on particular nodes.
- Sample pod definition file with Node Affinity
```yaml
apiVersion: v1
kind: Pod
metadata:
	name: myapp-pod
spec:
	containers:
	- name: data-processor
	  image: data-processor
	affinity:
	  nodeAffinity:
	    requiredDuringSchedulingIgnoredDuringExecution:
		  nodeSelectorTerms:
		  - matchExpressions:
			- key: size
			  operator: In
			  values:
			  - Large
			  - Medium
```
- In the above example the pods will be placed on nodes with labels including large and medium value.
- We can use the `NotIn` operator to not place on a node with a particular value.
- There are lot of operators. Check the documentation to know more.

> [!caution]- A combination of Taints & Tolerations and Node Affinity rules are used to place pods on the desired node.
> - If we just use Taints & Tolerations then it would mean that other pods can't be placed in our node but it is possible that the required pod may not be placed in the required node.
> ![[attachments/Pasted image 20220924184513.png]]
> - If we just use Node Affinity then we can guarantee that the pod will go to the desired node but it is possible that other pods get placed in the desired node.
> ![[attachments/Pasted image 20220924184659.png]]

### Different types of Node Affinity
- The *type of node affinity defines the behaviour of scheduler wrt to node affinity and stages in the lifecycle of the pod*.
	- ![[attachments/Pasted image 20220924182329.png]]
	- Planned means planned for some future release
- ![[attachments/Pasted image 20220924182835.png]]
	- `requiredDuringSchedulingIgnoredDuringExecution` means that if the scheduler cannot find the node for the pod then the node will not be scheduled.
	- `requiredDuringSchedulingIgnoredDuringExecution` means scheduler tries its best to get the right node for the pod but even if it is not found the pod will be scheduled.
	- `DuringExecution` - this means the pod has been running but a change has been made in the node affinity. Like an admin removed a label from the node.