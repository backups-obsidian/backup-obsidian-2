---
created: 2022-05-16 12:39
updated: 2023-01-30 15:37
---
---
**Links**: [[102 AWS DVA Index]]

---
## Resources
- Resources are the **core** of your *CloudFormation template* (**MANDATORY**)
- They represent the different *AWS Components* that will be created and configured
- Resources are declared and *can reference each other* 
- AWS figures out creation, updates and deletes of resources for us
- Identifier: `AWS::aws-product-name::data-type-name`
	- Example: `AWS::EC2::Instance`

## Parameters
- Parameters are a way to **provide inputs** to your AWS CloudFormation template
- They're important to know about if:
	- You want to **reuse your templates** across the company
	- *Some inputs can not be determined ahead of time*
- Parameters are extremely powerful, controlled, and can prevent errors from happening in your templates thanks to **types**.
	- ![[attachments/Pasted image 20220518151809.png]]

### Referencing a parameter
- The `Fn:Ref` function can be leveraged to *reference parameters*
- Parameters can be used anywhere in a template.
- The **shorthand** for this in YAML is `!Ref`
	- ![[attachments/Pasted image 20220518152024.png]]
- The function can also reference other elements within the template

> [!caution] `!Ref` can be used to refer **parameters** as well as **resources**.

### Pseudo Parameters
- AWS offers us pseudo parameters in any CloudFormation template.
- These can be *used at any time and are enabled by default*.
	- Example: `AWS::AccountId`

## Mappings
- Mappings are *fixed variables* within your CloudFormation Template.
- They're very handy to *differentiate between different environments* (dev vs prod), regions (AWS regions), AMI types, etc
	- ![[attachments/Pasted image 20220518152430.png]]
- All the values are **hardcoded** within the template

> [!note] Mappings are great *when you know in advance all the values* that can be taken. If you want something more *user specific* then use **parameters**.

> [!important]- Accessing Map Values
> - We use `Fn::FindInMap` to return a named value from a specific key
> - `!FindInMap [MapName, TopLevelKey, SecondLevelKey]`
> ---
> ![[attachments/Pasted image 20220518152739.png]]
> Here `AWS::Region` is a **pseudo parameter**.

## Outputs 
- The Outputs section declares **optional** outputs values that we **can import into other stacks** (if you *export them first*)!
- You can also view the outputs in the AWS Console or using the AWS CLI
- They're very useful for example if you define a network CloudFormation, and output the variables such as VPC ID and your Subnet IDs
- It's the best way to perform some *collaboration cross stack*, as you let expert handle their own part of the stack
- Example: *Exporting* a value
	- ![[attachments/Pasted image 20220518153418.png]]
- To import the value we use `Fn::ImportValue function` or `!ImportValue`
- Exporting and importing process: 
	- Create a cross-stack reference and use the *Export output field* to export the value. Then use `Fn:ImportValue` intrinsic function to import the value.

> [!caution]- You *can't delete* a CloudFormation Stack if its outputs are being referenced by another CloudFormation stack
> To delete it first, you need to *delete the other CloudFormation stacks that reference the exported outputs*, then delete it

> [!note] Whenever question mentions about *linking CloudFormation templates* go with outputs.

> [!caution] Exported output names must be *unique within your region*

## Conditions
- Conditions are used to _control the creation of **resources or outputs**_ based on a condition.
	- You *cannot* use conditions with *parameters*.
- Conditions can be whatever you want them to be, but common ones are:
	- *Environment* (dev / test / prod)
	- AWS *Region*
	- Any *parameter value*
- Each condition can **reference another condition, parameter value or mapping**.
- We define a condition and then reference it
	- ![[attachments/Pasted image 20220518154208.png]]
	- ![[attachments/Pasted image 20220518154319.png]]