---
created: 2022-08-11 19:22
updated: 2023-05-08 14:08
---
---
**Links**: [[110 Kubernetes Index]]

---
- Architecture diagram for reference
	- ![[attachments/Pasted image 20220811193418.png]]

## Components
1. `api-server` is responsible for managing everything. 
	1. It talks to the UI, API and the CLI.
	2. `api-server` or `kube-apiserver` is **how we talk and interact with the cluster** (*entry point to the k8s cluster*).

2. `etcd` is a **highly available key value store**. 
	1. It is *only accessible via the api server for security reasons*. 
	2. It has all the configuration and status data of kubernetes.

3. A `scheduler` **identifies the right node to place a pod** on based on different requirements and available resources. 
	1. It is also known as the **control plane**.
	2. It ensures pod placement.

4. A `kubelet` is an **agent that runs on each worker node in a cluster**. 
	1. It listens for instructions from the `kubeapi-server` or `api-server` and deploys and destroys containers on the node as required.

5. `Controllers`/`controller-manager` is the **brain behind orchestration they are responsible for noticing and responding when pods go down**.  ^9d5e63
	- It *consists of several controllers* like the deployment controller, replicas set controller etc.
	- In Kubernetes, controllers are control loops that watch the state of your cluster, then make or request changes where needed.

7. **Container runtime** is the underlying software used to run containers. 
	1. Some container runtimes are containerd, docker, rocket, etc.

8. Suppose you have a database server running on a pod on one of the worker nodes and the application server running on a pod in some other worker node. 
	1. **Communication between worker nodes is facilitated using** `kube-proxy`.

> [!note]- We can have multiple pods on a single worker node. 
> - So it is possible to have 3 worker nodes and 20 pods. 
> - It is Kubernetes job (**scheduler**) to take care of resources and decide which pod should go to which worker. 