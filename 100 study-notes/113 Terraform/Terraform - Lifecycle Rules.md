---
created: 2023-01-06 09:49
updated: 2023-12-03 18:08
---
---
**Links**: [[113 Terraform Index]]

| Previous: [[Terraform - Import & Datasources]] |
|-|

| Next: [[Terraform - Modules]] |
|-|

---

## Lifecycle rules
- By default when terraform updates the resource it *treats the resource to be immutable* i.e. deletes the resource and then recreates a new one.
	- Like if we change the file permission then the *older file is first deleted and then a new file is created*.
		- ![[attachments/Pasted image 20230106100438.png]]
- This may not be a desirable behaviour in all cases. 
	- We *might want the resource to be created first before deleting a the old one* or we *may not want to delete the resource at all*.
	- The above behaviours can be achieved using life cycle rules.

> [!note] Lifecycle rules are *applied when a resource is updated* and **NOT created**.

- `create_before_destroy`:
	- ![[attachments/Pasted image 20230106100756.png]]
	- **This can lead to weird results**. 
		- For example suppose that you already have a file created with name x. Now if we update something in the file, it will *first try to create file with name x and then delete the same file since the file name doesn't change*.
- `prevent_destroy`:
	- Terraform will *prevent any changes that might result in the resource getting destroyed*.
	- ![[attachments/Pasted image 20230106101040.png]]
	- This can prevent the resources from getting accidentally deleted like a database resource.
	- The resources can still be destroyed using the `terraform destroy` command.
	- This rule only applies to `terraform apply`

- `ignore_changes`:
	- This lifecycle rule will prevent a resource from being updated based on a list of attributes that we define within the lifecycle block.
	- In this example once the EC2 instance is created terraform will not force a change of its tags. This means if we change the tag using some tool or manually, doing a `terraform apply` won't result in any changes since we have ignored the tags. 
		- Without the `ignore_changes` flag terraform would have changed the tag back to "ProjectA-Webserver"
	- *Changes made to tags outside of terraform is completely ignored*.
	- ![[attachments/Pasted image 20230106101744.png]]
	- Since it is a list we can easily add more arguments.
	- If we want to ignore all arguments then we can add `ignore_changes = all`

- Summary of lifecycle rules
	- ![[attachments/Pasted image 20230106101901.png]]


