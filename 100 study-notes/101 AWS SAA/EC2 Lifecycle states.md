---
created: 2022-04-19 16:22
updated: 2022-04-20 11:09
---
---
**Links**: [[101 AWS SAA Index]]

---

![[attachments/Pasted image 20220420110158.png]]

### Pending 
-  `pending` - The instance is **preparing to enter the `running` state**.
-   An instance enters the pending state when
	- **when it launches for the first time** or
    -   When it is **restarted after being in the stopped state**.

### Running
-  `running` - The instance is running and ready for use.

### Stopping/Stopped/Terminated
-   `stopping` - The instance is **preparing to be stopped**. 

> [!tip] Take note that **you will not billed if it is preparing to stop** however, you **will still be billed if it is just preparing to hibernate**.

- `stopped` - The instance is shut down and cannot be used. The instance can be restarted at any time.

-  `shutting-down` - The **instance is preparing to be terminated**.
-  `terminated` - The instance has been **permanently deleted and cannot be restarted**.

> [!note] **Reserved Instances that applied to terminated instances are still billed until the end of their term** according to their payment option.

---
In short you are billed for
- On demand instance in **stopping state** for **hibernation**
- Reserved instance in terminated state.