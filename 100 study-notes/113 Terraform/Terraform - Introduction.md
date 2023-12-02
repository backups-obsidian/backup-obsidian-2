---
created: 2023-01-02 19:46
updated: 2023-12-02 12:31
---
---
**Links**: [[113 Terraform Index]]

| Next: [[Terraform - How Terraform works?]] |
|-|

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

- ***Server Templating***:
	- Preinstall software and dependencies
	- Virtual machine and docker images
	- *Immutable infrastructure: Once the VM or container is deployed it is designed to remain unchanged*.

- ***Provisioning Tools***:
	- Deploy *Immutable Infrastructure resources*.
	- Servers, Databases, Network Components etc.
	- Multiple Providers

> [!note] Terraform is vendor agnostic unlike CloudFormation which is only meant for AWS.

### Why Terraform?
- Free and open source tool developed by HashiCorp.
- It *installs as a single binary*.
- We can deploy infrastructure across *multiple platforms including private and public cloud*. 
	- This is **achieved using providers**.
	- We can *also manage network infrastructure* using other providers.
	- Terraform supports 100's of providers because of this Terraform can work with any infrastructure platform.
		- ![[attachments/Pasted image 20230102195237.png]]

- Terraform uses **HashiCorp Configuration Language (HCL)** (`.tf`) which is a simple **declarative** language to define the infrastructure resources *as blocks of code*.