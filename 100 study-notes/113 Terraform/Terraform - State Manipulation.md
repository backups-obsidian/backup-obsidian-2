---
created: 2023-12-02 19:44
updated: 2023-12-07 19:01
---
---
**Links**: [[113 Terraform Index]]

| Previous: [[Terraform - Remote State]] |
|-|

| Next: [[Terraform - Import & Datasources]] |
|-|

---
## Terraform state manipulation
- Syntax of terraform state command: `terraform state <sub command> [options] [args]`
- `terraform state list`: This will **list all the resources** recorded within the terraform state file.
	- ![[attachments/Pasted image 20230111110330.png]]
- To get **more information about the resources** we can use the `terraform state show <resrource_type>.<resource_name>`
	- This will *show all the attributes of the respective resource*.
	- ![[attachments/Pasted image 20230111110524.png]]
- `terraform state mv SOURCE DESTINATION`: This is used to move items in a terraform state file.
	- It can be used to *move resources within the same state file* (essentially **renaming the resource**).
	- It can be used to *move resources from one state file to another state file* maintained by another configuration directory.
	- Example renaming the dynamodb table
		- ![[attachments/Pasted image 20230111111512.png]]
		- **After this we must manually rename the resource in the configuration file**.
- **Viewing remote state**: 
	- `terraform state pull` and then pipe the output to json parsing tools like `jq`.
 
 > [!question]- A requirement has come up which requires you to inspect the state file of terraform configuration. *Your terraform script is already configured to work with the remote backend*. Which of the following commands would you use to view a specific field in the state file.
> `terraform state pull`

- Removing items from terraform state `terraform state rm <resource-address>`.
	- This is used when we no longer wish to manage one or more resources using terraform  configuration and state.
		- ![[attachments/Pasted image 20230111111455.png]]
	- After removing the resource from the state file we need to manually *remove it from the configuration file*.
	- **Resources removed from the state file are NOT actually destroyed**. It is only removed from the terraform management.
- We can disable state locking for most commands using the `-lock` flag.

> [!question]- If a resource is deleted from the state file using `rm`, and a `refresh` command is executed, will the state file be updated to include that resource, considering its existence in the real world?
> - No. Because the `refresh` command only refreshes the state for the resources that are present in the `resources` section of the state file and doing a `rm` removes the resource from the `resources` section of the state file.
