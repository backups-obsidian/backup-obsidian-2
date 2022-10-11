---
created: 2022-10-10 19:49
updated: 2022-10-10 19:57
---
---
**Links**: [[111 KodeCloud Index]]

---
## Kube proxy
- Kube proxy is a process that runs on each node in the k8s cluster.
	- Its job is to *look for new services*. 
- **Every time a new service is created it creates appropriate rules on each node to forward traffic to those services**.
	- It does this using *IP tables* rules.

- The kubeadm tool deploys the the kube-proxy as pods on each node.
	- It is *deployed as a daemonset*.