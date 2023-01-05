---
created: 2023-01-02 19:46
updated: 2023-01-02 21:26
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

- ***Server Templating***:
	- Pre installed software and dependencies
	- Virtual machine and docker images
	- *Immutable infrastructure: Once the VM or container is deployed it is designed to remain unchanged*.

- ***Provisioning Tools***:
	- Deploy *Immutable Infrastructure resources*.
	- Servers, Databases, Network Components etc.
	- Multiple Providers

> [!note]- Terraform is vendor agnostic unlike CloudFormation which is only meant for AWS.

### Why Terraform?
- Free and open source tool developed by HashiCorp.
- It *installs as a single binary*.
- We can deploy infrastructure across *multiple platforms including private and public cloud*. 
	- This is **achieved using providers**.
	- We can *also manage network infrastructure* using other providers.
	- Terraform supports 100's of providers because of this Terraform can work with any infrastructure platform.
	- ![[attachments/Pasted image 20230102195237.png]]

- Terraform uses **HashiCorp Configuration Language (HCL)** (`.tf`) which is a simple **declarative** language to define the infrastructure resources *as blocks of code*.
- Terraform works in *3 phases: init, plan and apply*.
	- During the init phase *terraform initialises the project and **identifies the providers** to be used for the current environment*.
	- During the plan phase terraform drafts a plan to get to the target state.
	- During the apply phase terraform makes the necessary changes to the target environment to bring it to the desired state.

- **Every object that terraform manages is called a resource**.
	- It can be a compute instance or a database server or any other component

- Terraform records the state of the infrastructure as seen in the real world. 
	- Based on this it can determine what actions to take for updating resources for a particular platform.
	- This ensures that the entire infrastructure is in the defined state at all times.
	- *Terraform can read attributes of existing infrastructure components*.
	- *Terraform can manage resources created by other IaC tools*.