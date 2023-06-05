---
created: 2023-05-16 11:27
updated: 2023-05-31 13:04
---
---
**Links**: [[111 KodeCloud Index]]

---
## Setting up k8s
### Video References
- [KodeCloud Video](https://drive.google.com/file/d/128DNyfWY6-w5bDQWgPRT_hIe2-gXnbcu/view)
- [Kubernetes Multi Master Setup with LoadBalancer on Ubuntu - YouTube](https://www.youtube.com/watch?v=Zxozz8P_l5M)

### Steps for setting up a cluster using kubeadm
- Diagrammatic representation of steps:
	- ![[attachments/Pasted image 20230531120722.png]]
- **Steps**:
	- Provision the VMs.
	- Designate one as the master and others as the worker nodes.
	- Install a container runtime on the hosts.
	- Install kubeadm tool on all the nodes.
	- Initializing the master server.
	- Setup the pod network.
	- Maker worker nodes join the master node.
	- Deploy applications

### Installing kubeapi-server
- If we install k8s using kubeadm then we don't need to worry about installing the kubeapi-server 
	- **kubeadm deploys the kubeapi-server as a pod in the `kube-system` namespace**.
		- ![[attachments/Pasted image 20221010191218.png]]
- If we are setting up the cluster from scratch then we will have to download the binary of kubeapi-server and then run it as a service.
	- We need to specify the location of the etcd server as one of the parameters.
- In a kubeadm setup we can find the configuration options at the location: `/etc/kubernetes/manifests/kube-apiserver.yaml` in the master node.
- In case of scratch we can view it at `cat /etc/systemd/system/kube-apiserver.service`

### Installing etcd
- Depending on how we setup our cluster etcd is deployed differently.
- If we deploy our cluster from *scratch* then we will have to download the binaries of etcd and then *configure etcd as a service in our master node*.
	- There are lot of configurations options but most of them are related to certificates and configuring etcd as a cluster for HA.
	- `advertise-client-urls` is the URL on which etcd listens. *This is the URL that should be configured in the kubeapi-server when it tries to reach the etcd server*.
	- ![[attachments/Pasted image 20221010160843.png]]
- If we setup our cluster using *kubeadm* then kubeadm *deploys the etcd server for us as a pod in the `kube-system` namespace*.
	- We can explore the etcd database by using the `etcdctl` client in the pod.
	- To get all the keys in etcd run: `kubectl exec etcd-master -n kubesystem -- etcdctl get / --prefix -keys-only`
- etcd in HA
	- We need to make sure that the different etcd services know about each other
	- ![[attachments/Pasted image 20221010161256.png]]

#### ETCD commands
- When we download and install *etcd* it *starts a service on port 2379 by default*.
	- We can then attach different clients to the etcd service.
	- `etcdctl` is the default client that comes with etcd.
- Setting values in etcd: `etcdctl set <key1> <value1>`
- Reading values in etcd: `etcdctl get <key1>`
- ETCDCTL can interact with ETCD Server using 2 API versions – Version 2 and Version 3.
- *By default it’s set to use Version 2*. 
	- Each version has different sets of commands. We cannot use version 2 commands with version 3 and vice-versa.
- Apart from that, you must also specify the path to certificate files so that ETCDCTL can authenticate to the ETCD API Server. 
	- The certificate files are available in the etcd-master at the following path.
```bash
--cacert /etc/kubernetes/pki/etcd/ca.crt  
--cert /etc/kubernetes/pki/etcd/server.crt  
--key /etc/kubernetes/pki/etcd/server.key
```

- Command to get the 10 keys from the etcd server running in a pod:
```bash
kubectl exec etcd-controlplane -n kube-system -- sh -c "ETCDCTL_API=3 etcdctl get / --prefix --keys-only --limit=10 --cacert /etc/kubernetes/pki/etcd/ca.crt --cert /etc/kubernetes/pki/etcd/server.crt --key /etc/kubernetes/pki/etcd/server.key"
```

### Installing Scheduler
- *All the controllers are packaged into a single process known as the kube-controller-manager*.
- If the cluster has to be setup from scratch then we need to download the binary of kube controller manager and configure it to run as a service.
	- We can view the options at: `cat /etc/systemd/system/kube-controller-manager.service`
- In case of kubeadm the kube-controller-manager is deployed as pod in the `kube-system` namespace.
	- We can see the options using `cat /etc/kubernetes/manifests/kube-controller-manager.yaml` in the master node.

### Installing kubelet
- **If we use kubeadm to setup the cluster it DOES NOT install the kubelet**
	- This is the difference from other components 
	- *We must manually install kubelet on the worker nodes*.
- We have to download the binary and **run it as a service** on the worker node.
- To find the location of the binary we can use `which kubelet`.
- To view the status of the service we can use `sudo systemctl status kubelet`
- *kubelet has its own kubeconfig* file at `/etc/kubernetes/kubeconfig.conf`.
	- **kubelet is just like another user so if it needs to issue api calls to the api-server then it needs to have its own certificates, user etc**.
