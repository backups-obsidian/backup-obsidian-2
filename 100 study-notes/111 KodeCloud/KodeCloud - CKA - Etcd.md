---
created: 2022-10-10 19:07
updated: 2022-10-10 19:07
---
---
**Links**: [[111 KodeCloud Index]]

---
## ETCD
- ETCD is a **distributed** reliable **key value store** that is simple secure and **fast**.
- Key value databases are used to store and retrieve small chunks of data such as *configuration data that requires fast read and writes*.
- When we download and install *etcd* it *starts a service on port 2379 by default*.
	- We can then attach different clients to the etcd service.
	- `etcdctl` is the default client that comes with etcd.
- Setting values in etcd: `etcdctl set <key1> <value1>`
- Reading values in etcd: `etcdctl get <key1>`

### ETCD in k8s
- Etcd stores information regarding the cluster such as nodes, pods, configs, secrets, etc.

> [!note]- **Every information we see when we run the `k get` command is from the etcd server**.

- *Every change we make to our cluster* is updated in the etcd cluster.
	- Only once the change is updated in the etcd cluster it is considered to be complete.
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

### ETCD commands
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