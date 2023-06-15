---
created: 2022-09-13 16:17
updated: 2023-06-15 08:20
---
---
**Links**: 
- [[../100 home| 100 Home]]
- [[../104 Linux/104 Linux Index | Linux Index]]

---
## DevOps Prerequisites 
[[KodeCloud - Package Management using yum]]
[[KodeCloud - Linux Services]]
[[KodeCloud - Networking Basics]]
[[KodeCloud - Applications]]
[[KodeCloud - WebServers]]
[[KodeCloud - Databases]]
[[KodeCloud - SSL-TLS]]
[[../104 Linux/Linux - Openssl| Using openssl]]

## Kubernetes

### Introduction & Basics
[[../110 Kubernetes/Kubernetes - Introduction| Kubernetes - Introduction & Basic Architecture]]

[[KodeCloud - Kubernetes - Pods]]
[[KodeCloud - Kubernetes - ReplicaSets]]
[[KodeCloud - Kubernetes - Deployments]]
[[KodeCloud - Kubernetes - Networking]]
[[KodeCloud - Kubernetes - Services]]

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

- Helm Charts
	- [[KodeCloud - CKAD - Helm Charts]]
	- [[KodeCloud - CKAD - Writing helm charts]]

- [[KodeCloud - CKAD - KubeConfig]]

- Understanding the API & API Server
	- [[KodeCloud - CKAD - API & API Groups]]
	- [[KodeCloud - CKAD - API Versions]]
	- [[Kubernetes - Steps of an API Request in API Server]]

- Authentication & Authorization
	- [[Kubernetes - Authentication]]
	- [[KodeCloud - CKAD - Authorization]]
	- [[KodeCloud - CKAD - RBAC]]
	- [[KodeCloud - CKAD - ClusterRoles]]

- Controllers, Custom Resources & Operators
	- [[Kubernetes - Controllers]]
	- [[KodeCloud - CKAD - Admission Controllers]]
	- [[KodeCloud - CKAD - Custom Resource Definition]]
	- [[KodeCloud - CKAD - Operators]]

- [[KodeCloud - CKAD - Deployment Strategies]]
- [[KodeCloud - CKAD - Port Forwarding vs Proxy]]
- [[KodeCloud - CKAD - Misc]]

### CKA
- [[KodeCloud - CKA - Cluster Architecture]]
- [[KodeCloud - CKA - Daemon Sets]]
- [[KodeCloud - CKA - Static Pods]]
- Cluster Maintenance:
	- [[KodeCloud - CKA - OS Upgrades]]
	- [[KodeCloud - CKA - Cluster Upgrade]]
	- [[KodeCloud - CKA - Backup & Restore]]
- [[KodeCloud - CKA - TLS Kubernetes]]
- [[KodeCloud - CKA - Container Storage Interface (CSI)]]
- Networking:
	- Prerequisites:
		- [[KodeCloud - Networking Basics]]
		- [[../104 Linux/Linux - Networking - Interfaces|Linux - Networking - Interfaces]]
		- [[../104 Linux/Linux - Networking - Iptables|Linux - Networking - Iptables]]
		- [[../104 Linux/Linux - Networking - Network Namespaces|Linux - Networking - Network Namespaces]]
	- [[KodeCloud - CKA - CNI]]
	- [[KodeCloud - CKA - Cluster Networking]]
	- [[KodeCloud - CKA - Pod & Service Networking]]
	- [[KodeCloud - CKA - Pause Container]]
	- [[KodeCloud - CKA - DNS]]
- [[KodeCloud - CKA - Setting up k8s]]
- [[KodeCloud - CKA - Finalizers & Owner References]]
- [[KodeCloud - CKA - Miscellaneous]]

### Self
- [[Kubernetes - Logging]]
- [[Kubernetes - Dumping Ground]]

## Tools
- [[KodeCloud - Kustomize]]
- [[KodeCloud - ArgoCD]]
- [[KodeCloud - Istio]]

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
- [Demystifying kube-proxy | Mayank Shah](https://mayankshah.dev/blog/demystifying-kube-proxy/#extra-reading)
- [How to Troubleshoot Kubernetes Clusters | Kubernetes Tutorial | K21Academy - YouTube](https://www.youtube.com/watch?v=EirIuYq1Yes)
- https://www.youtube.com/watch?v=B6FsWNUnRo0
- [Understanding Kubernetes Networking. Part 2: POD Network, CNI, and Flannel CNI Plug-in. - YouTube](https://www.youtube.com/watch?v=U35C0EPSwoY)
- [Container Networking From Scratch - Kristen Jacobs, Oracle - YouTube](https://www.youtube.com/watch?v=6v_BDHIgOY8)
- [Kubernetes Networking Series - YouTube](https://www.youtube.com/playlist?list=PLSAko72nKb8QWsfPpBlsw-kOdMBD7sra-)
- Must have k8s operators?
- Check datree, OPA and Kyverno.

## References
- Installing the metrics server: [How to Install Kubernetes (K8s) Metrics Server Step by Step (linuxtechi.com)](https://www.linuxtechi.com/how-to-install-kubernetes-metrics-server/)
	- We CANNOT see historical performance data in the metrics server.
- [Docker vs Containerd: Understanding the Differences and Choosing the Right Containerization Tool - YouTube](https://www.youtube.com/watch?v=21onkZfL2yM)
- [Storing Secrets in GIT | GitOps | Kubernetes - YouTube](https://www.youtube.com/watch?v=u0qtgUMLua0) - Sealed Secrets

## Considerations:
- Have a cluster which is being backed up regularly for deploying argo cd.
- If we are planning to use sealed secrets then we need to use the same private and public key for all the clusters.
	- This needs to be created before installing the sealed secrets controller.
