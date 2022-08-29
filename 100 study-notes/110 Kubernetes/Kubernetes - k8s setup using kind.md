---
created: 2022-08-28 12:56
updated: 2022-08-29 17:42
---

## k8s setup using Kind
- `kind` stands for kubernetes in docker.
- In `minikube` you are limited to clusters with only one node. 
	- It is the worker node as well as the control plane. 
	- It creates only one docker container.
- In `kind` **we can create clusters with multiple nodes**. 
	- Each node is a docker container.

### Commands
- Installing `kind`: 
	- `brew install kind`
- Creating a single node cluster
	- `kind create cluster --name cluster-name`
	- The clusters are created with a name `kind-cluster-name`
	- The *default cluster name* is `kind`. So if you don’t give any arguments while creating a cluster you will get a cluster named `kind kind`.
- List all the clusters
	- `kind get clusters `
- **Creating a multi node cluster**
	- `kind create cluster --config kind-example-config.yaml --name cluster-name`

```yaml
# three node (two workers) cluster config
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker
```

- If you create a multi master cluster then you will be accessing the cluster over a load balancer.
	- So if you create a 3 master 1 worker node cluster then there will be 5 docker containers (1 extra for the load balancer)
	- ![[attachments/Pasted image 20220829174134.png]]