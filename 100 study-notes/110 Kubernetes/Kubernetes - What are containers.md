---
created: 2022-09-05 21:45
updated: 2022-09-05 21:56
---
---
**Links**: [[110 Kubernetes Index]]

---
## What are containers
![[attachments/Pasted image 20220905214256.png]]

- The container actually hosts cgroups, namespaces & union capable file system.
	- Because of these 3 a container looks and feels like an operating system.

> [!note]- The **cgroups** and **namespaces** capabilities of the Linux kernel.

- *cgroup*: Control Groups provide a mechanism for aggregating/partitioning sets of tasks, and all their future children, into hierarchical groups with specialised behaviour.
- *namespace*: wraps a global system resource in an abstraction that makes it appear to the processes within the namespace that they have their own isolated instance of the global resource.

> [!important]- *cgroups* = limits *how **much** you can use*; *namespaces* = limits **_what_ you can see** (and therefore use)
> - cgroups provide resource metering and limiting like memory, CPU, block I/O, network, Device node (/dev/) access control
> - namespaces provides processes with their own view of the filesystem. We have multiple namespaces for pid, net, mnt, uts etc.

## References
- [Containers: cgroups, Linux kernel namespaces, ufs, Docker, and intro to Kubernetes pods - YouTube](https://www.youtube.com/watch?v=el7768BNUPw&list=PLqnWYrfCqvm4IG1SAj_6lqpwkKdHrRzRF&index=1)