---
created: 2023-01-13 15:42
updated: 2023-01-13 15:54
---
---
**Links**: [[113 Terraform Index]]

---
## Imports
- `import` is used to *bring the resources created using other methods (like ansible or directly from the console) under the control of Terraform*.
- We can read data from other resources not managed by terraform using `data` sources.
	- *This only helps us to make use of attributes of the datasource*.
	- The resource itself is not managed by terraform.
- `terraform import <resource_type>.<resource_name> <attribute>` 
	- Here attribute is an uniquely identifiable attribute for that resource.
	- The first time we run this command we will get an error because **`terraform import` DOESN'T update the configuration files**. 
	- It only updates the state file with the details of the infrastructure that is being imported.
	- In order to fix the error we can write an empty resource block.
	- ![[attachments/Pasted image 20230113154727.png]]
	- Now once the import command works, we fill in the details in the configuration file which we left empty.
	- `terraform plan` will now refresh the state and resource is now under the control of terraform.
- 