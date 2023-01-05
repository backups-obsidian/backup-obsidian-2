---
created: 2023-01-02 21:55
updated: 2023-01-03 11:03
---
---
**Links**: [[113 Terraform Index]]

---
## Basics
### Providers
- After we write a terraform configuration file the first thing we have to do is to run `terraform init`.
	- *When we run `terraform init` in the directory containing the configuration files, terraform **downloads and installs plugins for the providers** used in the configuration*.
	- It shows the version of the plugin being installed.
- `terraform init` is a safe command which **can be run as many times as required without impacting the architecture**.
- There are 3 different tiers of providers:
	- *Official*: Owned and maintained by hashicorp. This includes the major cloud providers like AWS, Azure and GCP.
	- *Verified*: Owned and maintained by a 3rd party technology company.
	- *Community*: Published and maintained by individual contributors.
- Once we run `terraform init` the plugins are downloaded to a hidden directory `.terraform/plugins` in the directory containing the configuration files.
	- We can inspect this to find all the plugins installed.
- If the registry is not provided then terraform uses the default registry of `registry.terraform.io` to download the plugins.

> [!note]- By default terraform installs the latest version of the provider.

- Terraform will consider any file with `.tf` extension for configuration changes.
- A single configuration file can also have as many configuration blocks as we need.
- We can have multiple providers in the same configuration directory.
	- Each time we add a new provider in the configuration file we have to run `terraform init`

### Input Variables
- We can create variables in a `variables.tf` file.
- To create a variable we use the following syntax

```terraform
# variables.tf
variable "variable_name" {
	default = "some_default_value"
}
```

- The advice is to use argument name for the variable name for the argument that the variable is being used.
- To use variables we use `var.variable_name`
	- **When using variable we need not enclose the values with double quotes**.
	- ![[attachments/Pasted image 20230102223131.png]]

- The variable block in *terraform accepts 3 parameters: default, type and description*.
	- `default` parameter in our variable block is also optional. 
		- If we don't have this field and use this variable then terraform will ask us to enter the value interactively when we run `terraform apply`
	- `description` is optional but it is a good practice to describe what the variable is used for.
	- `type` argument is also optional but when used it enforces the type of the variable.
		- The basic types that can be used are `string`, `number`, `boolean` and `any`
		- **If type parameter is not specified then it is set to `any` by default**.
		- Other complex types are `list`, `map`, `tuple`, `object` and `set`.

#### Complex type examples
- list: **all the elements should be of the same type**
	-  We use indexes to access the value in a list.
	- ![[attachments/Pasted image 20230102223614.png]]
- map
	- ![[attachments/Pasted image 20230102223751.png]]
- set: difference between a set and a list is that *a set cannot have duplicate terms*
	- ![[attachments/Pasted image 20230102224145.png]]
- objects: it is a complex data type which can contain other data types
	- ![[attachments/Pasted image 20230102224304.png]]
- tuple: the difference between tuple and list is that *we can use elements of different variable types*.
	- ![[attachments/Pasted image 20230102224608.png]]
	- Variable to be passed to it should be exactly 3 in number.
	- Adding additional elements or incorrect type will result in an error.

- Using *type constraints* while declaring complex types
	- ![[attachments/Pasted image 20230102223848.png]]
	- ![[attachments/Pasted image 20230102224014.png]]

### Using variables
- We can make use of command line flags to provide variables for eg: `terraform apply -var "filename=/root/pets.txt"`
	- We can pass as many variables with this method as required by passing the `-var` flag multiple times
	- We can also make use of environment variables using 
		- `export TF_VAR_filename="/root/pets.txt"`
		- `export TF_VAR_content="We love pets!"`
- We can also use a variable definition file if we are dealing with a lot of variables.
	- These files can be named anything but they should either end in `.tfvars` or `.tfvars.json`
	- Files with the extensions are automatically loaded by terraform: `terraform.tfvars`, `terraform.tfvars.json`, `*.auto.tfvars` and `*.auto.tfvars.json`
- If we use any other file name like `variables.tfvars` then we will have to pass it like a command line flag `terraform apply -var-file variables.tfvars`
- We can use any of the options above to assign values to variables. 

> [!note]- We use *`variables.tf` to declare the variables* to be used in the terraform files, we can declare their values there or use the above methods to assign values to variables.

- If we use multiple ways to assign the values of variables then terraform follows a **variable definition precedence** to decide which value should be used.
	- ![[attachments/Pasted image 20230103100438.png]]
	- 4 is the highest priority
	- Priority increases when you go down. Environment variables have the last preference.
	- Variable filename will be assigned `/root/best-pet.txt`

### Resource attributes
- **There are bound to be multiple resources that are dependent on each other**.
- Example:

```terraform
resource "local_file" "pet" {
	filename = var.filename
	content = "My favorite pet is ${random_pet.my-pet.id}"
}

resource "random_pet" "my-pet" {
	prefix = var.prefix
	separator = var.separator
	length = var.length
}
```

- In the above example I am using `id` returned by the `random_pet` resource.
	- Every resource might have 0 or more output variables.
	- `random_pet` resource has one output variable called `id`

### Resource Dependencies
- In the above example due to the dependency terraform knows to create `random_pet` resource first and then create the `local_file` resource. 
- When resources are deleted they are deleted in the reverse order. 
	- `local_file` first and then the `random_pet`
- This type of dependency is known as the **implicit dependency**. 
	- We are not specifying which resources are dependent on what, terraform figures it out itself.
 - We can specify **dependency explicitly using `depends_on`** argument.
	 - This is necessary *only when a resource relies on some other resource indirectly* and it *does not make use of a reference expression*.

```terraform
resource "local file" "pet" {
	filename = var.filename
	content = "My favorite pet is Mr.Cat"
	depends_on = [
		random_pet.my-pet
	]
}

resource "random pet" "my-pet" {
	prefix = var.prefix
	separator = var.separator
	length = var.length
}
```

### Output Variables
- Output variables can be used to store the value of an expression in terraform. 
	- ![[attachments/Pasted image 20230103103420.png]]
	- When we run terraform apply we see that the output variable is printed on the screen.
- We can use `terraform output` command to use the value of the output variables.
	- It will print the output of all the terraform output variables in all the configuration files.
	- We can use it to print a specific output variable using `terraform output pet-name`

> [!note]- The best use of terraform output variables is when we want to *quickly display details about a resource provision* or to *feed the output to other IaC tools*.
> It isn't meant for feeding output of one resource to another resource. This is done using resource attributes.