---
created: 2023-05-24 12:24
updated: 2023-05-24 13:34
---
---
**Links**: [[111 KodeCloud Index]]

---
## Container Storage Interface (CSI)
- Initially k8s only supported docker but as other container runtimes started coming in it became essential to support other container runtimes.
	- This is why *[[KodeCloud - CKA - Cluster Architecture#Container Runtime | Container Runtime]] Interface*  was created.
- CRI defines how k8s will communicate with the container runtimes.
	- So *if a new container runtime is created it has to follow CRI to be compatible with k8s*.
- *To extend solution to different networks CNI (Container Networking Interface) was created*.
- Similarly **CSI was created to support multiple storage solutions**.
	- CSI is a *standard for exposing storage systems* in an arbitrary block and file storage to containerized workloads on Container Orchestrations like Kubernetes, Mesos, and Cloud Foundry.
	- CSI defines what APIs k8s will call to create or delete a volume and it is upto the storage solution to implement the logic.
- Diagram:
	- ![[attachments/Pasted image 20230524124123.png]]
