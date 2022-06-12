---
created: 2022-05-16 12:39
updated: 2022-05-18 20:20
---
---
**Links**: [[102 AWS DVA Index]]

---
- CloudFormation is a **declarative** way of outlining your AWS Infrastructure, for any resources (most of them are supported).
- CloudFormation is **free**.
- Cost
	- Each resources within the *stack is tagged* with an identifier so you can *easily see how much a stack costs you*.
	- You can estimate the costs of your resources using the CloudFormation template.
- *Separation of concern*: *create many stacks* for many apps, and many layers.

## How CloudFormation works
- *Templates* have to be **uploaded in S3** and then *referenced* in CloudFormation

> [!note] CloudFormation references a template from Amazon S3, no matter what. *If you upload the template from the AWS console, it gets uploaded to Amazon S3* behind the scenes, and CloudFormation references that template from there.

- *Template* is the **yml file**.
- To update a template, we **can't edit previous ones** we have to *re upload a new version* of the template to AWS.
	- CloudFormation will figure out the changes between the old and the new template and make the required changes to the infrastructure.
- **Stacks** are identified by a **name**.

> [!important] **We can have multiple templates inside stack**. For example templates referencing each other for better separation of concerns.

- **Deleting a stack deletes every single artifact** that was created by CloudFormation.
- If **replacement** is *true* then the resource will be *terminated* and a new one will be created.

> [!caution] *Templates* are just the *instruction*. To delete the resources you delete the stack.

> [!important] Every resource, creation and updation has to happen via CloudFormation.