---
created: 2022-04-19 16:22
updated: 2022-05-05 16:10
---
---
**Links**: [[101 AWS SAA Index]]

---
- In EC2 Hibernate RAM is preserved in the EBS volume.
- Some **use cases** of this would be *saving the ram state* (redis) or *services that take a long time to initialise*.
- RAM size cannot be greater than **150 GB**.
- An instance cannot be hibernated for more than **60 days**.
- Hibernate is *not supported on bare metal*.

> [!caution] **Root** EBS volume must be **encrypted** to use hibernate.

> [!caution] **Instance storage** is not supported.

> [!note] Use hibernate if you want to *keep the instance pre-warmed* so it can launch the analysis right away when needed. *Custom AMIs cannot be used* to pre-warm the instances.