---
created: 2023-12-02 19:31
updated: 2023-12-10 11:54
---
---
**Links**: [[113 Terraform Index]]

| Previous: [[Terraform - Count & for each]] |
|-|

| Next: [[Terraform - Terraform Cloud]] |
|-|

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
- Different levels: `ERROR -> WARN -> INFO -> DEBUG -> TRACE`
	- **`trace` is the most verbose**.
- We can set `TF_LOG` to `JSON` apart from the different levels to get `TRACE` logs in JSON.
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
	- `version = "~> 1.2"`: Terraform can download the version 1.2 or any other incremental version **without changing the minor and major version**.
		- *Only the patch version can be changed*.
		- If we use `version = "~> 1.2.0"` then it can only go up to `1.2.9`

> [!note]- Our of `>=`, `!=`, `<=`, `=` and `==`, **`==` is NOT a valid version constraint**.

## Moving Resources
- **When we move existing resources from a parent to a child module, our Terraform resource IDs will change**. 
	- Because of this, we *must let Terraform know that we intend to move resources rather than replace them*, or Terraform will destroy and recreate your resources with the new ID.
- The **`moved` configuration block lets us track our resource moves in the configuration itself**. 
	- With the `moved` configuration block, we can plan, preview, and validate resource moves, enabling us to safely refactor our configuration.

```hcl title:"moved block example" fold
moved {
  from = module.security_group.aws_security_group.sg_8080
  to   = module.web_security_group.aws_security_group.this[0]
}
```

- We can also use the `moved` configuration block to **rename existing resources**.

> [!note] It is strongly recommend that we retain all `moved` blocks in your configuration as a record of our changes. Removing a `moved` block plans to delete that existing resource instead of moving it.

```hcl title:"renaming existing resources" fold
# the resource was created with the name of testing. Now I changed the name of the resource in the configuration to testing 2.

resource "local_file" "testing2" {
  filename = "testing"
  content  = "Hello, World!"
}

moved {
  from = local_file.testing
  to   = local_file.testing2
}

```