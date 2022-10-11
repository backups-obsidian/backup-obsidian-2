---
created: 2022-10-10 19:16
updated: 2022-10-10 19:38
---
---
**Links**: [[111 KodeCloud Index]]

---
## Kube controller manager
- **A controller is a process that continuously monitors the state of various components within the system and works towards bringing the whole system to the desired working state**.
- *Node controller* is responsible for monitoring the status of the nodes.
	- Node controller checks the status of nodes every 5s. 
- *Replication controller* is responsible for monitoring the status of replica sets and ensures that the desired number of pods are available at all times.
- There are many more controllers available in k8s.
	- ![[attachments/Pasted image 20221010192001.png]]

- *All the controllers are packaged into a single process known as the kube-controller-manager*.

- If the cluster has to be setup from scratch then we need to download the binary of kube controller manager and configure it to run as a service.
	- We can view the options at: `cat /etc/systemd/system/kube-controller-manager.service`
- In case of kubeadm the kube-controller-manager is deployed as pod in the `kube-system` namespace.
	- We can see the options using `cat /etc/kubernetes/manifests/kube-controller-manager.yaml` in the master node.