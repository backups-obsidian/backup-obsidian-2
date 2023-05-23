---
created: 2023-05-19 15:00
updated: 2023-05-19 16:44
---
---
**Links**: [[111 KodeCloud Index]]

---
## Backup & Restore
- What should be backed up in a cluster?
	- **Resource configuration**
	- **ETCD cluster**
	- **Persistent volumes**
- Resources can be created using the *imperative* (`create` commands) or the *declarative* way (`apply` commands).

### Backing up resource configuration
- `kubectl get all --all-namespaces -o yam] > all-deploy-services.yaml`
	- **Since all the resources in the cluster MAY NOT  be created by declarative methods** like someone using imperative ways to create a resource.
- We can use use velero (3rd party) for resource configuration backups.


### Backing up & Restoring etcd
- When we start the etcd server we specify the data directory.
- We can *back up the data directory* using our back up tool.
- etcd also comes with a built in **snapshot feature** so we can create a snapshot and then restore the snapshot.
- *Creating a snapshot*: 
```bash
ETCDCTL_API=3 etcdctl --endpoints=https://127.0.0.1:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key snapshot save /location.db
```

> [!note]- We **DON'T need certificates** when *checking the status of the snapshot* or *restoring the snapshot* since we are *NOT connecting with etcd*.
> We were **connecting with etcd while saving the snapshot** and hence needed certificates.

- Viewing the status of the snapshot: 
```bash
ETCDCTL_API=3 etcdctl snapshot status /location.db
```

- **Restoring the snapshot**:
```bash
ETCDCTL_API=3 etcdctl snapshot restore --data-dir /new-location /location.db
```

- Now we need to **update the data directory in the manifest file of etcd**.
	- `vim /etc/kubernetes/manifests/etcd.yaml`
	- ![[attachments/Pasted image 20230519153840.png]]
- *Any change in the `etcd.yaml` file means that kubelet will recreate the etcd pod and it will use the new location as the data directory*.
	- If the pod is in a pending state for a long time then just deleting (`k delete pod/etcd-controlplane -n kube-system`) it might fix the issue by forcing kubelet to create a new one instead of restarting.
 
> [!note]- In a kubeadm setup when etcd restarts it **also restarts the controller manager and the scheduler**.
> ![[attachments/Pasted image 20230519154318.png]]

- If *etcd is running as a service* then look for the service file (`systemctl status etcd`)
	- Then *change the data directory* in the service file.
	- *Reload the daemon since service file has changed*: `systemctl daemon-reload`
	- Restart etcd: `systemctl restart etcd`

