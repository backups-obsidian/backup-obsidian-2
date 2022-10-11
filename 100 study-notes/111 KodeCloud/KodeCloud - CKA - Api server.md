---
created: 2022-10-10 19:08
updated: 2022-10-10 19:38
---
---
**Links**: [[111 KodeCloud Index]]

---
## Kube API Server
- When we use the kubectl command it actually interacts with the kubeapi-server.
	- We need not use kubectl for interacting with the kubeapi-server. 
	- We can invoke the apis directly by sending a POST request.

### What happens when we create a pod using kubectl
![[attachments/Pasted image 20221010190526.png]]
- The request is authenticated and then validated.
- Api server creates a pod object without assigning it to the node, updates the information in the etcd server and updates the user that the pod has been created.
- *The scheduler continuously monitors the api server and sees that there is a new pod with no node assigned*. 
- The scheduler identifies the right node to place the pod on and communicates back to the kubeapi-server.
- The api server then updates the information in the etcd server.
- The api server then passes the information to the kubelet in the appropriate worker node.
- The kubelet then instructs the container runtime to create a pod on the node.
- Once done the kubelet updates the status back to the api server.
- The api server then updates the data in the etcd server.
- A similar pattern is followed anytime a change is requested.
- kubeapi-server is at the center of all the different tasks that need to be performed.

---

> [!caution]- Kubeapi-server is the only component that interacts with the etcd data store.

### Installing kubeapi-server

- If we install k8s using kubeadm then we don't need to worry about installing the kubeapi-server 
	- **kubeadm deploys the kubeapi-server as a pod in the `kube-system` namespace**.
		- ![[attachments/Pasted image 20221010191218.png]]
- If we are setting up the cluster from scratch then we will have to download the binary of kubeapi-server and then run it as a service.
	- We need to specify the location of the etcd server as one of the parameters.
- In a kubeadm setup we can find the configuration options at the location: `/etc/kubernetes/manifests/kube-apiserver.yaml` in the master node.
- In case of scratch we can view it at `cat /etc/systemd/system/kube-apiserver.service`