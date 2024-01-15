---
created: 2023-01-09 22:37
updated: 2023-12-19 19:37
---
---
**Links**: [[113 Terraform Index]]

| Previous: [[Terraform - Lifecycle Rules]] |
|-|

| Next: [[Terraform - Managing Multiple Environments]] |
|-|

---
## Modules
- **Why use modules**?
	- Modules make it *easier to navigate, understand, and update our configuration by keeping related parts of our configuration together*. 
	- Even moderately complex infrastructure can require hundreds or thousands of lines of configuration to implement. By using modules, we can organize our configuration into logical components.
	- Modules also help to provide consistency in our configurations.
	- Modules can be easily shared.
- **What is a Terraform module**?
	- A Terraform module is a *set of Terraform configuration files in a single directory*.
	- *Even a simple configuration consisting of a single directory with one or more `.tf` files is a module*. 
	- **When you run Terraform commands directly from such a directory, it is considered the root module**.
		- So in this sense, every Terraform configuration is part of a module.
		- ![[attachments/Pasted image 20231204183836.png]]
- **Terraform commands will only directly use the configuration files in one directory, which is usually the current working directory**.
	- However, the configuration can use module blocks to call modules in other directories.
	- When Terraform encounters a module block, *it loads and processes that module's configuration files*.
	- Modules can either be loaded from the local filesystem, or a remote source.
- Terraform modules are similar to the concepts of libraries or packages found in other programming languages.
- **Module best practices**:
	- Name the module `terraform-<PROVIDER>-<NAME>`.
	- Start writing the configuration with modules in mind.
	- Use local modules to organize and encapsulate your code.
	- Use the public Terraform Registry to find useful modules.
- *A module that is called by another configuration is sometimes referred to as a "child module" of that configuration*.
- Using modules:
	- ![[attachments/Pasted image 20230114184711.png]]
	- We can also use absolute path to the child module.
	- Here `dev-webserver` is the logical name of the module. 
- *Requirements for publishing and sharing the modules* on **Terraform public module registry**:
	- Module repositories must use this three-part name format, `terraform-<PROVIDER>-<NAME>`.
	- The registry uses tags to identify module versions. 
		- Release tag names must be for the format `x.y.z`, and can optionally be prefixed with a `v`.
	- **The module must be on GitHub** and must be a *public repo*.
	- The module must adhere to the standard module structure.

### How Modules work?
- When using a new module for the *first time*, we must run either **`terraform init` or `terraform get` to install the module**. 
- When we run these commands, Terraform will **install any new modules in the `.terraform/modules` directory** within our configuration's working directory.

> [!important] For **local modules**, Terraform will create a **symlink** to the module's directory. Because of this, **any changes to local modules will be effective immediately**, without having to reinitialize or re-run `terraform get`.

### Using Modules
- Referring to a private registry we use the format: `HOSTNAME/ORGANISATION-NAMESPACE/NAME/PROVIDER`.
	- `app.terraform.io/example_corp/vpc/aws`

#### Using Modules from Terraform Registry
- The **`source` argument is required** when we use a Terraform module.
- The **`version` argument is NOT required** but it is *highly recommended* when using a Terraform module.
- *Terraform treats other arguments in the module blocks as input variables for the module*.
	- Using input variables with modules is similar to using variables in any Terraform configuration. 
	- A *common pattern is to identify which module arguments we may want to change in the future, and create matching variables in your configuration's `variables.tf` file with sensible default values*. 

```hcl title:"Using modules from terraform registry" fold
module "vpc" {
	source  = "terraform-aws-module"
	version = "3.14.0"
	# other input variables for the module
	subnet_id = var.subnet_id # passing variable as argument to the module
}

variable "subnet_id" {
	type     = string
	default  = "123"
}
```

#### Using local modules
![[attachments/Pasted image 20230114185753.png]]

### Output Values from Modules
- **We can reference the output value in modules with the `module.MODULE_NAME.OUTPUT_NAME`** naming convention. 
	- We can reference module outputs in other parts of your configuration. 
- **Terraform will NOT display module outputs by default**. 
	- We *must create a corresponding output in our root module* and set it to the module's output.

```hcl hl:3 title:"Using module outputs" fold
output "vpc_public_subnets" {
  description = "IDs of the VPC's public subnets"
  value       = module.vpc.public_subnets
}
```

### Creating Modules
- It is recommend that every Terraform configuration be created with the assumption that it may be used as a module.
- *Terraform treats any local directory referenced in the `source` argument of a `module` block as a module*.

```hcl title="Standard module structure" fold
.
├── README.md # tell what the module does
├── main.tf
├── variables.tf
├── outputs.tf
```

- In the above example *none of these files are required, or have any special meaning to Terraform when it uses our module*. 
	- We can create a module with a single `.tf` file, or use any other file structure we like.
- There is generally *no `provider` block in module configuration*. 
	- When Terraform processes a module block, it will **inherit the provider from the enclosing configuration** or **specific providers can be passed to modules**.
	- It is *recommend that we DONOT include `provider` blocks in modules*.
- Variables declared in modules that aren't given a default value are required, and so must be set whenever we use the module.
- If we want to expose some outputs to be used in the root module then we should expose them from the child module using the `output` block.