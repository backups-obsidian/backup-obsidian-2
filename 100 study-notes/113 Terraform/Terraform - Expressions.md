---
created: 2023-12-03 18:07
updated: 2023-12-05 09:59
---
---
**Links**: [[113 Terraform Index]]

| Previous: [[Terraform - Functions]] |
|-|

| Next: [[Terraform - Expressions]] |
|-|

---
## Expressions
- Assigning value to a variable based on condition
	- Generating a random password of atleast 8 characters where length of password comes from the user.
	- ![[attachments/Pasted image 20230114193611.png]]
- *Splat expression*: The `splat` expression captures **all objects in a list that share an attribute**. 
	- The special `*` symbol iterates over all of the elements of a given list and returns information based on the shared attribute you define.
	- **Splat expression only interpolates list type**.

```hcl title:"Example of splat operation" fold
output "private_addresses" {
  description = "Private DNS for AWS instances"
  value       = aws_instance.ubuntu[*].private_dns
}
```

### Locals
- Terraform local values (or "locals") **assign a name to an expression or value**. 
- Using locals simplifies our Terraform configuration – since we can reference the local multiple times, we reduce duplication in your code.
- Terraform's *locals DONOT change values during or between Terraform runs such as plan, apply, or destroy*.

```hcl hl:9,17 title:"Setting and using locals" fold
locals {
  name_suffix = "${var.resource_tags["project"]}-${var.resource_tags["environment"]}"
}

module "vpc" {
   source  = "terraform-aws-modules/vpc/aws"
   version = "2.66.0"

   name = "vpc-${local.name_suffix}"
   ## ...
}

module "app_security_group" {
   source  = "terraform-aws-modules/security-group/aws//modules/web"
   version = "3.17.0"

   name        = "web-sg-${local.name_suffix}"
   ## ...
}
```

- Unlike variable values, **local values can use dynamic expressions and resource arguments**.

```hcl title:"Using dynamic expressions with locals" fold
variable "project_name" {
  description = "Name of the project."
  type        = string
  default     = "my-project"
}

variable "environment" {
  description = "Name of the environment."
  type        = string
  default     = "dev"
}

variable "resource_tags" {
   description = "Tags to set for all resources"
   type        = map(string)
   default     = { }
}

locals {
  required_tags = {
    project     = var.project_name,
    environment = var.environment
  }
  tags = merge(var.resource_tags, local.required_tags) # cannot use dynamic expressions in variables. This can be done while assigning it to a resource but this will lead to duplication of code.
  name_suffix = "${var.project_name}-${var.environment}"
}

# now we can use local.tags
```