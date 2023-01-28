---
created: 2023-01-27 14:29
updated: 2023-01-27 14:32
---
---
**Links**: [[701 How Linux Works Index]]

---
## Chapter 1
- *Processes* - the running programs that the kernel manages—collectively make up the system’s upper level, called user space.

![[attachments/Pasted image 20230127142846.png]]

- **The kernel runs in kernel mode, and the user processes run in user mode**. 
	- Code running in kernel mode has unrestricted access to the processor and main memory.
- User space refers to the parts of main memory that the user processes can access. 
	- If a process makes a mistake and crashes, the consequences are limited and can be cleaned up by the kernel.
- Kernel does **4 things**:
	- *Manage processes*: context switching
	- *Manage main memory*: RAM
	- *Device drivers*
	- *System calls*: system calls (or syscalls) perform specific tasks that a user process alone cannot do well or at all. For example, the acts of opening, reading, and writing files all involve system calls.
- Users exist primarily to support permissions and boundaries. 
- Every user-space process has a user owner, and processes are said to run as the owner. 
	- A user may terminate or modify the behaviour of its own processes (within certain limits), but it *cannot interfere with other users’ processes*. 
- In addition, users may own files and choose whether to share them with other users.
- **The root user is an exception to the preceding rules because root may terminate and alter another user’s processes and access any file on the local system**.

> [!caution]+ Operating as root can be dangerous. It can be *difficult to identify and correct mistakes* because the system will let you do anything, even if it is harmful to the system
> **As powerful as the root user is, it still runs in the operating system’s user mode, NOT kernel mode**.

## Summary
- User processes make up the environment that we directly interact with.
- The kernel manages processes and hardware.
- **Both the kernel and processes reside in memory (RAM)**.
