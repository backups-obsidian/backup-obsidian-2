---
created: 2022-10-10 19:28
updated: 2022-10-12 23:19
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

- So if don't have a scheduler then one way of scheduling the pods would be specifying the `nodeName`
- *A pod remains in a PENDING state unless it is scheduled by the scheduler*.

- If for some reason the default scheduler doesn't meet our needs we can write our own scheduler.
	- When creating a pod or a deployment we can instruct k8s to have pod scheduled by our custom scheduler.

## How does the scheduler work?
- Lets suppose we submit a pod definition file to the kubeapi-server. 
- It asks the scheduler to find the right node for the pod depending on the requirements.
- The first step in the creation of pods is that they end up in a scheduling queue.
	- This is where the pods wait to be scheduled.
- **Pods are sorted based on the priority defined on the pods** in the scheduling queue.
- To set a priority we must first create a priority class/object and set a priority value.
- ![[attachments/Pasted image 20221012230631.png]]
- After the sorting/queuing phase the pods enter the **filtering phase** where the *nodes that cannot run the pod are filtered out*.
- After the filtering phase we have the **scoring phase**.
	- In this nodes are scored with different weights.
	- On the remaining nodes the scheduler associates a score with each node after reserving the CPU for the pod.
	- ![[attachments/Pasted image 20221012231013.png]]
- The second node has the higher score and hence it gets picked up.
- After the scoring phase we have the **binding phase** in which the pod is bound to the node.
- All of these processes are achieved using a plugin.
	- ![[attachments/Pasted image 20221012231325.png]]
- The highly customisable nature of k8s ensures that we can write our own plugin.
	- This is achieved using **extension points**.
- At each stage there is an extension point to which a plugin can be plugged to.
	- ![[attachments/Pasted image 20221012231551.png]]
	- *Some plugins extend to multiple extension points*.