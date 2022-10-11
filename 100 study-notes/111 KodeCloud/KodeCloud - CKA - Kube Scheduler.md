---
created: 2022-10-10 19:28
updated: 2022-10-10 21:52
---
---
**Links**: [[111 KodeCloud Index]]

---
## Scheduler
> [!caution]- It is **only responsible for deciding which pod goes on which node**, it **DOESN'T actually place the pod on the node**. 
> That is the job of the kubelet.

- If we are setting up the scheduler from scratch then we can download the binary and run it as a service.
- kubeadm tool deploys the kube-scheduler as a pod in the `kube-system` namespace on the master node.
	- Location to view the configuration on the master node: `cat /etc/kubernetes/manifests/kube-scheduler.yaml` 

> [!question]- How does a scheduler work in the backend?
> - Every pod has a field called `nodeName` in `spec` that by default is not set. 
> - We don't specify this field when we create the pod manifest file. k8s adds it automatically.
> - The scheduler goes through all the pods and looks for those that do not have this property.
> - It then runs the scheduling algorithm to find the right node for the pod by setting the nodeName property to the name of the node.
> - It does this by creating a binding object.

- *A pod remains in a PENDING state unless it is scheduled by the scheduler*.

