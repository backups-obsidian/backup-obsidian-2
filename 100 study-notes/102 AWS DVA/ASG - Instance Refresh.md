---
created: 2023-03-01 08:23
updated: 2023-03-01 08:31
---
---
**Links**: [[102 AWS DVA Index]]

---
## Instance Refresh
- Goal is to **update launch template** and then *re-creating all EC2 instances*.
	- Instead of terminating the instances and waiting for them to launch back we can use the native Instance Refresh of the API.
	- We can use the **`StartInstanceRefresh`** API. 
- We set a *minimum health percentage*. 
- We can *specify a warm up time* (wait until the instance is ready for use).
- Instances are terminated and new instances come up.

![[attachments/Pasted image 20230301083051.png]]