---
created: 2023-05-05 14:50
updated: 2023-05-16 07:13
---
---
**Links**: [[111 KodeCloud Index]]

---
## Events
- We track metrics using Prometheus and Grafana.
- We track application logs using ELK or fluentd.
- k8s generates a lot of events related to deployment, scheduling etc.
- Events are a great source of information to understand what is currently happening in our cluster.
- To get the event of a particular resource we can use `describe`
	- `k describe <resource-type> <resource-name>`
- To get all the events we can use:
	- `k get events`
- Some important events:
	- `Crashloopbackoff`: A container within a Pod repeatedly *crashes immediately after starting up*. When this happens, Kubernetes will automatically restart the container to try to recover it, but if it *crashes again within a short period of time*, Kubernetes will give up and mark the container as "CrashLoopBackOff".
	- `Imagepullbackoff`: When a container within a Pod *fails to pull its container image* from the specified image repository. When this happens, Kubernetes will try to pull the image several times, and if it continues to fail, it will mark the container as "ImagePullBackOff".

## Setting up k8s
### Installing kubeapi-server
- If we install k8s using kubeadm then we don't need to worry about installing the kubeapi-server 
	- **kubeadm deploys the kubeapi-server as a pod in the `kube-system` namespace**.
		- ![[attachments/Pasted image 20221010191218.png]]
- If we are setting up the cluster from scratch then we will have to download the binary of kubeapi-server and then run it as a service.
	- We need to specify the location of the etcd server as one of the parameters.
- In a kubeadm setup we can find the configuration options at the location: `/etc/kubernetes/manifests/kube-apiserver.yaml` in the master node.
- In case of scratch we can view it at `cat /etc/systemd/system/kube-apiserver.service`

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