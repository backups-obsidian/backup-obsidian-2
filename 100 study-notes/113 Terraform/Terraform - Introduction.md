---
---
---
**Links**: [[113 Terraform Index]]

---
## Introduction
- Also known as IaC (Infrastructure as Code)
- *Ansible and Terraform have very different use cases*.

### IaC tools classification
![[attachments/Pasted image 20230102194051.png]]

- ***Configuration management***:
	- **Designed to Install and Manage Software**
	- Maintains Standard Structure which means it is easy to update as compared to shell scripts.
	- Version Control
	- **Idempotent**: We can run the code multiple times and every time we run it will only make changes required to bring the environment in the defined state.

- Server Templating:
	- Pre installed software and dependencies
	- Virtual machine and docker images
	- Immutable infrastructure: Once the VM or container is deployed it is designed to remain unchanged.
- Provisioning Tools