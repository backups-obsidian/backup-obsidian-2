---
created: 2022-05-18 20:06
updated: 2022-05-18 20:14
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
- To update a nested stack, always update the parent (root stack)

> [!important] Whenever you see the keyword **reused** go for nested stacks.

### Difference between cross and nested stacks
- Cross Stacks
	- Helpful when stacks have different lifecycles
	- Use Outputs Export and `Fn::ImportValue`
	- When you need to **pass export values** to many stacks (VPC Id, etc...)
	- ![[attachments/Pasted image 20220518200814.png]]
- Nested Stacks
	- Helpful when components must be **re-used**
	- Ex: re-use how to properly configure an Application Load Balancer
	- The nested stack only is important to the higher level stack (it's not shared)
	- ![[attachments/Pasted image 20220518200821.png]]

> [!important] Go for nested stacks if you see the keyword *pass values*, *import/export* 

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