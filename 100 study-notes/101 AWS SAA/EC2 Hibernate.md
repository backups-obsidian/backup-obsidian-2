---
created: 2022-04-19 16:22
updated: 2023-03-02 11:19
---
---
**Links**: [[101 AWS SAA Index]]

---
## EC2 Hibernate
- In EC2 Hibernate *RAM is preserved in the EBS volume*.
- Some **use cases** of this would be 
	- Long running processing
	- *Saving the ram state* (redis) 
	- *Services that take a long time to initialise*
- RAM size cannot be greater than **150 GB**.
- An instance *CANNOT* be hibernated for more than **60 days**.
- Hibernate is *not supported on bare metal*.

> [!caution] **Root** EBS volume must be **encrypted** to use hibernate.

> [!caution] **Instance storage** is not supported.

> [!note] Use hibernate if you want to *keep the instance pre-warmed* so it can launch the analysis right away when needed. *Custom AMIs cannot be used* to pre-warm the instances.