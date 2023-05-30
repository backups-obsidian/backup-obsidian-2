---
created: 2023-05-29 16:11
updated: 2023-05-30 12:47
---
---
**Links**: [[111 KodeCloud Index]]

---
## DNS in k8s
- Whenever a service is created k8s DNS service creates a record for the service.
	- It maps the service name to the IP of the service.
- **FQDN format: `<hostname>.<namespace>.<type>.<root>`**
- FQDN for service resolution:
	- `<service-name>.<namespace>.svc.cluster.local`
	- Diagram:
		- ![[attachments/Pasted image 20230530122156.png]]
- *Records for pods are NOT created by default*.
	- Once enabled records are created for pods as well.
	- For the host name it uses the pod IP with `.` replaced with `-`.
	- The `<type>` is `pod`.
	- Diagram:
		- ![[attachments/Pasted image 20230530122401.png]]
- The DNS server in k8s is known as **CoreDNS**.
	- *It is deployed as a pod in the kube-system namespace*.
- CoreDNS pod reads the configuration settings from a file known as `Corefile` which can be found at `/etc/coredns/Corefile` inside the pod.
	- ![[attachments/Pasted image 20230530122904.png]]
- The `Corefile` is passed to the pod as a config map object. 
	- If we have to modify the settings we can edit the config map object.
	- ![[attachments/Pasted image 20230530123220.png]]
- *When we deploy CoreDNS it also creates a service to to make it available to other components within the cluster*.
	- The service is named `kube-dns` by default.
		- ![[attachments/Pasted image 20230530123600.png]]
	- **IP address of CoreDNS service (`kube-dns`) is configured as the nameserver on pods**.
		- ![[attachments/Pasted image 20230530123800.png]]
	- The `/etc/resolv.conf` file also has a *search* entry which allows us to find the service using `service-name` or `service-name.namespace` or `service-name.namespace.svc` or `service-name.namespace.svc.cluster.local`.
		- *NOTE: that the search entries are only for services and we WON'T be able to reach the pod the same way*.
		- We need to provide the FQDN to reach the pods.
		- **`10-244-2-5` wont work but `10-244-2-5.namespace.pod.cluster.local` will work**.
- The *DNS configurations on pods is done automatically by k8s when pods are created*.
	- Kubelet is responsible for this.
	- We can view this at `/var/lib/kubelet/config.yaml` (can be found using `ps -ef | grep kubelet` since kubelet is running as a service)
		- ![[attachments/Pasted image 20230530124131.png]]