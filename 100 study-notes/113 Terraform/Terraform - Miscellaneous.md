---
created: 2023-12-02 19:31
updated: 2023-12-03 10:51
---
---
**Links**: [[113 Terraform Index]]

---
## Taints - DEPRECATED (use `-replace` instead)
- There can be cases where resource creation fails when we run `terraform apply`.
	- When this happens *terraform marks the resources as tainted*.
- **If the resource is tainted and we run `terraform apply` it will be recreated**.
	- Example: If we are using provisioners while creating an EC2 instance and for some reason the provisioner fails then terraform will mark the resource as tainted even though it may have been created by terraform. Running the `terraform apply` command will try to recreate the resource.
	- *Use case*: Suppose we created and EC2 instance running Nginx using terraform. We manually changed the version of Nginx on that instance. Running `terraform apply` won't recreate the instance since it already exists. But *if we want terraform to recreate it then we can taint the resource*.
- Tainting: `terraform taint aws_instance.web_server`
- Un-tainting: `terraform untaint aws_instance.web_server`

## Logging
- Using logs in terraform
	- `export TF_LOG=TRACE`
	- ![[attachments/Pasted image 20230113152806.png]]
- **`trace` is the most verbose**.
	- This will display a lot of logs.
- Storing the logs persistently:
	- `export TF_LOG_PATH=/tmp/terraform.log`
- To disable logging we unset the environment variables
	- `unset TF_LOG`, `unset TF_LOG_PATH`

## Version Constraints
- Using a specific version of a provider
	- ![[attachments/Pasted image 20230107105041.png]]
	- It is inside the `terraform` block
- Some version constraints:
	- `version = "!= 2.0.0"`: Don't use version `2.0.0`
	- `version = "< 1.4.0"`
	- `version = "> 1.4.0"`
	- `version = "> 1.2.0, < 2.0.0, != 1.4.0"`: Mixing comparison operators
	- `version = "~> 1.2"`: Terraform can download the version 1.2 or any other incremental version. The major version cannot be changes this means it can go max upto 1.9.
		- If we use `version = "~> 1.2.0"` then it can only go upto `1.2.9`