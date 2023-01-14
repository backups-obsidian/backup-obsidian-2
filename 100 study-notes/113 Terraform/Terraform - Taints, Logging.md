---
created: 2023-01-13 15:20
updated: 2023-01-13 15:26
---
---
**Links**: [[113 Terraform Index]]

---
## Taints
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