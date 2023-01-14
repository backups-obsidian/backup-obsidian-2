---
created: 2023-01-13 14:32
updated: 2023-01-13 14:50
---
---
**Links**: [[300 home]]

---
## Packer
> [!note]- With Packer we can create **cloud agnostic AMIs**. 
> The AMIs that we create will work for AWS, GCP, Azure and even for *proxmox vms*.

- We can have Packer in our CI/CD pipeline to generate machine images.
- Packer vs Ansible:
	- *You can bring a base image to a desired state using Ansible but if it takes 2H to do it, it is better to have AMIs*.
	- Also if Ansible is taking 2H then this means there is a lot of downloading which is a waste of resources.

## References
- [Hashicorp Packer Crash Course with Digital Ocean - YouTube](https://www.youtube.com/watch?v=T2Gx2fGT9kk)