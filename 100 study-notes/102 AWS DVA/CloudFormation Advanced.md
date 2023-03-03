---
created: 2022-05-18 20:06
updated: 2023-03-03 10:07
---
---
**Links**: [[102 AWS DVA Index]]

---
## ChangeSets
- When you **update** a stack, you need *to know what changes* before it happens for greater confidence
- ChangeSets **won't say if the update will be successful**

![[attachments/Pasted image 20220518200412.png]]

## Nested Stacks
- Nested stacks are stacks as part of other stacks
- They allow you to *isolate repeated patterns* / common components in separate stacks and call them from other stacks
- Example:
	- Load Balancer configuration that is *re-used*
	- Security Group that is *re-used*
- Nested stacks are **considered best practice**
- To *update a nested stack*, **always update the parent (root stack)**.

> [!important] Whenever you see the keyword **reused** go for nested stacks.

## StackSets
- Create, update, or delete stacks **across multiple accounts and regions** with a single operation
- Administrator account to create StackSets
- Trusted accounts to create, update, delete stack instances from StackSets
- When you *update a stack set*, **all associated stack instances are updated** throughout all accounts and regions.

> [!important] Go for StackSets if you see *updating multiple stacks* in *multiple regions* or *multiple accounts*.

## Drift
- CloudFormation allows you to create infrastructure
- But it *doesn't protect you against manual configuration changes*
- How do we *know if our resources have drifted*? We can use CloudFormation drift!

## Stack Notifications
- Send *Stack events to SNS Topic* (Email, Lambda, ...)
- Enable SNS Integration using Stack Options.
- We can filter for particular stack notifications using a lambda function.
	- ![[attachments/Pasted image 20230301100437.png]]

## Stack Policies
- During a CloudFormation Stack update, *all update actions are allowed on all resources (default)*.
- A Stack Policy is a **JSON document** that defines the update actions that are allowed on specific resources during Stack updates.
- **Protect resources from unintentional updates**.
- When you set a Stack Policy, *all resources in the Stack are protected by default*.
- Specify an explicit ALLOW for the resources you want to be allowed to be updated
	- ![[attachments/Pasted image 20230301100725.png]]

## Miscellaneous
- Services that use CloudFormation
	- *ElasticBeanStalk* 
	- *SAM*
	- *CodePipeline*

> [!question]- A developer received the following error message during an AWS CloudFormation deployment: DELETE_FAILED (The following resource(s) failed to delete: (sg-11223344).) Which action should the developer take to resolve this error?
> Modify the CloudFormation template to *retain the security group resource*. Then *manually delete the resource after deployment of the CloudFormation template.*
