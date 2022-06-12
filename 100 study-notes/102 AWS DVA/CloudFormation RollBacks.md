---
created: 2022-05-16 12:39
updated: 2022-05-18 20:17
---
---
**Links**: [[102 AWS DVA Index]]

---
- Stack **Creation Fails**:
	- *Default*: *everything rolls back* (gets deleted). We can look at the logs to know what triggered the rollback.
	- Option to disable rollback and troubleshoot what happened
	- The **stack can only be deleted**. Look for word *created* in the question.
	- ![[attachments/Pasted image 20220518200001.png]]
- Stack **Update Fails**:
	- The *stack automatically rolls back* to the previous known working state
	- Ability to see in the log what happened and error messages
	- The *Stack can be updated*.
