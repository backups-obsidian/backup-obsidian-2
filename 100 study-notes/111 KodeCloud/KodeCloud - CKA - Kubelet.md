---
created: 2022-09-15 21:09
updated: 2022-10-10 19:48
---
---
**Links**: [[111 KodeCloud Index]]

---
## Kubelet
- Kubelet in the worker node registers the node with the k8s cluster.
- When it receives request from the api server to run a pod it instructs the container runtime to pull the image and run the pod.
- Kubelet continues to monitor the state of pods and reports to the kubeapi-server on a timely basis.

- **If we use kubeadm to setup the cluster it DOES NOT install the kubelet**
	- This is the difference from other components 
	- *We must manually install kubelet on the worker nodes*.
- We have to download the binary and run it as a service on the worker node.