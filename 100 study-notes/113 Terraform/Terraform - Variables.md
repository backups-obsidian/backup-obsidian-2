---
created: 2023-12-02 13:04
updated: 2023-12-17 18:41
---
---
**Links**: [[113 Terraform Index]]

| Previous: [[Terraform - Commands]] |
|-|

| Next: [[Terraform - Resource Attributes & Dependencies]] |
|-|

---
## Variables
- Variables allow users to more safely customize their infrastructure by *assigning different values to the variables before execution begins, rather than editing configuration files manually*.
- Variable declarations *can appear anywhere in the configuration files*. 
	- However, it is **recommend putting them into a separate file called `variables.tf`** to make it easier for users to understand how they can customize the configuration.

> [!caution]- When it comes to working with variables, the *value that is used in the Terraform configuration will be stored in the state file*, regardless of whether the sensitive argument was set to `true`.

### Defining Variables
- The variable block in *terraform accepts 3 parameters: default, type and description*.
	- `default` parameter in our variable block is also optional. 
		- If we don't have this field and use this variable then terraform will ask us to enter the value interactively when we run `terraform apply`
	- `description` is optional but it is a good practice to describe what the variable is used for.
	- `type` argument is also optional but when used it enforces the type of the variable.
		- The basic types that can be used are `string`, `number`, `boolean` and `any`
		- **If type parameter is not specified then it is set to `any` by default**.
		- Other complex types are `list`, `map`, `tuple`, `object` and `set`.
	- `validation` is used to define validation rules, usually in addition to type constraints.
	- `sensitive` redacts terraform UI/CLI output when the variable is used in configuration.
	- `nullable` specifies if the variable can be null within the module.
		- The default value for `nullable` is `true`.
		- When `nullable` is `true`, `null` is a valid value for the variable, and the module configuration must always account for the possibility of the variable value being `null`. 
		- Setting `nullable` to `false` ensures that the variable value will never be `null` within the module.
 
- To create a variable we use the following syntax

```hcl file:variables.tf fold
variable "variable_name" {
	default = "some_default_value"
}
```

> [!caution]- None of the above mentioned fields are required to create a variable.
> We can create a variable without using any of these too `variable variable_name {}`

- The variable name should be *unique among the variables in the same module*.
- **Variable naming convention**:
	- It CANNOT start with a number.
	- It CANNOT contain spaces or special characters *except underscore*.
	- It CANNOT contain only numbers.
	- It CANNOT contain Terraform reserved keywords like `source`, `version`, `providers`, `count`, `for_each`, `lifecycle`, `depends_on` and `locals`.
- It is recommended to use only lowercase letters, numbers, and underscores in variable names

- We can refer to variables in our configuration with `var.<variable_name>`.
	- **When using variable we need not enclose the values within double quotes otherwise they will be treated as strings**.
		- If we want to interpolate variables in strings we use the following syntax: `"This is a variable: ${var.<variable_name}"`
	- ![[attachments/Pasted image 20230102223131.png]]
- We can **declare an input variable as sensitive by setting `sensitive` to `true`**.
	- Terraform will redact the value from its output when we run a plan, apply, or destroy command.
	- If we want to refer sensitive inputs in outputs then we have to use [[Terraform - Variables#Sensitive Outputs | Sensitive Outputs]]
- Marking variables as sensitive is not sufficient to secure them since they are stored in plain text in the local state file.  
	- Terraform Cloud encrypts all variable values before storing them.
- The Terraform `console` command opens an interactive console that we can use to evaluate expressions in the context of your configuration. 
	- This can be very useful when working with and *troubleshooting variable definitions*.

> [!caution] The `terraform console` command loads our Terraform configuration only when it starts. In case we make *changes to variables* we have to *exit and restart the console* to pick up the most recent changes.

#### Different types of variables
- **Simple variables**: `bool`, `string`, `number`
- `list`:  A sequence of values of the *same type*.
	-  We use indexes to access the value in a list.
	- ![[attachments/Pasted image 20230102223614.png]]
- `map`: A lookup table, matching keys to values, all of the *same type*.
	- ![[attachments/Pasted image 20230102223751.png]]
	- Maps can be made with braces (`{}`) and colons (`:`) or equals signs (`=`): `{ "foo": "bar", "bar": "baz" }` OR `{ foo = "bar", bar = "baz" }`. Quotes may be omitted on keys, unless the key starts with a number, in which case quotes are required.
- `set`: An unordered collection of unique values, *all of the same type*.
	- ![[attachments/Pasted image 20230102224145.png]]
- `objects`: it is a complex data type which can contain other data types
	- ![[attachments/Pasted image 20230102224304.png]]
- `tuple`: the difference between tuple and list is that we have to explicitly specify the types and number of items in the tuple.
	- ![[attachments/Pasted image 20230102224608.png]]
	- Variable to be passed to it should be exactly 3 in number.
	- Adding additional elements or incorrect type will result in an error.

- Using *type constraints* while declaring complex types
	- ![[attachments/Pasted image 20230102223848.png]]
	- ![[attachments/Pasted image 20230102224014.png]]

- `list`, `map`, `set` can contain items of any type by defining the type using `any`
	- Example: `list(any)` - NOT recommended to use type `any`.

### Passing value to variables
- **We define the variables in `variables.tf` file but there are different ways in which we can pass values to these variables**.
- **Command Line Flags**:
	- We can make use of **command line flags** to provide variables for eg: `terraform apply -var "filename=/root/pets.txt"`
	- We can pass as many variables with this method as required by passing the `-var` flag multiple times
- **Environment Variables**: 
	- We can also make use of environment variables using 
		- `export TF_VAR_filename="/root/pets.txt"`
		- `export TF_VAR_content="We love pets!"`
- **Variable Definition File**:
	- We can also use a **variable definition file** if we are dealing with a lot of variables.
	- These files can be named anything but they should either end in `.tfvars` or `.tfvars.json`
	- **Files with the extensions are automatically loaded by terraform: `terraform.tfvars`, `terraform.tfvars.json`, `*.auto.tfvars` and `*.auto.tfvars.json`**.
		- If we use any other file name like `variables.tfvars` then we will have to pass it like a command line flag `terraform apply -var-file variables.tfvars`
- If we use multiple ways to assign the values of variables then terraform follows a **variable definition precedence** to decide which value should be used.
	- ![[attachments/Pasted image 20230103100438.png]]
	- *4 is the highest priority*
	- Priority increases when you go down. Environment variables have the last preference.
	- Variable filename will be assigned `/root/best-pet.txt`
- We can use any of the options above to assign values to variables. 

> [!note]- We use `variables.tf` to define the variables and assign the default values. We use the above ways to pass values to these variables.
> `.tfvars` files should *NEVER be committed to version control* since it can contain sensitive values.

```hcl title:"Example terraform.tfvars" file fold
resource_tags = {
  project     = "project-alpha",
  environment = "dev",
  owner       = "me@example.com"
}

ec2_instance_type = "t3.micro"

instance_count = 3
```

### Variable validation
- We can use variable validation to restrict the possible values for the variables.

```hcl title:"Example variable validation" fold
variable "resource_tags" {
  description = "Tags to set for all resources"
  type        = map(string)
  default     = {
    project     = "my-project",
    environment = "dev"
  }

  validation {
    condition     = length(var.resource_tags["project"]) <= 16 && length(regexall("[^a-zA-Z0-9-]", var.resource_tags["project"])) == 0
    error_message = "The project tag must be no more than 16 characters, and only contain letters, numbers, and hyphens."
  }

  validation {
    condition     = length(var.resource_tags["environment"]) <= 8 && length(regexall("[^a-zA-Z0-9-]", var.resource_tags["environment"])) == 0
    error_message = "The environment tag must be no more than 8 characters, and only contain letters, numbers, and hyphens."
  }
}
```

### Output Variables
- It is *recommended to define all the output variables in `outputs.tf`*.
- Terraform output values lets us export structured data about our resources. 
	- We can use this data to *configure other parts of our infrastructure with automation tools*, or as a data source for another Terraform workspace.
	- Outputs are also how we **expose data from a child module to a root module**.

```hcl title:"syntax for terraform output variables" fold
output "output_variable_name" {
	description = "" # optional
	value = <resource_type>.<resource_name>.<value>
}
```

- Output variable example:
	- ![[attachments/Pasted image 20230103103420.png]]
- **When we run terraform apply we see that the output variable is printed on the screen**.
- Terraform **stores output values in the configuration's state file**. 
	- *In order to see and use these outputs, we need to update the state by applying the configuration using `terraform apply`*.
- We can use `terraform output` command to query all the output variables.
	- We can use it to print a specific output variable using `terraform output <output-variable-name>`
		- When querying a specified output Terraform wraps string outputs in quotes by default. We can use the `-raw` flag for getting machine-readable format.
		- `terraform output -raw <output-variable-name>`

> [!note]- The best use of terraform output variables is when we want to *quickly display details about a resource provision* or to *feed the output to other IaC tools*.
> It isn't meant for feeding output of one resource to another resource. This is done using resource attributes.

- `terraform apply` and `terraform output` will show the output variables but **`terraform plan` WON'T show any output variables**.

#### Sensitive Outputs
> [!important] Sensitive outputs are the only way to refer sensitive inputs.

```hcl title:"example of sensitive outputs referencing sensitive inputs" fold
output "db_connect_string" {
  description = "MySQL database connection string"
  value       = "Server=${aws_db_instance.database.address}; Database=ExampleDB; Uid=${var.db_username}; Pwd=${var.db_password}"
  sensitive   = true
}

```

- We can designate *Terraform outputs as sensitive*. 
	- Terraform will *redact the values of sensitive outputs to avoid accidentally printing them out to the console*.
- Terraform will **redact sensitive outputs** when **planning**, **applying**, or **destroying** your configuration, or when we **query all of our outputs**.
- Terraform will **NOT** redact sensitive outputs in other cases, such as when we
	- *query a specific output by name*
	- *query all of our outputs in JSON format*.
		- `terraform output -json`
	- *when we use outputs from a child module in our root module*.
- Terraform does not redact sensitive output values in the above cases, because it assumes that an automation tool will use the output.

> [!caution] Terraform stores all output values, including those marked as sensitive, as **plain text in our state file**.

```hcl title:"sensitive output values example" fold
output "db_username" {
  description = "Database administrator username"
  value       = aws_db_instance.database.username
  sensitive   = true
}

output "db_password" {
  description = "Database administrator password"
  value       = aws_db_instance.database.password
  sensitive   = true
}
```

#### Output dependencies
```hcl title:"output dependencies using depends_on" fold
output "instance_ip_addr" {
  value       = aws_instance.server.private_ip
  description = "The private IP address of the main server instance."

  depends_on = [
    # Security group rule must be created before this IP address could
    # actually be used, otherwise the services will be unreachable.
    aws_security_group_rule.local_access,
  ]
}
```

#### Using precondition checks
```hcl title:"Using precondition checks" fold
output "api_base_url" {
  value = "https://${aws_instance.example.private_dns}:8433/"

  # The EC2 instance must have an encrypted root volume.
  precondition {
    condition     = data.aws_ebs_volume.example.encrypted
    error_message = "The server's root volume is not encrypted."
  }
}
```