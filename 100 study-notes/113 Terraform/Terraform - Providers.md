---
created: 2023-12-06 19:11
updated: 2023-12-06 19:39
---
---
**Links**: [[113 Terraform Index]]

| Previous: [[Terraform - How Terraform works?]] |
|-|

| Next: [[Terraform - Commands]] |
|-|

---
## Understanding Providers
- There are *3 different kinds of providers*:
	- *Official*: owned and maintained by Hashicorp. Example AWS.
		- They have a *gold badge* against them.
	- *Partner*: owned and managed by third party technology but they have been reviewed and tested by Hashicorp. Example DigitalOcean.
		- The have a *purple tick* against them.
	- *Community*
- **Naming of providers: `hostname/organisation-namespace/type`**:
	- Example: `registry.terraform.io/hashicorp/local`
- We use the `required_providers` under `terraform` to specify a specific version of a provider.

![[Terraform - How Terraform works?#^0b9580]]

- We use the **`provider` block to set provider specific configurations**.
	- Like we setting region for the aws provider.

```hcl title:"Configuring the provider block" fold
provider aws {
	region = "us-east-1"
}
```

- Sometimes we might need to define multiple configurations for the same provider. The primary reason for this is to support multiple regions for a cloud platform
	- For example we might need to create EC2 instances in 2 different regions.
	- We use **`alias` to define multiple configurations of the same provider**.

```hcl title:"Multiple configurations of same provider" fold
provider aws {
	region = "us-east-1"
}

provider aws {
	region = "us-central-1"
	alias  = "central"
}
```

- **A `provider` block without an `alias` argument is the _default_ configuration for that provider**. 
	- *Resources that don't set the `provider` meta-argument will use the default provider configuration that matches the first word of the resource type name*.
		- (For example, an `aws_instance` resource uses the default `aws` provider configuration unless otherwise stated.)
- If we want to set an **alternate provider configuration for a *resource*** we use the following syntax `<PROVIDER NAME>.<ALIAS>`
	- In the above example it would be `aws.central`

- To **select alternate provider configurations for a *child module***, we use its `providers` meta-argument to specify which provider configurations should be mapped to which local provider names inside the module.

```hcl title:"Module provider alternate configuration"
module "aws_vpc" {
  source = "./aws_vpc"
  providers = {
    aws = aws.west
  }
}
```

- There are two main reasons to use the `providers` argument:
	- *Using different default provider configurations for a child module*.
	- Configuring a *module that requires multiple configurations of the same provider*.

```hcl title:"Reason for using the providers block in a child module" fold
# Using different default provider configurations for a child module
module "example" {
  source    = "./example"
  providers = {
    aws = aws.usw2
  }
}

# Configuring a module that requires multiple configurations of the same provider
module "tunnel" {
  source    = "./tunnel"
  providers = {
    aws.src = aws.usw1
    aws.dst = aws.usw2
  }
}
```
