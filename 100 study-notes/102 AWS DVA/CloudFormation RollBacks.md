---
created: 2022-05-16 12:39
updated: 2023-03-03 12:37
---
---
**Links**: [[102 AWS DVA Index]]

---
## Rollbacks
### Stack creation fails
- **Default**: `OnFailure=ROLLBACK`
	- *Everything rolls back* (gets deleted). 
	- We can look at the log to know what triggered the rollback.
- **Troubleshoot**: `OnFailure=DO_NOTHING`
	- Option to disable rollback and *manually troubleshoot* 
- **Delete**: `OnFailure=DELETE`
	- Get rid of the stack entirely, do not keep anything
- The **stack can only be deleted**. Look for word *created* in the question.
	- ![[attachments/Pasted image 20220518200001.png]]

- In case `OnFailure=ROLLBACK` and if a CloudFormation stack creation fails, you will get the status `ROLLBACK_COMPLETE`
	- This means:
		- CloudFormation tried to create some resources
		- One resource creation failed
		- CloudFormation rolled back the resources (ROLLBACK, DO_NOTHING)
		- The stack is in failed created ROLLBACK_COMPLETE state

### Stack update fails
- The **stack automatically rolls back** to the previous known working state
- Ability to see in the log what happened and error messages
- The **Stack can be updated**.

## Continue rolling back an update
- A stack goes into the `UPDATE_ROLLBACK_FAILED`, state when **CloudFormation CAN'T roll back all changes during an update**.
	- A resource can't return to its original state, causing the rollback to fail
	- Example: roll back to an old database instance that was deleted outside CloudFormation (like using the console).
- *Solutions*:
	- *Fix the errors manually* outside of CloudFormation and *then continue update rollback the stack*.
	- *Skip the resources* that can't rollback successfully (CloudFormation will mark the failed resources as UPDATE COMPLETE)
- **We CAN'T update a stack in this state**.

> [!question]- You have created a CloudFormation stack that has a lot of resources (ASG, ALB, EC2, RDS DB, S3 buckets, ...). One of your teammates doesn't know that the ALB has been created as part of the CloudFormation stack, so he deleted the ALB and created a new ALB. Later on, you attempted to update the stack, the update failed and the stack can't be rolled back with the following error `UPDATE_ROLLBACK_FAILED`. What would you do to resolve this issue?
> You can **fix the errors manually** (re-create the deleted ALB with the same configuration) **or you can skip the ALB** while updating the stack.