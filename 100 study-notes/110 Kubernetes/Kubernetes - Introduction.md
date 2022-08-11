---
created: 2022-08-11 19:14
updated: 2022-08-11 19:26
---
---
**Links**: [[110 Kubernetes Index]]

---
## Introduction
- Kubernetes is an **open source container orchestration tool** which was originally *developed by google*. 
- It helps us manage containerised applications in different deployment environments.
- It also works on the **concept of current state and the desired state** just like *docker-compose*. 
	- The state specified in some configuration yaml file is the desired state.

> [!tip]- Kubernetes is also known as k8s 
> The reason being professionals are lazy and there are 8 letters between k & s in Kubernetes.

- Since k8s deals with containerised applications it supports several runtimes such as `docker`, `cri-o` and `containerd`. 
	- This also means that **k8s can be utilised without docker** as well since it supports other container runtimes.

## Features of k8s
- Automatic deployment of the **containerised** applications across different servers.
- Distribution of the load across multiple servers.
- *Auto-scaling* of the deployed applications.
- Monitoring and health check of the containers.
- *Automatic replacement* of the failed containers.

## Basic Architecture
- We have the **master and the worker nodes**. 
- We can have more than one master to have a more reliable setup. 
- Nodes are physical or virtual machines.

> [!important]+ The worker nodes will need a *container runtime*, *kubelet* and *kube-proxy* installed on them.
> ![[attachments/Pasted image 20220811191319.png]]

- We also have cloud controller manager which comes into play when you are using a k8s cluster hosted on a specific cloud provider.
	- ![[attachments/Pasted image 20220811191354.png]]

- Some responsibilities of the master node
	- Entrypoint to K8S cluster
	- Keeps track of whats happening in the cluster
	- Ensures Pods placement

> [!caution]- `kube-apiserver` is the entry point through which we talk to the cluster.
