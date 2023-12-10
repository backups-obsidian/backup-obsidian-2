---
created: 2023-01-02 19:46
updated: 2023-12-10 12:03
---
---
**Links**: [[113 Terraform Index]]

| Next: [[Terraform - How Terraform works?]] |
|-|

---
## IaC tools classification
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

## Why Terraform?
- Free and open source tool developed by HashiCorp.
- It *installs as a single binary*.
- We can deploy infrastructure across *multiple platforms including private and public cloud*. 
	- This is **achieved using providers**.
	- We can *also manage network infrastructure* using other providers.
	- Terraform supports 100's of providers because of this Terraform can work with any infrastructure platform.
		- ![[attachments/Pasted image 20230102195237.png]]

- Terraform uses **HashiCorp Configuration Language (HCL)** (`.tf`) which is a simple **declarative** language to define the infrastructure resources *as blocks of code*.

> [!important] Terraform is an **immutable**, **declarative**, Infrastructure as Code provisioning language based on Hashicorp Configuration Language, or optionally JSON.

## Mutable and Immutable Infrastructure
- We can upgrade a software on different machines using normal updates but there is a chance that some of the machines might not update because of some missing dependencies.
	- Over time all the servers will have different version of the software making it harder to debug.
	- This is known as *configuration drift*.
	- Example of *mutable infrastructure*.
- Instead when we want to update a software we install the new version on the fresh machine and then bring down the machines using the old version.
	- This is an example of *immutable infrastructure*.
	- **We cannot carry out in-place updates with immutable infrastructure**.
- Immutability makes it easier to version the infra and roll back.