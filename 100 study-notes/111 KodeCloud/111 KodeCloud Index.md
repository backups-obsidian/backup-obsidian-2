---
created: 2022-09-13 16:17
updated: 2023-05-15 15:02
---
---
**Links**: 
- [[../100 home| 100 Home]]
- [[../104 Linux/104 Linux Index | Linux Index]]

---
## DevOps Prerequisites 
[[KodeCloud - Package Management using yum]]
[[KodeCloud - Linux Services]]
[[KodeCloud - Networking]]
[[KodeCloud - Applications]]
[[KodeCloud - WebServers]]
[[KodeCloud - Databases]]
[[KodeCloud - SSL-TLS]]
[[../104 Linux/Using openssl | Using openssl]]

## Kubernetes

### Introduction & Basics
[[../110 Kubernetes/Kubernetes - Introduction| Kubernetes - Introduction & Basic Architecture]]

[[KodeCloud - Kubernetes - Pods]]
[[KodeCloud - Kubernetes - ReplicaSets]]
[[KodeCloud - Kubernetes - Deployments]]
[[KodeCloud - Kubernetes - Networking and Services]]

[[../110 Kubernetes/Kubernetes - Chain of events behind running a pod|Kubernetes - Chain of events behind running a pod]]
[[Kubernetes - Objects & Resources]]

### CKAD
[[KodeCloud - CKAD - Namespaces]]
[[KodeCloud - CKAD - Commands and Arguments]]
[[KodeCloud - CKAD - Environment Variables]]
[[KodeCloud - CKAD - Security Context]]
[[KodeCloud - CKAD - Resource Limits]]
[[KodeCloud - CKAD - Service Accounts]]

[[KodeCloud - CKAD - Taints and Tolerations]]
[[KodeCloud - CKAD - Node Selectors and Node Affinity]]

[[KodeCloud - CKAD - Multi Container Pods]]
[[KodeCloud - CKAD - Probes]]
[[KodeCloud - CKAD - Logging]]
[[KodeCloud - CKAD - Labels, Selectors and Annotations]]
[[KodeCloud - CKAD - Jobs]]

[[KodeCloud - CKAD - Network Policies]]
[[KodeCloud - CKAD - Ingress]]

[[KodeCloud - CKAD - Volumes]]
[[KodeCloud - CKAD - StatefulSets]]

[[KodeCloud - CKAD - Helm Charts]]
[[KodeCloud - CKAD - Writing helm charts]]

[[KodeCloud - CKAD - KubeConfig]]

[[KodeCloud - CKAD - API & API Groups]]
[[KodeCloud - CKAD - API Versions]]
[[Kubernetes - Steps of an API Request in API Server]]

[[Kubernetes - Authentication]]
[[KodeCloud - CKAD - Authorization]]
[[KodeCloud - CKAD - RBAC]]
[[KodeCloud - CKAD - ClusterRoles]]

[[Kubernetes - Controllers]]
[[KodeCloud - CKAD - Admission Controllers]]
[[KodeCloud - CKAD - Custom Resource Definition]]
[[KodeCloud - CKAD - Operators]]

[[KodeCloud - CKAD - Deployment Strategies]]

[[KodeCloud - CKAD - Misc]]

### CKA
- Concepts to cover: 
	- etcd
	- kube api server
	- kube controller manager
	- kube scheduler
	- kubelet
	- kube proxy
 
- [[KodeCloud - CKA - Cluster Architecture]]
- [[KodeCloud - CKA - Etcd]]
- [[KodeCloud - CKA - Api server]]
- [[KodeCloud - CKA - Kube Controller Manager]]

- [[KodeCloud - CKA - Kube Scheduler]]
- [[KodeCloud - CKA - Daemon Sets]]

- [[KodeCloud - CKA - Kubelet]]
- [[KodeCloud - CKA - Static Pods]]

- [[KodeCloud - CKA - Kubeproxy]]

- [[KodeCloud - CKA - Cluster Maintenance]]
- [[KodeCloud - CKA - TLS Kubernetes]] - **INCOMPLETE**

- [[KodeCloud - CKA - Security Primitives and Authentication]]

### Self
- [[Kubernetes - Logging]]
- [[Kubernetes - Dumping Ground]]

# Questions
- [ ] We know any pod and communicate with any other pod on the cluster using IPs. Does each pod have a unique DNS name without a service? 
	- [ ] I have written something about headless services in statefulsets and pod dns names but I am not clear about it.
- [ ] How to determine the resources required by each pod so that I can specify it in deployment definition file
- [ ] What is kustomize or something similar to that?
- [ ] Install and use the metrics API server. `k top node`, `k top pods`
- [ ] Kustomize
- [ ] Knative (serverless)
- [ ] Crossplane (IaaC)
- [ ] Kubernetes is a control plane to manage stuff with extensive API backed by controllers.
- [ ] What is a CNI?
- What are finalizers.
- Must have k8s operators?

# Read Later
- [Kubernetes Tutorials For Beginners [43 Comprehensive Guides] (devopscube.com)](https://devopscube.com/kubernetes-tutorials-beginners/)
	- [Etcd Backup And Restore On Kubernetes Cluster [Tutorial] (devopscube.com)](https://devopscube.com/backup-etcd-restore-kubernetes/)
- [Demystifying kubeconfig file | kubeconfig file explained - Part 1 - YouTube](https://www.youtube.com/watch?v=Q74gSxeO4cI)
- [Understanding Kubernetes Networking. Part 1: Container Networking - YouTube](https://www.youtube.com/watch?v=B6FsWNUnRo0)
- [How does Kube API-Server work? Looking into Controller-Manager in detail - YouTube](https://www.youtube.com/watch?v=mOE1O3dQiUY)
- [Salman Iqbal - YouTube](https://www.youtube.com/@SoulmanIqbal/videos)
- [ ] [Kubernetes network stack fundamentals: How containers inside a pod communicate | Enable Sysadmin (redhat.com)](https://www.redhat.com/sysadmin/kubernetes-pod-network-communications)
- [ ] [Kubernetes network stack fundamentals: How pods on different nodes communicate | Enable Sysadmin (redhat.com)](https://www.redhat.com/sysadmin/kubernetes-pods-communicate-nodes)
- [ ] [Tracing the path of network traffic in Kubernetes (learnk8s.io)](https://learnk8s.io/kubernetes-network-packets)
- [ ] [A Guide to the Kubernetes Networking Model - Kevin Sookocheff](https://sookocheff.com/post/kubernetes/understanding-kubernetes-networking-model/)