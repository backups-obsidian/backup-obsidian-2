---
created: 2023-01-05 13:23
updated: 2023-12-01 13:07
---
---
**Links**: [[113 Terraform Index]]

---
## State
- When we do `terraform apply` for the first time resources are created and an additional state file `terraform.tfstate` is created in the configuration directory.

> [!note]- `terraform.tfstate` file is not created unless `terraform apply` command is run atleast once.
> - `plan` refreshes the state but state file is only created by `apply`.
> - If there is no state file then `terraform plan` won't create a state file.


- *State file is a json data structure* that *maps the real world infrastructure to the resource definition in the configuration files*.
	- This allows terraform to create execution plans when a drift is identified between the resource configuration files and the state.
	- ![[attachments/Pasted image 20230105145416.png]]
- **Terraform uses this state file as a single source of truth when using `terraform apply`**.
- To see the human readable output of the state file we use `terraform show`
	- If resources have already been created using `terraform apply` then we can also see the attributes of the resources using this command.
	- For example if an EC2 resource is created using terraform then its public and private IP will be stored in the `terraform.tfstate` which we can view using `terraform show`

> [!important]- **State file contains sensitive information**, within it contains every little detail about our infrastructure.
> Like in the above example it had our public and private IPs.
> ![[attachments/Pasted image 20230106092620.png]]

- We need to *ensure that state file is stored in a secure storage*.
- When working as a team it is considered best practice to store terraform configuration files in version control.
	- But due to the *sensitive nature* of the state file it is *NOT recommended to them in version control*.

> [!caution]+ We should NEVER attempt to manually edit the state files.
> If we want to make changes to the state we should use terraform state commands.

### Purpose of state
- State file can be considered as a blueprint for all the resources that terraform manages in the real world. 
- Each resource that is created and managed by terraform will have a unique id.
- It also tracks the dependency that resources have on each other. 
	- The relationship that one resource is dependent on another can be inferred from the configuration file. Now if relationship is deleted then terraform uses the state file to find the relationship to delete the resources in the right order.
- It uses the state file as single source of truth since in large environments it can take a lot of time to get the current state of all the resources.
- *We can make terraform refer to the state file alone and by pass refreshing the state everytime*.
	- This improves the performance significantly.
	- To do this we can use `--refresh=false` with all the commands that use state like `terraform apply --refresh=false`
	- *`init` doesn't refresh the state but `plan` and `apply` refresh the state*.
- Since there can be multiple developers working with terraform and it acts as a source of truth it is **recommended to save the terraform file in a remote location** so that state can be shared between all the members of the team at the same time.
	- Some examples of remote state stores are: AWS S3, GC Storage, HashiCorp Consul, Terraform Cloud.

## [[Terraform - Remote State#Terraform state manipulation | Terraform State Manipulation]]
