---
created: 2022-09-15 21:09
updated: 2022-10-10 14:01
---
---
**Links**: [[111 KodeCloud Index]]

---
- Currently CRD and the custom controllers are separate entities.
- These 2 entities can be packaged together and deployed as a single entity using the operator framework.
	- ![[attachments/Pasted image 20221010135107.png]]
- But the operator framework can do much more than deploying these together.
- Etcd operator
	- ![[attachments/Pasted image 20221010135849.png]]
- A k8s operator does what a human operator do to the cluster like taking backups, restoring backups.
- All operators are available at operator hub. Like prometheus, grafana etc