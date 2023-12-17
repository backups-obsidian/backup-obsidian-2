---
created: 2023-01-05 13:23
updated: 2023-12-12 20:40
---
---
**Links**: [[113 Terraform Index]]

| Previous: [[Terraform - Variables]] |
|-|

| Next: [[Terraform - Remote State]] |
|-|

---
## Backends
- Backend is a place where Terraform will store the state file.
- There are 2 different backends:
	- **Local backend**: the state file lives on current system
	- **Remote backend**: the state file is stored somewhere remote.
		- Remote backend is used for remote state.
 
- *Terraform will use a local backend if no backend configuration is specified*.

```hcl title="specifying the location of state file in a local backend" fold
terraform {
	backend "local" {
		path = "relative/path/to/terraform.tfstate"
	}
}
```


## State
- When we do `terraform apply` for the first time resources are created and an additional state file `terraform.tfstate` is created in the configuration directory.
	- **Terraform stores information about our infrastructure in this state file**.

> [!note]- `terraform.tfstate` file is not created unless `terraform apply` command is run atleast once.
> - `plan` refreshes the state but state file is only created by `apply`.
> - If there is no state file then `terraform plan` won't create a state file.


- *State file is a json data structure* that *maps the real world infrastructure to the resource definition in the configuration files*.
	- This allows terraform to create execution plans when a drift is identified between the resource configuration files and the state.
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

> [!caution]+ We should *NEVER attempt to manually edit the state files*.
> If we want to make changes to the state we should use terraform state commands.

- Terraform stores the information about the state file version and the terraform version in the state file.
- **The `resources` section of the state file contains the schema for any resources we create in Terraform**.
	- `mode`: It refers to the type of resource Terraform creates — either a resource *(`managed`) or a data source (`data`)*.
	- `type`: It refers to the resource type (for example local_file or aws_instance).
	- `instances` section in this resource contains the `attributes` of the resource.

```hcl file:"sample terraform.tfstate state file" fold
{
  "version": 4,
  "terraform_version": "1.6.5",
  "serial": 45,
  "lineage": "c61cb97d-19ee-17b0-f848-4b8234ee732b",
  "outputs": {
    "filename": {
      "value": "0777",
      "type": "string"
    }
  },
  "resources": [
    {
      "mode": "managed",
      "type": "local_file",
      "name": "test",
      "provider": "provider[\"registry.terraform.io/hashicorp/local\"]",
      "instances": [
        {
          "schema_version": 0,
          "attributes": {
            "content": "Cotent of the file",
            "content_base64": null,
            "content_base64sha256": "mDqDKG9ye7AIjRKFXD+QMzmOXevjQmyMSDminZrdgPc=",
            "content_base64sha512": "XIfzZ8lQFM5SrJ5lfGj+lgo6EZGxfhlyWa6jMY7Ppcxe1GkYt6Km6c8y4q0BEAkZPyKjoa+lwHYUCYjQcA0FzQ==",
            "content_md5": "5ecf3e79476129db4dbe3f41cb3a7188",
            "content_sha1": "a1d5596411393897b86d23c08ac84a23e893e0d5",
            "content_sha256": "983a83286f727bb0088d12855c3f9033398e5debe3426c8c4839a29d9add80f7",
            "content_sha512": "5c87f367c95014ce52ac9e657c68fe960a3a1191b17e197259aea3318ecfa5cc5ed46918b7a2a6e9cf32e2ad011009193f22a3a1afa5c076140988d0700d05cd",
            "directory_permission": "0777",
            "file_permission": "0777",
            "filename": "./testing",
            "id": "a1d5596411393897b86d23c08ac84a23e893e0d5",
            "sensitive_content": null,
            "source": null
          },
          "sensitive_attributes": []
        }
      ]
    }
  ],
  "check_results": null
}
```

### Purpose of state
- State file can be considered as a blueprint for all the resources that terraform manages in the real world. 

> [!note] Terraform *state file* tells Terraform **what we have** and Terraform *configuration files* tells Terraform **what we want**.

- Each resource that is created and managed by terraform will have a unique id.
- It also tracks the dependency that resources have on each other. 
	- The relationship that one resource is dependent on another can be inferred from the configuration file. Now if relationship is deleted then terraform uses the state file to find the relationship to delete the resources in the right order.
- **Terraform `plan` and `apply` commands always refresh the state where as `init` doesn't**.
	- *Refreshing the state means updating the state file to be up to date with the current state of infrastructure*.
	- Refreshing the state is like updating what we have.
- We can *explicitly perform a refresh using the `terraform refresh` command*.


- **When would we want to skip refreshing the state?**
	- In large environments it can take a lot of time to get the current state of all the resources.
	- Bypassing refreshing the state everytime can lead to significant performance improvements.
		- To do this we can use `--refresh=false` with all the commands that use state like `terraform apply --refresh=false`
- **Why is refreshing the state essential?**
	- Terraform relies on the contents of the state file to generate an execution plan to make changes to our resources. To ensure the accuracy of the proposed changes, out state file must be up to date.
	- Imagine a scenario where an EC2 instance is managed by Terraform and now it has been manually deleted by someone. Now if we do a `terraform apply --refresh=false` then Terraform WON'T know that the resource has been deleted since its state file hasn't been updated with the latest infrastructure changes and it won't do anything.

> [!question]- Imagine that we are managing a file using terraform and we change the contents of the file manually. What will happen if we refresh (`terraform refresh`) the state now?
> - Since we have manually changed the contents of the file, performing a refresh would modify the state file such that resource is no longer present in the state file.
> - This is because Terraform will try to find the file with the content present in the state file and once it can no longer find it, it will think that it has been manually deleted.

- Since there can be multiple developers working with terraform and it acts as a source of truth it is **recommended to save the terraform file in a remote location** so that state can be shared between all the members of the team at the same time.
	- Some examples of remote state stores are: AWS S3, GC Storage, HashiCorp Consul, Terraform Cloud.


### Using `-refresh-only` instead of `terraform refresh`
- `terraform refresh` command is deprecated.
- In previous versions of Terraform, the only way to refresh our state file was by using the `terraform refresh` subcommand. 
	- However, this was less safe than the `-refresh-only` plan and apply mode since **it would automatically overwrite our state file without giving us the option to review the modifications first**. 
	- The `-refresh-only` flag allows us to review any updates to our state file. 
- The `-refresh-only` mode for `terraform plan` and `terraform apply` operations makes it safer to check Terraform state against real infrastructure by letting us review proposed changes to the state file. 
	- It lets us avoid mistakenly removing an existing resource from state and gives us a chance to correct your configuration.

### Explaining the difference between commands with an example
- Imagine a scenario where we have created and EC2 instance using Terraform. Now we go and make some manual changes to the security group of the EC2 instance.
- `terraform refresh`: It will refresh our Terraform state file to reflect the manual changes to the security group.
- `terraform apply`: It will destroy the instance and create a new instance to match the security group in the terraform configuration.
- `terraform plan -refresh-only`: It will show the plan on what will be changed in the Terraform state file. In this case it will show that the security group will be updated in the state file.
- `terraform apply -refresh-only`: It will refresh our Terraform state file to reflect the manual changes to the security group. 
	- It is different from `terraform refresh` because it will show us what will be changed and we have the option of going forward with it by entering yes to the prompt. 
	- In case of `terraform refresh` it would have directly applied the changes to the state file.

> [!question]+ How to correct the drift?
> - If the manual changes are required then the best way to correct the drift would be to update the state first using `terraform apply -refresh-only` and then modify the security group in the Terraform configuration file to value set manually so that when we do a `terraform apply` it doesn't remove the instance on which we made the manual changes.
> - If the manual changes are not required then `terraform apply`.