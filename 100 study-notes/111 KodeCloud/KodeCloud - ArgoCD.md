---
created: 2023-06-06 10:49
updated: 2023-06-06 12:38
---
---
**Links**: [[111 KodeCloud Index]]

---
## Introduction - GitOps
- GitOps process diagram:
	- ![[attachments/Pasted image 20230606122002.png]]
- **In GitOps pipeline we have 2 repositories**.
	- One for the application code and other for the infrastructure.
- **Difference between GitOps and DevOps**:
	- ![[attachments/Pasted image 20230606122710.png]]

> [!note] In *GitOps changes were pulled by the operator* and in *DevOps changes were pushed to the clutser*.

- **Difference between push and pull based approach**:
	- ![[attachments/Pasted image 20230606123206.png]]

> [!note] The main drawback of a push based approach is that cluster config is embedded in the CI system.

- A **single GitOps operator can deploy applications to multiple clusters** without the need for it to be installed or setup on all the clusters.
	- ![[attachments/Pasted image 20230606123519.png]]
- Some Benefits of GitOps:
	- It is vendor neutral.
	- Git is the source of truth.
	- Helps in eliminating configuration drift.
	- We have revisions of the infrastructure with git history.

## Introduction - ArgoCD
- 