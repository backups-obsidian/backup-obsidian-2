---
created: 2023-01-13 15:42
updated: 2023-12-17 16:37
---
---
**Links**: [[113 Terraform Index]]

| Previous: [[Terraform - State Manipulation]] |
|-|

| Next: [[Terraform - Lifecycle Rules]] |
|-|

---
## Datasources
- Terraform data sources lets us dynamically **fetch data from APIs** or **other Terraform state backends**. 
	- Examples of data sources include *machine image IDs or availability zones* from a cloud provider or Terraform outputs from other configurations. 
	- *It allows terraform to read resources which are provisioned outside of terraform*.
- We define data resources using the `data` block. It is quite similar to `resource` block.
	- ![[attachments/Pasted image 20230106104830.png]]
	- Reading the data source and using it with other resource.
- We read it using `data.<resource-type>.<name>.<attribute>`
- *`data` block consists of specific arguments for a datasource*.
- **Data source only reads the infrastructure**.
- Difference between a data source and a resource:
	- ![[attachments/Pasted image 20230106105024.png]] 
- **We can use the `terraform_remote_state` data source to use another Terraform workspace's output data**.

```hcl title:"Using another Terraform's output data" fold
data "terraform_remote_state" "vpc" {
  backend = "local"

  config = {
    path = "../learn-terraform-data-sources-vpc/terraform.tfstate"
  }
}
```

- In the above example remote state block uses the local backend to load state data from the path in the config section.
	- The behaviour of *local-only data sources* is the same as all other data sources, but their *result data exists only temporarily during a Terraform operation, and is re-calculated each time a new plan is created*.
- *Terraform remote state also supports a remote backend type for use with remote systems, such as Terraform Cloud or Consul*.
- `data` block has the same dependency resolution as normal resources.
- *`lifecycle` meta-argument is not supported by the `data` block*.

## Imports
- Terraform `import` is used for bringing the existing infrastructure under its management.
	- The `terraform import` command can *only import one resource at a time*.
- We can read data from other resources not managed by terraform using `data` sources.
	- *This only helps us to make use of attributes of the datasource*.
	- The resource itself is not managed by terraform.
- **There are 2 ways of importing existing infrastructure**:
	- *Configuration driven approach where we define an `import` block*.
	- Using the `import` CLI command.

- Using configuration to import resources involves the following steps:
	- Identify the existing infrastructure you will import.
	- Define an `import` block for the resources.
	- Run `terraform plan` to review the import plan and optionally generate configuration for the resources.
	- Prune generated configuration to only the required arguments.
	- Apply the configuration to bring the resource into your Terraform state file.

- *Limitations* of the configuration driven import:
	- Importing involves manual steps which can be error prone.
	- Terraform import does not detect which default attributes you can skip setting.
	- **Not all providers and resources support Terraform import**.
### Example: Importing an existing running docker container using configuration driven approach
- Configuration-driven import relies on the import block, which has two required arguments:
	- **`id`**: is the **provider-specific identifier for the infrastructure** you want to import.
	- **`to`**: is the **identifier Terraform will give the resource in state, consisting of the resource type and name**.

```hcl title:"docker.tf file for importing" fold
import {
  id = "FULL_CONTAINER_ID"
  to = docker_container.web
}
```

> [!note]- The identifier we use for the `id` argument in the `import` block is resource-specific.
> We can find the required ID in the provider documentation for the resource we wish to import.

- **When importing a resource, we must both bring the resource into our state file, and define a corresponding `resource` block for it in our configuration**. 
	- Although we can manually define the resource ourselves, configuration-driven import can generate configuration for us to use as a starting point.
- The generated configuration contains all possible arguments for the imported resources, including those set to default values and those without values. 
	- It is recommend that we prune the generated configuration to only required arguments and arguments whose values differ from defaults, to reduce the size of our configuration.
- Using `terraform plan -generate-config-out=generated.tf` with the `-generate-config-out` will generate configuration for the container we will import and output it to the file.

### Example: Importing using import cli command
- `terraform import <resource_type>.<resource_name> <attribute>` 
	- Here attribute is an uniquely identifiable attribute for that resource.
	- The first time we run this command we will get an error because **`terraform import` DOESN'T update the configuration files**. 
	- It only updates the state file with the details of the infrastructure that is being imported.
	- In order to fix the error we can write an empty resource block.
	- ![[attachments/Pasted image 20230113154727.png]]
	- Now once the import command works, we fill in the details in the configuration file which we left empty.
	- `terraform plan` will now refresh the state and resource is now under the control of terraform.

- `import` command examples:
	- This example will import an AWS instance into the `aws_instance` resource named `foo`: `terraform import aws_instance.foo i-abcd1234`.
	- **This example below will import an AWS instance into the `aws_instance` resource named `bar` into a module named `foo`**: `terraform import module.foo.aws_instance.bar i-abcd1234`
	- The example below will import an AWS instance into the first instance of the `aws_instance` resource named `baz` configured with `count`: `terraform import 'aws_instance.baz[0]' i-abcd1234`.
	- The example below will import an AWS instance into the `"example"` instance of the `aws_instance` resource named `baz` configured with `for_each`: `terraform import 'aws_instance.baz["example"]' i-abcd1234`