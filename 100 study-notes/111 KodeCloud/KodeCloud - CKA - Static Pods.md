---
created: 2022-09-15 21:09
updated: 2023-05-15 15:46
---
---
**Links**: [[111 KodeCloud Index]]

---
## Static pods
- Kubelet can manage a node independently.
- We need to provide pod definition files to kubelet to create pods.
	- Assuming there is no kubeapi-server to provide kubelet with the pod definition files we need to place the files in `/etc/kubernetes/manifests` folder.
	- Kubelet constantly checks this folder for any manifests file.
- Kubelet will create the pod and ensure that it is alive. 
	- If for some reason the pod crashes kubelet will restart the pod.
- If a file is removed from this directory then the pod is deleted automatically.
- Any modifications made to the file ensures that the pod is modified.

> [!note]- *The pods created by the kubelet without the intervention of the api server are known as static pods*.
> We can only create pods this way. 
> We cannot create replicasets or deployments this way.
> ![[attachments/Pasted image 20221010231849.png]]

- Kubelet works at a pod level and can only understand pods.
- The location to place the pod manifest files can be any directory on the host. 
	- To configure the directory it has to be passed to the kubelet while running the service.
	- The option is named `--pod-manifest-path`
		- ![[attachments/Pasted image 20221010231841.png]]
	- In case the we are using the kubeadm setup the configuration options can be found at `cat /var/lib/kubelet/config.yaml`

- Kubelet can create both static pods and the pods from the kubeapi-server at the same time.
- **The api server is aware of the static pods created by kubelet**.
	- We only see a read only mirror of the static pod.
	- We can see the details of the pod but we cannot edit or delete it like normal pods.
	- *Name of the pod is automatically appended with node name*.

 ### Why use static pods?
 - Since static pods are *not dependent on the control plane*, **static pods can be used to deploy  the control plane components as pods on a node**.
- Install kubelet on all the master nodes.
- Use docker images of the various control plane components and place them in the `/etc/kubernetes/manifests` directory.
	- ![[attachments/Pasted image 20221010233035.png]]
- Kubelet takes care of deploying the control plane component as pods.
- *This way we don't have to download the binaries, configure services or worry about the services crashing*.
	- If any of these pods were to crash it would be automatically restarted by the kubelet
- **This is how the kubeadm tool sets up the k8s cluster**.
	- This is why we see control plane components as pods in the `kube-system` namespace.
- Since the name of the node gets appended to the name of the pod we get names like this:
	- ![[attachments/Pasted image 20221010233913.png]]
	- ![[attachments/Pasted image 20221010233937.png]]

---

- Difference between static pods and daemon sets
	- ![[attachments/Pasted image 20221010233628.png]]

---

- Here is a real-world example use case of the static pod.
- While bootstrapping the control plane, kubelet starts the api-server, scheduler, and controller manager as static pods from podSpecs located at `/etc/kubernetes/manifests`