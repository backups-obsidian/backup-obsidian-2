---
created: 2022-09-15 21:09
updated: 2022-09-24 16:44
---
---
**Links**: [[111 KodeCloud Index]]

---
## Taints & Tolerations
- We can restrict what pods are placed on what nodes.
- Taints and tolerations are used to restrict what pods can be scheduled on a node.
- Procedure of placing a particular pod on a node:
	- We first taint the node (node 1) where we want to place the desired pod (pod D).
	- Since **pods have NO toleration by default** none of the pods can *tolerate the taint*. 
	- Now no unwanted pods are going to be placed on the tainted node (node 1).
	- Now we apply a tolerant to the pod (pod D) which we want to place on the tainted node (node 1).
	- ![[attachments/Pasted image 20220924161048.png]]
	- ![[attachments/Pasted image 20220924161148.png]]

> [!important]- **Taints** are set on **nodes** and **tolerations** are set on **pods**.

- Tainting a node:
	- We taint a node using: `kubectl taint nodes <node-name> key=value:taint-effect`
	- *Taint is a key value pair*.
	- `taint-effect` defines what would happen to the pod if they do not tolerate the taint.
- There are 3 different kinds of `taint-effect`
	- ![[attachments/Pasted image 20220924161501.png]]
	- `NoSchedule`: Pods will not be scheduled on the node.
	- `PreferNoSchedule`: System will try to avoid placing a pod on the node. Not guaranteed.
	- `NoExecute`: New pods will not be scheduled on the node and existing pods on the node if any will be evicted if they do not tolerate the taint. These pods may have been scheduled on the node before taint was applied to the node.

- Sample pod definition file with tolerations
```yaml
apiVersion: v1
kind: Pod
metadata:
	name: myapp-pod
spec:
	containers:
	- name: nginx-container
	  image: nginx
	tolerations:
	- key: "app"
	  operator: "Equal"
	  value: "blue"
	  effect: "NoSchedule"
```

- *All the values in tolerations must be enclosed within quotes"
- The above pod definition file will tolerate the following taint: `k taint nodes node1 app=blue:NoSchedule`

> [!caution]+ **Taints and tolerations does not tell the pod to go to a particular node. Instead it tells nodes to only accept pods with certain tolerations.**
> ![[attachments/Pasted image 20220924162514.png]]
> If we want to **restrict a pod to a particular node then it is achieved using node affinity**.

> [!note]- If we notice master node is also a node and scheduler doesn't schedule any pod on the master node.
> When a k8s cluster is created a taint is set on the master node automatically which prevents any pod from being scheduled on the master node.
> We can view this taint using: `kubectl describe node/kubemaster | grep Taint`
> We have a taint of `NoSchedule` hence no pods are scheduled on the master node.
> ![[attachments/Pasted image 20220924162951.png]]

- Un tainting the node: `k taint nodes <node-name> key=value:taint-effect-`
	- ![[attachments/Pasted image 20220924164243.png]]

- **Removing the taint of the control plane**:
	- `kubectl taint nodes controlplane node- role.kubernetes.io/master:NoSchedule-`
	
### Understanding NoExecute
- Initial scenrio:
	- ![[attachments/Pasted image 20220924162247.png]]
- Now we decide to taint node 1 and add a toleration to pod D.
- Once applied node evicts pod C (killed) whereas pod D continues to run.