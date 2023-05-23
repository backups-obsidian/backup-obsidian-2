---
created: 2023-05-19 14:59
updated: 2023-05-19 14:59
---
---
**Links**: [[111 KodeCloud Index]]

---
## Cluster Upgrade
### Understanding k8s releases
- Kubernetes releases follow the `major.minor.patch` standard.
	- Before a *feature or bug fix* makes it to a release it is **first released as an alpha release** (*disabled by default* in the cluster).
	- From there they make their way to the **beta release** (*well tested, enabled by default*) then finally to the main release version.
	- Schedule diagram:
		- ![[attachments/Pasted image 20230519130658.png]]
- When we download the latest release of k8s we see that most of the control plane components have the same version except for *etcd* and *coredns* have their own versions as they are *separate projects*.
	- ![[attachments/Pasted image 20230519131119.png]]

> [!important]- It is *NOT mandatory for the control plane components to be of the same version*.
> - Since kubeapi-server is the core component and all other components talk to it **no component should have a version higher than the kube-api server except for kubectl**.
> ![[attachments/Pasted image 20230519131556.png]]

- k8s only *supports recent 3 minor versions*.
	- ![[attachments/Pasted image 20230519131737.png]]
- The **recommended way of upgrading k8s is one minor version at a time**.
- Depending on how the cluster is managed it can be easy or difficult to upgrade it.
	- ![[attachments/Pasted image 20230519132021.png]]

### Steps for upgrading the cluster
- Upgrading the cluster is a two step process.
	- We first **upgrade the master node and then the worker nodes**.
- While the *master is being upgraded* the *control plane components* like the api-server, controller manager, scheduler etc *go down briefly*.
	- Even though the *master node is down* the **workloads in the worker nodes are still up and running**.
	- But since the *master is down, all the management functions are down*. 
		- We *CANNOT access the cluster using kubectl*.
		- We CANNOT delete, create or deploy applications.
		- Controller managers DON'T function, so if *a pod were to fail it WON'T be created automatically*.
- Once the master has been upgraded it will be at version X+1 whereas the workers nodes will be at version X.
- There are **different strategies for [[KodeCloud - CKA - OS Upgrades| upgrading the worker nodes]]**:
	- *Upgrading all of them at once*.
		- This will result in downtime.
	- *Upgrading one node at a time*.
		- This is done by draining the nodes and then uncordoning them once the node is done upgrading.
		- ![[attachments/Pasted image 20230519134753.png]]
	- *Adding new nodes to the cluster with upgraded version*.
		- Move the workload to the new and remove the node with the old version

### Kubeadm Cluster upgrade example
- After upgrading the control plane components we must **manually upgrade the kubelet**.
	- Since *kubeadm DOES NOT install or upgrades the kubelet*.
- We *must first upgrade the kubeadm tool before we can upgrade the cluster*.
	- The kubeadm tool follows the same software version as k8s.
- Lets suppose we are at 1.11 and want to go to 1.13.
	- Since we have to upgrade only one version at a time we first go to 1.12 and then go to 1.13.
- Upgrading the kubeadm tool:
	- `apt-get upgrade -y kubeadm=1.12.0-00`
- Upgrading the cluster:
	- `kubeadm upgrade apply v1.12.0`
	- It pulls the necessary images and once complete the control plane is at 1.12

> [!important]- If we run the `k get nodes` command we will see the master node at 1.11.
> - This is because in the **output it is showing versions of kubelets in each of these nodes and NOT the version of the api-server**.
> ![[attachments/Pasted image 20230519140626.png]]
> - Depending on our setup we may or may not have kubelets on our master node.
> - *The cluster deployed using kubeadm has kubelet on the master node* which is used to run the control plane components as [[KodeCloud - CKA - Static Pods | static pods]].
> - If we install a k8s cluster from scratch then we WON'T have a kubelet on the master node and we WON'T see the master node in `k get nodes` output.

- We upgrade the kubelet on the master node and restart the service (since kubelet is installed as a service on the nodes):
	- `apt-get upgrade -y kubelet=1.12.0-00`
	- `systemctl restart kubelet`
- Running the `k get nodes` command will show that the master node has been upgraded to 1.12
	- ![[attachments/Pasted image 20230519135913.png]]
- For upgrading the worker nodes to 1.12 we drain the node, upgrade the kubelet and kubeadm packages, uncordon the node and then repeat this process for other nodes.
	- `kubectl drain node-1`
	- `apt-get upgrade -y kubeadm=1.12.0-00`
	- `apt-get upgrade -y kubelet=1.12.0-00`
	- `kubeadm upgrade node config - -kubelet-version v1.12.0`
	- `systemctl restart kubelet`
	- `kubectl uncordon node-1`